# forOf循环实现 #

## 描述 ##

我们无法直接定义forOf语法，但可以定义一个`forOf`函数，接收一个可迭代对象和一个回调作为参数，用法模拟forOf循环。如:

```JavaScript
let array = [1, 2, 3, 4];
forOf(array, (item) => {
  console.log(item); // 依次打印 1, 2, 3, 4
});
```

## 实现 ##

思路：

1. forOf循环的是可迭代对象，可迭代对象必须要有`[Symbol.iterator]`方法，该方法执行后的对象有一个`next()`方法，每次调用`next()`方法会返回形如`{value: xxx, done：true}`这样的数据，具体细节可以看[这篇文章](https://www.kai666666.com/2021/06/24/for-of%E5%BE%AA%E7%8E%AF%E7%9A%84%E4%BD%BF%E7%94%A8/);
2. 循环调用`next()`函数，直到`done`为`ture`表示已经结束，未结束的每次循环调用回调函数。

```JavaScript
function forOf(obj, cb) {
  if (typeof obj[Symbol.iterator] !== "function")
      throw new TypeError(result + " is not iterable");
  if (typeof cb !== "function") throw new TypeError("cb must be callable");

  let iterable = obj[Symbol.iterator]();
  let result = iterable.next();
  while (!result.done) {
    cb(result.value);
    result = iterable.next();
  }
}
```
