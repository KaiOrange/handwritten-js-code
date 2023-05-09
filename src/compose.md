# 函数组合 #

## 描述 ##

定义一个`memoize`函数，接收多个函数作为参数，返回一个新函数，该函数将会执行参数中从右往左所有的函数。如:

```JavaScript
// 应用：判断一句话有奇数个单词还是偶数个
let splitIntoSpaces = (str) => str.split(" ");
let count = (array) => array.length;
let oddOrEven = (ip) => ip % 2 == 0 ? "even" : "odd";

// 使用组合函数 先拆分字符串 然后计算个数 随后看个数是奇数个还是偶数个
let oddOrEvenWords = compose(oddOrEven,count,splitIntoSpaces);
// 打印 "Even or odd via compose ? odd"
console.log("Even or odd via compose ?",oddOrEvenWords("hello your reading about composition"));
```

## 实现 ##

### 反向迭代法 ###

思路：

1. 返回一个函数，这个函数可以传一个value作为参数。
2. 返回函数的结果是将原有实参的函数反向（因为是从右往左），依次迭代执行的结果。

```JavaScript
const compose = (...fns) => (value) => fns.reverse().reduce((acc, fn) => fn(acc), value);
```

也可以使用reduceRight来反向迭代。

```JavaScript
const compose = (...fns) => (value) => fns.reduceRight((acc, fn) => fn(acc), value);
```

### 逆向调用法 ###

思路与上面一致，使用while循环从后往前执行。

```JavaScript
function compose(...fns) {
  return function() {
      var i = fns.length - 1;
      var result = fns[i].apply(this, arguments);
      while (i--) result = fns[i].call(this, result);
      return result;
  };
};
```
