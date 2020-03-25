---
title: vue源码解析
date: 2020-03-23 15:05:55
tags:
    - 学习笔记
---

### Vue源码解析

#### 今日目标
1. 调试vue项目的方式
   - 安装依赖 npm i
   - 安装打包工具 npm i rollup -g
   - 修改pageage.json里面的dev脚本 
     `"dev":"rollup -w -c scripts/config.js --sourcemap --environment TARGET:web-full-dev"`
   - 执行打包
     `npm run dev`
   - 修改example里面的文件引用新生成的vue.js
2. vue是如何启动的
3. vue响应式机制逐行分析


整体启动顺序
vue\src\platforms\web\entry-runtime-with-compiler.js


src\platforms\web\runtime\index.js

```javascript
Vue.prototype.$mount = function (
  el?: string | Element,
  hydrating?: boolean
): Component {
  el = el && inBrowser ? query(el) : undefined
  return mountComponent(this, el, hydrating)
}
```
- mountComponent待研究

src\core\index.js

```initGlobalAPI(Vue)```
- initGlobalAPI
  - set
  - delete
  - nextTick
  - ...

src\core\instance/index.js
构造函数

```javascript
function Vue(options) {
  if (process.env.NODE_ENV !== "production" && !(this instanceof Vue)) {
    warn("Vue is a constructor and should be called with the `new` keyword");
  }
  this._init(options);
}

initMixin(Vue); //实现上面的_init这个初始化方法
stateMixin(Vue); //实现$watch,$get,$set方法
eventsMixin(Vue); //实现$on $once $emit ...
lifecycleMixin(Vue); //实现_update,$forceUpdate,destroy
renderMixin(Vue); //_render $nextTick
```
- initMixin

```javascript
    vm._self = vm
    initLifecycle(vm)
    initEvents(vm)
    initRender(vm)
    callHook(vm, 'beforeCreate')
    initInjections(vm) // resolve injections before data/props
    initState(vm)
    initProvide(vm) // resolve provide after data/props
    callHook(vm, 'created')
```


  - initLifecycle:$parent、$children等
  - initEvents:事件监听初始化
  - initRender:定义$createElement
  - initInjections:获取注入数据并作响应化
  - initState：初始化props、methods、data、computed、watch等
  - initProvide:注入数据处理等

- stateMixin


### 数据响应式

initData
src\core\instance\index.js

```javascript
 proxy()
 observe(data, true /* asRootData */)
```

observe src\core\observer\index.js
```javascript
    ob = new Observer(value)
    return ob
```

Observe

```javascript
 // 判断当前value是不是数组或者Object
    if (Array.isArray(value)) {
      if (hasProto) {
        protoAugment(value, arrayMethods);
      } else {
        copyAugment(value, arrayMethods, arrayKeys);
      }
      this.observeArray(value);
    } else {
      // 是对象
      this.walk(value);
    }
```

defineReactive

```javascript
// 递归响应式处理
  let childOb = !shallow && observe(val);
  // 拦截
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter() {
      const value = getter ? getter.call(obj) : val;
      if (Dep.target) {
        // 加入到dep管理watcher
        dep.depend();
        // 如果存在子对象
        if (childOb) {
          childOb.dep.depend();
          if (Array.isArray(value)) {
            // 如果值是数组，要特殊处理
            dependArray(value);
          }
        }
      }
      return value;
    },
    set: function reactiveSetter(newVal) {
      const value = getter ? getter.call(obj) : val;
      /* eslint-disable no-self-compare */
      if (newVal === value || (newVal !== newVal && value !== value)) {
        return;
      }
      /* eslint-enable no-self-compare */
      if (process.env.NODE_ENV !== "production" && customSetter) {
        customSetter();
      }
      // #7981: for accessor properties without setter
      if (getter && !setter) return;
      if (setter) {
        setter.call(obj, newVal);
      } else {
        val = newVal;
      }
      // 为什么需要递归? 'str' {foo:'str'}
      childOb = !shallow && observe(newVal);
      dep.notify();
    }
  });
```

[JS事件循环机制（event loop）之宏任务、微任务](https://segmentfault.com/a/1190000014940904)


$mount

mountComponent core/instance/lifecycle

```javascript
updateComponent = () => {
      // 首先执行vm._render() 返回VNode
      // 然后VNode作为参数执行update做dom更新
      vm._update(vm._render(), hydrating)
    }

new Watcher(vm, updateComponent, noop, {
  before () {
    if (vm._isMounted && !vm._isDestroyed) {
      callHook(vm, 'beforeUpdate')
    }
  }
}, true /* isRenderWatcher */)
```

_render() src\core\instance\render.js

```javascript
    const { render, _parentVnode } = vm.$options
      vnode = render.call(vm._renderProxy, vm.$createElement)
```

_update src\core\instance\lifecycle.js
```javascript
 if (!prevVnode) {
      // initial render
      // 如果没有老的vnode，说明在初始化
      vm.$el = vm.__patch__(vm.$el, vnode, hydrating, false /* removeOnly */)
    } else {
      // updates
      // 更新周期直接diff，返回新的dom
      vm.$el = vm.__patch__(prevVnode, vnode)
    }
```

__patch__  src\platforms\web\runtime\patch.js

createPatchFunction src\core\vdom\patch.js

[vue页面更新](https://segmentfault.com/a/1190000022133023)