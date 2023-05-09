# Object.create实现 #

## 描述 ##

实现一个`create`函数，功能与`Object.crteate`相同。如:

```JavaScript
let obj = {
  name: 'Orange',
  sayName () {
    console.log(`我是${this.name}`);
  }
}
let man = create(obj);
man.name = 'AAA';
obj.sayName(); // 打印 我是Orange
man.sayName(); // 打印 我是AAA
```

## 三行代码简易实现 ##

```JavaScript
const create = (proto) => {
  const F = function () {};
  F.prototype = proto;
  return new F();
}
```

这三行已经实现了`Object.create`的最基础功能，但是与`Object.create`稍微有些区别：

1. 参数类型检查；
2. `Object.create`拥有第二个参数，也就是属性操作符；
3. 对于`Object.create(null)`返回的对象的`__proto__`属性也是null。

所以完整的实现还应对上面三种做特殊处理。

## 完整实现 ##

步骤：

1. 判断类型不是object或者function抛出异常；
2. 定义构造器函数，其原型指向第一个参数，并创建对象；
3. 如果有第二个参数则使用`Object.defineProperties`定义属性操作符；
4. 如果第一个参数是null，则修复`obj.__proto__`指向null；
5. 返回创建的对象。

```JavaScript
const create = (proto, properties) => {
  // 不是函数或者对象抛出错误
  if (![ 'object', 'function' ].includes(typeof proto)) {
    throw new TypeError(`Object prototype may only be an Object or null: ${proto}`)
  }
  // 创建构造函数
  const F = function () {};
  // 构造函数的原型指向对象
  F.prototype = proto;
  // 创建实例对象
  const obj = new F();
  // 支持第二个参数
  if (properties) {
    Object.defineProperties(obj, properties);
  }
  // 支持空原型
  if (proto === null) {
    obj.__proto__ = null;
  }

  return obj
}
```
