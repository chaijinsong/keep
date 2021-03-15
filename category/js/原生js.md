<!--
 * @Author: your Name
 * @Date: 2021-03-15 19:49:41
 * @LastEditors: your Name
 * @LastEditTime: 2021-03-15 20:21:35
 * @Description: 
-->

#### 用 ObjectDefineProperty 监听 proxy 对象，能否正常拦截 proxy的修改？
```js
  let person = { name: '张三' }
  const handler = {
    get(obj, prop) {
      console.log('proxy get');
      return '默认值'
    }
  }
  let p = new Proxy(person, handler);

  Object.defineProperty(p, 'name', {
    get() {
      console.log('define get..')
      return 'define p name'
    }
  })

  p.name
  // proxy get


  
  let person = { name: '张三' }
  const handler = {
    get(obj, prop) {
      console.log('proxy get');
      return obj[prop] || '默认值'
    }
  }
  let p = new Proxy(person, handler);

  Object.defineProperty(p, 'name', {
    get() {
      console.log('define get..')
      return 'define p name'
    }
  })

  Object.defineProperty(p, 'haha', {
    get() {
      console.log('define haha...')
      return 'hahahaha'
    }
  })

  p.name
  // proxy get
  // define get..
  person.name
  // define get..
  p.haha
  // define haha...
  person.haha
  // define haha...
```
根据上述的代码结果来看，Object.defineProperty 能否拦截 `person` 的代理对象 `p` 的get，是由`proxy`的handler实现方式决定的，如果在handler中定义的get中访问了 `person['name']`，那么是能够触发 `Object.definePeoperty` 对 `p` 的拦截的。
反之，如果proxy的handler中未访问`person['name']`，那么不会触发define的get

从上面的`p.haha, person.haha`的结果可以推倒出结论，`Object.defineProperty` 拦截一个 代理对象时，其实是拦截了代理对象的源对象。