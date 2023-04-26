# instanceOf实现 #

## 描述 ##

由于`instanceof`是JS关键字，所以无法直接模拟，可以写一个`instanceOf`函数，接收2个参数，分别是对象和类。如:

```JavaScript
function Person (name) {
  this.name = name;
}

let p = new Person('Orange');
console.log(instanceOf(p, Person)); // 打印 true
```

## 实现 ##

步骤：

1. 如果第一个参数是非null且非对象或非函数的情况则直接返回false；
2. 循环判断原型是否在`func.prototype`上；
3. 若找到则返回true，没找到则返回false。

```JavaScript
const instanceOf = (obj, func) => {
  if (!(obj && ['object', 'function'].includes(typeof obj))) {
    return false
  }

  let proto = obj;

  while (proto = Object.getPrototypeOf(proto)) {
    if (proto === func.prototype) {
      return true;
    }
  }

  return false;
}
```
