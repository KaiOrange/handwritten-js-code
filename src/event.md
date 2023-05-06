# 事件系统 #

## 描述 ##

实现一个`EventEmitter`类，拥有通用事件常用的功能，如监听事件、分发事件、取消监听事件等。如下:

```JavaScript
const event = new EventEmitter();

const listener1 = (data) => {
  console.log('触发事件1', data);
};

const listener12 = (data) => {
  console.log('触发事件2', data);
};

// 监听事件 支持连写
event.on('test', listener1)
  .on('test', listener12);

// 触发事件
event.emit('test', '第1次触发'); // 依次打印 触发事件1 第1次触发 和 触发事件2 第1次触发

// 取消监听
event.off('test', listener1);

event.emit('test', '第2次触发'); // 打印 触发事件2 第2次触发

event.offAll('test'); // 取消所有test事件

event.offAll(); // 取消全部事件

event.emit('test', '第3次触发'); // 不打印
```

## 实现 ##

```JavaScript
class EventEmitter {
  constructor() {
    // 底层使用对象来存储事件 对象键是事件名 值是函数数组
    this._events = {};
  }

  /**
   * 把事件存放在_events对象中
   */
  on(eventName, listener) {
    if (!eventName || !listener) return this;

    const listeners = this._events[eventName] = this._events[eventName] || [];

    // 重复事件不添加
    if (listeners.indexOf(listener) === -1) {
      listeners.push(listener);
    }

    return this;
  };

  /**
   * 把事件从_events值的数组中删除
   */
  off(eventName, listener) {
    const listeners = this._events[eventName];
    if (!listeners) return this;

    const index = listeners.indexOf(listener);

    if (index !== -1) {
        listeners.splice(index, 1)
    }

    return this;
  };

  /**
   * 遍历对应的事件数组 使得每个函数都调用一下
   */
  emit(eventName, ...args) {
    const listeners = this._events[eventName];
    if (!listeners) return this;

    listeners.forEach(listener => {
      listener.apply(this, args || []);
    });
    return this;
  };


  /**
   * 将事件清空
   */
  offAll = function(eventName) {
    if (eventName && this._events[eventName]) {
      this._events[eventName] = []
    } else {
      this._events = {}
    }

    return this;
  };
}
```

## 进阶 ##

上面实现的事件系统已经拥有了一般的通用功能，不过有的事件系统有一个`once`方法，表示只执行一次，如下：

```JavaScript
const event = new EventEmitter();

const listener1 = (data) => {
  console.log('触发事件1', data);
};

// 只执行一次
event.once('test', listener1);

event.emit('test', '第1次触发'); // 打印 触发事件1 第1次触发

event.emit('test', '第2次触发'); // 不打印
```

上一版中`_events`的键是事件名称，值是函数数组，如果要支持`once`就需要添加额外的信息来告诉事件系统是否是执行一次，如果是则需要在触发事件后对该事件删除。所以我们修改一下值，使得值是对象数组，对象中包括函数和是否是执行一次信息。如：

```JavaScript
{
  listener: () => {},
  once: true,  
}
```

```JavaScript
/**
 * 判断是否是合法的监听
 * 如果是函数则合法
 * 如果是对象，那么对象必须要有listener属性的方法
 * 否则不合法
 */
function isValidListener(listener) {
  if (typeof listener === 'function') {
    return true;
  } else if (listener && listener.listener && typeof listener.listener === 'function') {
    return true;
  } else {
    return false;
  }
}

/**
 * 寻找对象中listener是对应函数的索引
 */ 
function indexOf(array, item) {
  const listener = typeof item === 'object'
      ? item.listener
      : item;

  const index = array.findIndex(curItem => curItem && curItem.listener === listener);
  return index;
}

class EventEmitter {
  constructor() {
    // 底层使用对象来存储事件 对象键是事件名 值是对象数组
    this._events = {};
  }

  /**
   * 把事件存放在_events对象中
   */
  on(eventName, listener) {
    if (!eventName || !listener) return this;

    if (!isValidListener(listener)) {
      throw new TypeError('listener must be a function');
    }

    const listeners = this._events[eventName] = this._events[eventName] || [];

    // 重复事件不添加
    if (indexOf(listeners, listener) === -1) {
      // 如果传入的是函数则需要包装成对象
      listeners.push(typeof listener === 'function' ? {
        listener: listener,
        once: false,
      } : listener);
    }

    return this;
  };

  /**
   * once 传入标识once为true
   */
  once(eventName, listener) {
    return this.on(eventName, {
      listener: listener,
      once: true,
    })
  };

  /**
   * 把事件从_events值的数组中删除
   */
  off(eventName, listener) {
    const listeners = this._events[eventName];
    if (!listeners) return this;

    const index = indexOf(listeners, listener);

    if (index !== -1) {
      // 注意这里把对应的对象替换为null 之前是直接删除 
      // 因为emit方法中 会对listeners遍历执行 
      // 如果是once则会删除对应的事件 为了保证遍历过程中个数不变 这里通过设置为null来处理
      listeners.splice(index, 1, null);
    }

    return this;
  };

  /**
   * 遍历对应的事件数组 使得每个函数都调用一下
   */
  emit(eventName, ...args) {
    const listeners = this._events[eventName];
    if (!listeners) return this;

    listeners.forEach(item => {
      // 由于可能有null的存在 所以有值的情况才执行
      if(item) {
        item.listener.apply(this, args || []);
        // 如果执行一次的话则把对应的事件删除
        if (item.once) {
          this.off(eventName, item.listener)
        }
      }
    });
    return this;
  };


  /**
   * 将事件清空
   */
  offAll = function(eventName) {
    if (eventName && this._events[eventName]) {
      this._events[eventName] = []
    } else {
      this._events = {}
    }

    return this;
  };
}
```

