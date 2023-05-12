# sleep延迟执行 #

## 描述 ##

定义一个`sleep(func, delay)`函数，延迟delay毫秒后执行func函数。如:

```JavaScript
(async () => {
  await sleep(() => {
    console.log('1秒后执行'); // 1秒后打印：1秒后执行
  }, 1000);
  await sleep(() => {
    console.log('再过一秒后执行'); // 2秒后打印：再过一秒后执行
  }, 1000);
})();
```

## 实现 ##

思路：Promise + setTimeout封装。

```JavaScript
function sleep(func, delay) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve(func());
    }, delay);
  });
}
```
