# trim实现 #

## 描述 ##

修改string原型，实现一个`trim2`函数，功能与string的trim一样。如:

```JavaScript
console.log('    123123    '.trim2()); // 打印 123123
```

## 实现 ##

### 匹配前后空格替换为空字符串 ###

```JavaScript
String.prototype.trim2 = function() {
  return this.replace(/^\s+|\s+$/g, '');
}
```

### 匹配中间部分的内容替换为中间内容 ###

```JavaScript
String.prototype.trim2 = function() {
  return this.replace(/^\s+(.*?)\s+$/, '$1');
}
```

同样的也可以是trimLeft和trimRight:

```JavaScript
String.prototype.trimLeft2 = function() {
  return this.replace(/^\s+/g, '');
}
String.prototype.trimRight2 = function() {
  return this.replace(/\s+$/g, '');
}
```
