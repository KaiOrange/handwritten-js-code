# 数组扁平化 #

## 描述 ##

定义一个`flatten`函数，接收一个数组作为参数，返回一个新数组，新数组是原数组去扁平化的情况。如:

```JavaScript
let array = [1, [2, [3, 4]]];
console.log(flatten(array)); // 打印 [1, 2, 3, 4]
```

不能使用`array.flat`函数。

## 实现 ##

### concat多层解析版 ###

`[].concat(...arr)`可以解析一层，如下：

```JavaScript
var arr = [1, [2, [3, 4]]];
console.log([].concat(...arr)); // [1, 2, [3, 4]]
```

可以通过循环多层解析：

```JavaScript
function flatten(arr) {
  while (arr.some(item => Array.isArray(item))) {
    arr = [].concat(...arr);
  }
  return arr;
}
```

### reduce递归法 ###

思路：使用reduce对数组的每一项进行遍历，可以使用`concat`解析一层，如果当前项是数组则递归解析。

```JavaScript
function flatten(arr) {
  return arr.reduce(function(prev, next){
    return prev.concat(Array.isArray(next) ? flatten(next) : next)
  }, []);
}
```

### toString ###

思路：`[1, [2, [3, 4]]].toString()`的结果是`1,2,3,4`，可以通过toString后对字符串拆分，然后转化数字即可。

```JavaScript
function flatten(arr) {
  return arr.toString().split(',').map(item => +item);
}
```

> 注意：该方法只能处理纯数字的数组，不能数字和字符串共存的数组，如`[1, '1']`。
