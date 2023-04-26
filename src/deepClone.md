# 深拷贝 #

## 描述 ##

定义一个`deepClone`函数，返回一个对象/数组的深拷贝对象。如:

```JavaScript
let obj = {a: 'a', b: { c: 'c'}};
console.log(deepClone(obj)); // 打印 {a: 'a', b: { c: 'c'}}
```

## 实现 ##

### 递归遍历属性版 ###

思路：

1. 定义一个新对象（或数组）。
2. 遍历对象每一个属性，给新对象添加键和值，如果值是对象则递归值。
3. 返回新对象。

```JavaScript
function deepClone(obj) {
  if (obj === null || typeof obj !== 'object') return obj;
  let newObj = obj instanceof Array ? [] : {};
  for (var key in obj) {
    if (obj.hasOwnProperty(key)) {
      newObj[key] = typeof obj[key] === 'object' ? deepClone(obj[key]) : obj[key];
    }
  }
  return newObj;
}
```

> 注：上述代码可以实现功能，但是如果有循环引用则会栈溢出，可以使用cache来记录值处理，如下。

```JavaScript
function deepClone(obj, cache = new Map()) {
  if (obj === null || typeof obj !== 'object') return obj;
  const cacheObj = cache.get(obj)
  
  if (cacheObj) {
    return cacheObj;
  }
  let newObj = obj instanceof Array ? [] : {};
  cache.set(obj, newObj)
  for (var key in obj) {
    if (obj.hasOwnProperty(key)) {
      newObj[key] = typeof obj[key] === 'object' ? deepClone(obj[key], cache) : obj[key];
    }
  }
  return newObj;
}
```

### JSON转化法 ###

思路：使用JSON对象转化为字符串再转化为对象。

```JavaScript
function deepClone(obj) {
  return JSON.parse(JSON.stringify(obj));
}
```

> 注意：该方法只能转化简单对象，如果对象中含有函数或有循环引用则不能拷贝。
