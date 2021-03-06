# Vue的一些原理的实现

## Vue2.0响应式原理
[code](./vue2.0-observe/index.js)
### 数据劫持
- 如何避免重复劫持
  - 给把obsever 设置为 __ob__ 属性, 通过此属性判断是否已经劫持过
- 如何劫持数组
  - 改造原数组方法, 监听数组类型数据时, 替换其原型
  - 提供Vue.$set(o, key, val)方法
  - 当判断到为嵌套数组时递归劫持和收集依赖
### 依赖收集  
一个vm实例只有一个watcher实例  new Watcher(vm, render, cb).  

### 流程:  
1. watcher实例化的时候会触发render函数, Dep.target = watcher
2. render函数中被劫持的数据会触发闭包 dep.depend()
3. dep.subs.push(Dep.target) dep 成功收集到该数据的watcher
4. Dep.target.deps.push(dep) watcher 成功收集该数据的 dep
5. 当数据setter触发时,  dep.notify()执行, 就可以遍历执行watcher.cb, 从而推动视图更新 

### 存在的问题
- 如何避免重复依赖  
  - 给 dep 设置id, 当watcher判断已经有这个数据的dep时, 则忽略

- 计算属性
  - 无cb参数的watcher实例的值:  
    vm.computed\[key\] = new Watcher(vm, () => a + b).get()
  - 两个计算属性相互依赖时, 怎么处理?

## Vue3.0响应式原理
[code](./vue3.0-reactivity/index.js)

### Proxy中的track和trigger
返回一个ReactiveObj, 内置track和trigger方法可以收集和触发ReactiveEffect
- reactive
- ref

### 基础方法`effect`的实现
#### 思考
1. 在effect中修改依赖对象, 如何不会陷入死循环?
   1. effect的回调函数中并没有再使用effect包裹修改依赖对象的语句, 所以不会重新触发


#### code
```javascript
// 1. 传入回调函数(内部引用ReactiveObj), 返回一个runner函数
// 2. runner函数里面做了以下事情: 
//    1. 抛出程序当前在执行的ReactiveEffect使它可以被track)
const runner = effect(fn, options)

// 2. 运行runner, 成功触发track, 把ReactiveEffect收集
runner()
```