# 偏函数 #

## 描述 ##

定义一个`partial`函数，接收一个函数和其他数据作为参数，返回一个新函数，如果其他数据中包含占位符则该参数可以之后调用的时候设置。如:

```JavaScript
// 这里使用symbol来做占位符 也可以undefined 或者其他对象做占位符
const _ = Symbol();
// 使用偏函数 函数1秒后执行
// 使用_来表示后续需要传入的参数
// 这里setTimeout第一个参数由调用时候决定 第二个参数固定永远是1000 表示1秒后调用
let delayTenMsPartial = partial(setTimeout, _, 1000);
delayTenMsPartial(() => console.log("Do X. . .  task"));
delayTenMsPartial(() => console.log("Do Y . . . . task"));
```

## 实现 ##

偏函数的实现主要难点是参数的处理，在原有参数中如果有占位符则使用返回函数中的参数替换，最好把返回参数中多余的参数追加到参数列表中并执行函数。

```JavaScript
// 这里使用symbol来做占位符 也可以undefined 或者其他对象做占位符
const _ = Symbol();

function partial(fn, ...args) {
  return function() {
    let position = 0;
    for(var i = 0; i < args.length; i++) {
        args[i] = args[i] === _ ? arguments[position++] : args[i]
    }
    while(position < arguments.length) args.push(arguments[position++]);
    return fn.apply(this, args);
  };
};
```
