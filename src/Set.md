# Set实现 #

## 描述 ##

定义一个`MySet`类，功能类似于Set。如:

```JavaScript
let set = new MySet([1, 1, 2, 3, 3]);

set.add(NaN);
console.log(set.size); // 4
set.delete(NaN); // 3
console.log(set.size); // 3
console.log([...set.keys()]); // [1, 2, 3]
console.log([...set.values()]); // [1, 2, 3]
console.log([...set.entries()]); // [[1, 1], [2, 2], [3, 3]]
set.clear();
console.log(set.size); // 0
```

## 实现 ##

思路：

1. 内部使用数组作为容器来存放数据，在调用add的时候，如果数据已经存在于数组中则不添加，否则添加。
2. 考虑到`NaN`也可以存放在`Set`中需要对`NaN`做特殊处理，可以使用存放的时候Symbol来代替，取值的时候如果是该Symbol返回NaN。
3. values和keys使用相同的方法，返回迭代器对象。

```JavaScript
(function(global) {
  // encodeVal 和 decodeVal是为了处理NaN的问题
  let NaNSymbol = Symbol('NaN');
  let encodeVal = function(value) {
    return value !== value ? NaNSymbol : value;
  }

  let decodeVal = function(value) {
    return (value === NaNSymbol) ? NaN : value;
  }

  let makeIterator = function(array, iterator) {
    let nextIndex = 0;
    let obj = {
      next: function() {
        return nextIndex < array.length ? { 
          value: iterator(array[nextIndex++]), 
          done: false 
        } : { 
          value: undefined, 
          done: true
        };
      }
    };

    obj[Symbol.iterator] = function() {
      return obj;
    }

    return obj;
  }

  class MySet {
    static length = 0;

    constructor(data) {
      this._values = [];
      this.size = 0;

      for(let item of data || []) {
        this.add(item);
      }

      this.keys = this.values;
    }

    add(value) {
      value = encodeVal(value);
      if (this._values.indexOf(value) == -1) {
        this._values.push(value);
        ++this.size;
      }
      return this;
    }

    has(value) {
      return (this._values.indexOf(encodeVal(value)) !== -1);
    }

    delete(value) {
      var idx = this._values.indexOf(encodeVal(value));
      if (idx == -1) return false;
      this._values.splice(idx, 1);
      --this.size;
      return true;
    }

    clear() {
      this._values = [];
      this.size = 0;
    }

    forEach(callbackFn, thisArg) {
      thisArg = thisArg || global;
      for(let item of this._values) {
         callbackFn.call(thisArg, item, item, this);
      }
    }

    values() {
      return makeIterator(this._values, function(value) { return decodeVal(value); });
    }

    entries() {
      return makeIterator(this._values, function(value) { return [decodeVal(value), decodeVal(value)]; });
    }

    [Symbol.iterator]() {
      return this.values();
    }

  }

  global.MySet = MySet;

})(this);
```
