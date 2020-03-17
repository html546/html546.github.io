---
title: 'vue实现原理'
date: 2020-03-05 18:00:54
tags:
    - 学习笔记
categories:
    - vue
---

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<!-- <script src="js/vue.js" type="text/javascript" charset="utf-8"></script> -->
	</head>
	<body>
		<div id="app">
			<input type="text" v-model="msg" name="">
			<h1>{{msg}}</h1>
			<!-- v-html绑定的是h1对象上的innerHTML -->
			<h1 v-html="msg"></h1>
			<button @click="changeEvent">修改msg</button>
		</div>
		<script>
			class Vue {
				constructor(options) {
					// 通过选择获取根对象
					this.$el = document.querySelector(options.el);

					// 数据创造之前
					if (typeof options.beforeCreate == 'function') {
						options.beforeCreate.bind(this)();
					}

					this.$options = options;



					// this.$watchEvent[key] = [event,event,event];
					// 设置一个对象专门保存修改更新的事件
					this.$watchEvent = {};
					// 代理options的data数据
					this.proxyData();
					// 劫持事件
					this.observe()
					// 数据创造之后
					if (typeof options.created == 'function') {
						options.created.bind(this)();
					}
					// 挂载前
					if (typeof options.beforeMount == 'function') {
						options.beforeMount.bind(this)();
					}
					// 把view的数据和事件进行绑定
					this.compile(this.$el)
					// 挂载后
					if (typeof options.mounted == 'function') {
						options.mounted.bind(this)();
					}
				}
				observe() {
					let that = this;
					for (let key in this.$options.data) {
						// 获取此处value保存
						let value = this.$options.data[key];
						Object.defineProperty(this.$options.data, key, {
							configurable: false,
							enumerable: true,
							// value:'定义值',
							// writeable:true, false 定义是否可以修改
							get() {
								// 获取this[key]时，即返回options的data[key]
								// console.log('触发获取内容事件');
								return value;
							},
							set(val) {
								value = val;
								// 触发以这个key值的更新事件
								if (that.$watchEvent[key]) {
									that.$watchEvent[key].forEach((item, index) => {
										item.update();
									})
								}
							}
						});
					}
				}
				proxyData() {
					// 循环通过set,get方法来实现代理数据
					for (let key in this.$options.data) {
						// console.log(key);
						Object.defineProperty(this, key, {
							configurable: false,
							enumerable: true,
							// value:'定义值',
							// writeable:true, false 定义是否可以修改
							get() {
								// 获取this[key]时，即返回options的data[key]
								return this.$options.data[key]
							},
							set(val) {
								this.$options.data[key] = val;
							}
						});
					}
				}
				compile(cNode) {
					// console.log([this.$el]);
					cNode.childNodes.forEach((node, index) => {
						if (node.nodeType == 1) {
							// 元素类型,getAttribute,hasAttribute
							if (node.hasAttribute('v-html')) {
								let vmKey = node.getAttribute('v-html').trim();
								if (this.hasOwnProperty(vmKey)) {
									node.innerHTML = this[vmKey];
									let watcher = new Watch(this, vmKey, node, 'innerHTML');
									if (this.$watchEvent[vmKey]) {
										this.$watchEvent[vmKey].push(watcher);
									} else {
										this.$watchEvent[vmKey] = [];
										this.$watchEvent[vmKey].push(watcher)
									}
									// 删除节点事件
									node.removeAttribute('v-html');
								}
							}
							// 判断是否有v-model属性
							if (node.hasAttribute('v-model')) {
								let vmKey = node.getAttribute('v-model').trim();
								if (this.hasOwnProperty(vmKey)) {
									node.value = this[vmKey];
									let watcher = new Watch(this, vmKey, node, 'value');
									if (this.$watchEvent[vmKey]) {
										this.$watchEvent[vmKey].push(watcher);
									} else {
										this.$watchEvent[vmKey] = [];
										this.$watchEvent[vmKey].push(watcher)
									}
								}
								node.addEventListener('input', (event) => {
									this[vmKey] = node.value;
								})
								// 删除属性
								node.removeAttribute('v-model')
							}
							// 判断是否有绑定@click事件
							if (node.hasAttribute('@click')) {
								let vmKey = node.getAttribute('@click').trim();
								node.addEventListener('click', (event) => {
									this.eventFn = this.$options.methods[vmKey].bind(this);
									this.eventFn(event);
								})
							}
							if (node.childNodes.length > 0) {
								this.compile(node)
							}
						}
						if (node.nodeType == 3) {
							// 文本类型
							let reg = /\{\{(.*?)\}\}/g;
							let text = node.textContent;
							node.textContent = text.replace(reg, (match, vmKey) => {
								/* console.log(match);
								console.log(vmKey); */
								// 去除空格
								vmKey = vmKey.trim();
								if (this.hasOwnProperty(vmKey)) {
									let watcher = new Watch(this, vmKey, node, 'textContent');
									if (this.$watchEvent[vmKey]) {
										this.$watchEvent[vmKey].push(watcher);
									} else {
										this.$watchEvent[vmKey] = [];
										this.$watchEvent[vmKey].push(watcher)
									}
								}
								return this[vmKey];

							});

						}
					})
				}
			}

			class Watch {
				constructor(vm, key, node, attr, nodeType) {
					// vm即实例化的app对象
					this.vm = vm;
					// key即绑定的vm触发的属性
					this.key = key;
					// node即,此vm[key]数据绑定的HTML节点
					this.node = node;
					// property,vm数据所绑定的HTML节点的属性名称
					this.attr = attr;
				}
				update() {
					// 更新之前
					if (typeof options.beforeUpdate == 'function') {
						options.beforeUpdate.bind(this)();
					}
					this.node[this.attr] = this.vm[this.key];
					// 数据更新之后
					if (typeof options.updated == 'function') {
						options.updated.bind(this)();
					}
				}
			}
		</script>
		<script>
			let options = {
				el: "#app",
				data: {
					msg: 'hello laochen',
					username: '小明'
				},
				methods: {
					changeEvent() {
						this.msg = 'hello vue'
					}
				},
				beforeMount() {
					console.log('挂载前');
				},
				mounted(){
					console.log('挂载后');
				}
			}
			let app = new Vue(options);
			console.log(app);
		</script>
	</body>
</html>

```