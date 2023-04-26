# 防抖 #

## 描述 ##

多次触发函数，n毫秒后执行，如果期间触发那么顺延当前时间后的n毫秒。

## 例子 ##

实现一个debounce函数。

```JavaScript
function log() {
  console.log('执行了！');
}

let debounceLog = debounce(log, 1000);
debounceLog();
debounceLog(); // 1秒后打印
```

## 实现 ##

### 简单实现 ###

思路：返回一个函数，该函数首先清空上一次调用的计时器，然后用计时器在`wait`毫秒后执行函数。

```JavaScript
function debounce(func, wait) {
  let timeout;

  return function (...args) {
    let context = this;

    if(timeout) clearTimeout(timeout);

    timeout = setTimeout(function(){
        func.apply(context, args);
    }, wait);
  }
}
```

### 带有取消和立即执行操作的debounce ###

```JavaScript
function debounce(func, wait, immediate) {
  let timeout, result;

  let debounced = function (...args) {
    let context = this;

    if (timeout) clearTimeout(timeout);
    if (immediate) {
      // 立即执行的时候 通过timeout来标记是否执行 如果timeout为null则执行
      var callNow = !timeout;
      timeout = setTimeout(function(){
        timeout = null;
      }, wait);
      if (callNow) result = func.apply(context, args);
    } else {
      timeout = setTimeout(function(){
        func.apply(context, args);
      }, wait); 
    }
    return result;
  };

  debounced.cancel = function() {
    clearTimeout(timeout);
    timeout = null;
  };

  return debounced;
}
```