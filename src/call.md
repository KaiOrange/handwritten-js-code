# 函数call与apply实现 #

## 描述 ##

在函数原型上添加call2和apply2方法，功能与call和apply相同。如:

```JavaScript
const sayName = function (age, hobby) {
  console.log(`我是${this.name}，今年${age}，爱好${hobby}`);
}

sayName.call2({
  name: 'Orange',
}, 18, '写代码'); // 打印 我是Orange，今年18，爱好写代码

sayName.apply2({
  name: 'AAA',
}, [20, '打篮球']); // 打印 我是AAA，今年20，爱好打篮球
```

## 实现 ##

思路：

1. 将上下文转化为对象，如果上下文为空的时候默认绑定再window上。
2. 在上下文对象上添加fn函数指向自己，因为`context.fn()`调用的时候this会指向context。
3. 调用函数`context.fn()`，记录并在最后返回返回值。
4. 删除`context.fn`属性。

```JavaScript
Function.prototype.call2 = function (context, ...args) {
  context = Object(context) || window;
  context.fn = this;

  const result = context.fn(...args);

  delete context.fn
  return result;
}

Function.prototype.apply2 = function (context, arr) {
  context = Object(context) || window;
  context.fn = this;

  const result = context.fn(...arr);

  delete context.fn
  return result;
}
```

> 注意：上述fn可以使用Symbol来代替，使用Symbol会更好一点，这里为了简单就使用fn。另外`context.fn(...arr)`使用了ES5的构，如果题目规定不能使用ES6的语法，对于参数的展开执行我们可以使用`eval()`函数，这里不再赘述。
