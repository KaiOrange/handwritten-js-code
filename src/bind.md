# 函数bind实现 #

## 描述 ##

在函数原型上添加bind2方法，功能与bind相同。如:

```JavaScript
const sayName = function () {
  console.log(this.name);
}

const sayNameBind = sayName.bind2({
  name: 'Orange',
});
sayNameBind();// 打印 Orange
```

## 实现 ##

### 简单实现 ###

思路：返回一个函数，当这个函数执行的时候，使用bind2第一个参数作为上下文来执行，同时合并bind2剩余参数和返回函数中剩余参数。

```JavaScript
Function.prototype.bind2 = function (context, ...args) {
  const self = this;

  return function () {
    return self.apply(context, [...args, ...arguments]);
  }
}
```

> 上述代码存在问题，如果使用new创建的对象，仍然会绑定context实际上new的优先级比bind高。更完善的代码如下：

```JavaScript
Function.prototype.bind2 = function (context, ...args) {
  // bind对非函数会抛出异常
  if (typeof this !== "function") {
    throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
  }

  const self = this;
  const Parent = function () {};
  const Son = function () {
    return self.apply(this instanceof Parent ? this : context, [...args, ...arguments]);
  }

  Parent.prototype = this.prototype;
  Son.prototype = new Parent();
  return Son;
}
```
