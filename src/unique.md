# 数组去重 #

## 描述 ##

定义一个`unique`函数，接收一个数组作为参数，返回一个新数组，新数组是原数组去重后的情况。如:

```JavaScript
let array = [1, 1, '1', 2, '2', 3];
console.log(unique(array)); // 打印 [1, '1', 2, '2', 3] 类型不同认为内容不同
```

## 实现 ##

### ES6 Set急速版 ###

思路：Set存储的是不重复的值，可以把Set转化为数组就可以了。

```JavaScript
function unique(array) {
  return [...new Set(array)];
}
```

### filter过滤版 ###

思路：使用filter过滤掉indexOf不等于当前索引的值。

```JavaScript
function unique(array) {
  let res = array.filter(function(item, index, array){
    return array.indexOf(item) === index;
  });
  return res;
}
```

> 注意：由于indexOf是通过全等来比较的，所以该方法不能对`NaN`去重。
