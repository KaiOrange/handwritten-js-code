# 节流 #

## 描述 ##

多次触发函数，n毫秒内只有一次执行，如果n毫秒内多次触发那么只执行第一次。

## 例子 ##

实现一个throttle函数。

```JavaScript
function log() {
  console.log('执行了！');
}

let throttleLog = throttle(log, 1000);
throttleLog(); // 1秒内只执行一次
throttleLog(); 
```

## 实现 ##

### 定时器版本 ###

思路：返回一个函数，如果没有计时器的时候则用计时器在`wait`毫秒后执行函数，并清空计时器id。

```JavaScript
function throttle(func, wait) {
  let timeout;

  return function(...args) {
    context = this;
    if (!timeout) {
      timeout = setTimeout(function() {
        timeout = null;
        func.apply(context, args);
      }, wait);
    }
  }
}
```

### 时间戳版本 ###

思路：返回一个函数，如果当前时间比上次执行的时间多于`wait`毫秒则执行函数，并把当前执行时间记录为上次执行时间。

```JavaScript
function throttle(func, wait) {
  let previous = 0;

  return function(...args) {
    let now = +new Date();
    if (now - previous > wait) {
        func.apply(this, args);
        previous = now;
    }
  }
}
```
