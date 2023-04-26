# new实现 #

## 描述 ##

模拟实现new操作，由于new是关键字，可以使用newFn函数来代替。如:

```JavaScript
function Person (name, age) {
  this.name = name;
  this.age = age;
}

let man = newFn(Person, 'Orange', 18);
// 类似于 let man = new Person('Orange', 18);
console.log(`我是${man.name}, 今年${man.age}岁`); // 打印 我是Orange, 今年18岁
```

## 实现 ##

思路：

1. 首先创建一个`fn.prototype`为原型的对象；
2. 执行函数；
3. 如果返回值时一个对象则返回返回值，否则返回第一步创建的对象。

```JavaScript
function newFn(fn, ...args) {
  const obj = Object.create(fn.prototype);
  const result = fn.apply(obj, args);
  return typeof result === 'object' ? result : obj;
}
```

