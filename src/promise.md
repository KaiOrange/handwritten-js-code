# Promise实现 #

## 描述 ##

实现一个`MyPromise`对象，功能与`Promise`相同。如:

```JavaScript
new MyPromise((resolve, reject) => {
  resolve(123);
}).then(res => {
  console.log(res);
});
```

## 实现 ##

```JavaScript
/**
 * resolvePromise 用来处理then中返回Promise的情况 具体做法
 * 1. 如果promise2 跟 x 相同则出现了循环引用；
 * 2. 当x是对象或者函数的时候 判断是否是thenable，如果有then函数则执行then然后递归比对执行的结果；如果没有then则直接* *   resolve,当调用失败的时候reject;
 * 3. 如果x是普通基础类型则resolve.
 */
function resolvePromise(promise2, x, resolve, reject) {
  if (promise2 === x) {
    return reject(new TypeError('circular reference'));
  }
  if (x !== null && (typeof x === 'object' || typeof x === 'function')) {
    let called;
    try {
      let then = x.then;
      if (typeof then === 'function') {
        then.call(x, y => {
          if (called) return;
          called = true;
          resolvePromise(promise2, y, resolve, reject);
        }, r => {
          if (called) return;
          called = true;
          reject(r)
        })
      } else {
        resolve(x);
      }
    } catch (e) {
      if (called) return;
      called = true;
      reject(e);
    }

  } else {
    resolve(x);
  }
}

class MyPromise {
  /**
   * 构造函数的步骤：
   * 1. 构造函数成功的数据保存在value中，失败的数据保存在reason中；
   * 2. status有三种情况pending resolved rejected；
   * 3. 初始化resolve和reject函数，如果状态是pending，则修改状态并保存值，最后循环遍历回调函数；
   * 4. 执行构造函数传递的函数，传入resolve和reject，若执行失败则执行reject。
   */
  constructor(executor) {
    this.value = undefined;
    this.reason = undefined;
    this.status = 'pending'; // pending resolved rejected
    this.onFulfilledCallbacks = [];
    this.onRejectedCallbacks = [];
    const resolve = (value) => {
      if (this.status === 'pending') {
        this.value = value;
        this.status = 'resolved';
        this.onFulfilledCallbacks.forEach(fn => fn());
      }
    }
    const reject = (reason) => {
      if (this.status === 'pending') {
        this.reason = reason;
        this.status = 'rejected';
        this.onRejectedCallbacks.forEach(fn => fn());
      }
    }
    try {
      executor(resolve, reject);
    } catch (e) {
      reject(e);
    }
  }

  /**
   * then步骤：
   * 1.对于没有传onFulfilled, onRejected的时候，初始化对应的默认值；
   * 2.定义一个promise2并返回，promise2根据对应的状态做不同的处理；
   * 3.如果是resolve状态，则返回的Promise中延迟调用onFulfilled(this.value)函数，并处理Promise结果。
   * 4.如果是reject状态，则返回的Promise中延迟调用onRejected(this.reason)函数，并处理Promise结果。
   * 5.如果是pending状态，则返回的Promise中只需把对应的callback压入对应的栈中即可，具体调用时机会在resolve或reject调用后执行
   */
  then(onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : y => y;
    onRejected = typeof onRejected === 'function' ? onRejected : errr => {
      throw err;
    };
    let promise2; 
    if (this.status === 'resolved') {
      promise2 = new MyPromise((resolve, reject) => {
        setTimeout(() => { //异步处理
          try {
            let x = onFulfilled(this.value);
            resolvePromise(promise2, x, resolve, reject);
          } catch (e) {
            reject(e);
          }
        }, 0);
      })
    }
    if (this.status === 'rejected') {
      promise2 = new MyPromise((resolve, reject) => {
        setTimeout(() => { //异步处理
          try {
            let x = onRejected(this.reason);
            resolvePromise(promise2, x, resolve, reject);
          } catch (e) {
            reject(e);
          }
        }, 0);
      })
    }
    if (this.status === 'pending') {
      promise2 = new MyPromise((resolve, reject) => {
        this.onFulfilledCallbacks.push(() => {
          setTimeout(() => { //异步处理
            try {
              let x = onFulfilled(this.value);
              resolvePromise(promise2, x, resolve, reject);
            } catch (e) {
              reject(e);
            }
          }, 0);
        })
        this.onRejectedCallbacks.push(() => {
          setTimeout(() => { //异步处理
            try {
              let x = onRejected(this.reason);
              resolvePromise(promise2, x, resolve, reject);
            } catch (e) {
              reject(e);
            }
          }, 0);
        })
      })
    }
    return promise2;
  }

  /**
   * catch就是只穿onRejected的then
   */
  catch(onRejected){
    return this.then(null, onRejected);
  }

  /**
   * finally 就是不管成功还是失败的时候都会执行
   * 可以利用then来模拟，无论成功还是失败都会走到then当中
   */
  finally(callback) {
    return this.then(
      value  => MyPromise.resolve(callback()).then(() => value),
      reason => MyPromise.resolve(callback()).then(() => { throw reason })
    );
  };
}

MyPromise.resolve = function (val) {
    return new MyPromise((resolve, reject) => resolve(val));
}

MyPromise.reject = function (val) {
    return new MyPromise((resolve, reject) => reject(val));
}

MyPromise.all = function (promiseArrs) {
  return new MyPromise((resolve, reject) => {
    let arr = [];
    let index = 0;

    for (let i = 0; i < promiseArrs.length; i++) {
      promiseArrs[i].then((data) => {
        arr[i] = data;
        index++;
        if (index === promiseArrs.length) {
          resolve(arr);
        } 
      }, reject)
    }
  })
}

MyPromise.race = function (promises) {
  return new MyPromise((resolve, reject) => {
    for (let i = 0; i < promises.length; i++) {
      promises[i].then(resolve, reject);
    }
  })
}

MyPromise.deferred = MyPromise.defer = function () { //这是promise的语法糖
  let dfd = {};
  dfd.promise = new MyPromise((resolve,reject)=>{
    dfd.resolve = resolve;
    dfd.reject = reject;
  })
  return dfd;
}
```
