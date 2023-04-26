# 函数记忆化 #

## 描述 ##

定义一个`memoize`函数，接收一个函数作为参数，返回该函数的记忆化函数。如:

```JavaScript
let add = function(a, b) {
  console.log('调用了');
  return a + b;
}

let memoizedAdd = memoize(add)

memoizedAdd(1, 2) // 返回3 打印 调用了 
memoizedAdd(1, 2) // 返回3 不打印 调用了
```

## 实现 ##

思路：

1. 定义一个缓存对象，并返回一个记忆化的函数。
2. 记忆化函数通过参数来生成一个key，根据key判断是否在缓存中执行过，如果执行过则直接返回缓存，没有执行过则执行并加入缓存。

```JavaScript
function memoize(f) {
  const cache = {};
  return function(...args) {
    const key = args.length + args.join(",");
    if (key in cache) {
      return cache[key]
    } else {
      return cache[key] = f.apply(this, arguments)
    }
  }
}
```
