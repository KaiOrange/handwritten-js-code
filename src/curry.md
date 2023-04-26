# 函数柯里化 #

## 描述 ##

定义一个`curry`函数，将一个多参函数转化为可嵌套的一元函数。如:

```JavaScript
function add(a, b, c) {
    return a + b + c;
}

add(1, 2, 3) // 6

// 假设有一个 curry 函数可以做到柯里化
var addCurry = curry(add);
addCurry(1)(2)(3) // 6
```

## 实现 ##

思路：返回一个函数，判断返回函数的实参是否小于原函数的形参，如果小于则返回函数合并参数的情况，否则返回原函数的执行。

```JavaScript
function curry(fn) {
  return function curriedFn(...args){
    if(args.length < fn.length){
      return function() {
        return curriedFn.apply(this, [...args, ...arguments]);
      };
    }

    return fn.apply(null, args);
  };
}
```
