# sleep链式打印 #

## 描述 ##

定义`Paint`类，实现如下效果，当调用eat、work的时候分别打印字符串`eat`和`work`，当调用`sleep(time)`会沉睡`time`秒然后打印`sleep`，也就是`time`秒后才会执行后面的操作。

```JavaScript
let paint = new Paint();
paint.eat().work().sleep(3).eat().work(); // 打印 eat work 三秒后再打印sleep eat work
```

## 实现 ##

### 队列 + next 调用 ###

思路：把操作执行的函数放在队列中，每次函数执行的时候只是把操作函数添加到队列中，当每次函数执行完调用next执行队列头部的操作函数。由于首次执行的时候需要等后面的执行函数添加进来所以构造函数中可以使用宏任务启动next的调用。

```JavaScript
class Paint {

  constructor() {
    this.fns = [];
    setTimeout(() => {
      this.next();
    });
  }

  next() {
    let fn = this.fns.shift();
    fn && fn();
  }

  eat() {
    this.fns.push(() => {
      console.log('eat');
      this.next();
    });

    return this;
  }

  work() {
    this.fns.push(() => {
      console.log('work');
      this.next();
    });
    return this;
  }

  sleep(t) {
    this.fns.push(() => {
      setTimeout(() => {
        console.log('sleep');
        this.next();
      }, t * 1000);
    })

    return this;
  }
}

let paint = new Paint()
paint.eat().work().sleep(3).eat().work();
```

### Primise链式调用 ###

思路：Promise resolve后可以进入下一个then函数处理，可以使用Promise链式调用来实现。该方法也是本文推荐的一种方法。

```JavaScript
class Paint {

  constructor() {
    this.chain = Promise.resolve();
  }

  eat() {
    this.chain = this.chain.then(()=>{
      console.log('eat');
    });

    return this;
  }

  work() {
    this.chain = this.chain.then(()=>{
      console.log('work');
    });
    return this;
  }

  sleep(t) {
    this.chain = this.chain.then(()=>{
      return new Promise(resolve => {
        setTimeout(() => {
          console.log('sleep');
          resolve();
        }, t * 1000);
      });
    });

    return this;
  }
}

let paint = new Paint();
paint.eat().work().sleep(3).eat().work();
```
