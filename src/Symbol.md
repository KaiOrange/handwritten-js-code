# Symbol实现 #

## 描述 ##

定义一个`MySymbol`函数，功能类似于Symbol。如:

```JavaScript
let s1 = MySymbol('foo');
let s2 = MySymbol('bar');
console.log(s1 === s2); // 打印 false
// MySymbol.for与MySymbol.keyFor不可枚举
let s3 = MySymbol.for('xxx');
let s4 = MySymbol.for('xxx');
console.log(s3 === s4); // 打印 true
let key1 = MySymbol.keyFor(s3);
console.log(key1); // 打印 xxx
let obj = {
  [s1]: 'symbol',
}
console.log(obj[s1]); // 打印 symbol
```

## 实现 ##

步骤：

1. 定义一个MySymbol函数，如果是new的该函数，直接报错；该函数接受一个参数作为Symbol的描述，如果有值则转化为字符串；
2. MySymbol函数需要创建一个对象并返回，该函数拥有不可修改的属性`__Description__`和`__Name__`分别用来记录描述和名称，对象的toString返回唯一的`__Name__`。
3. MySymbol函数需要加两个不可枚举的静态方法`for`和`keyFor`用来查找symbol和对应的key。

```JavaScript
(function() {
  let root = this;

  // 生成唯一的名称
  let generateName = (function(){
    let postfix = 0;
    return function(descString){
      postfix++;
      return '@@' + descString + '_' + postfix;
    }
  })()

  let MySymbol = function Symbol(description) {
    if (this instanceof MySymbol) throw new TypeError('Symbol is not a constructor');
    let descString = description === undefined ? undefined : String(description);
    let symbol = Object.create({
      toString: function() {
        return this.__Name__;
      },
      valueOf: function() {
        return this;
      }
    });

    Object.defineProperties(symbol, {
      '__Description__': {
        value: descString,
        writable: false,
        enumerable: false,
        configurable: false
      },
      '__Name__': {
        value: generateName(descString),
        writable: false,
        enumerable: false,
        configurable: false
      }
    });

    return symbol;
  }

  let forMap = {};
  
  Object.defineProperties(MySymbol, {
    'for': {
      value: function(description) {
        let descString = description === undefined ? undefined : String(description);
        return forMap[descString] ? forMap[descString] : forMap[descString] = MySymbol(descString);
      },
      writable: true,
      enumerable: false,
      configurable: true
    },
    'keyFor': {
      value: function(symbol) {
        for (var key in forMap) {
          if (forMap[key] === symbol) return key;
        }
      },
      writable: true,
      enumerable: false,
      configurable: true
    }
  });

  root.MySymbol = MySymbol;
})();
```

> 注意:
>  
> 1. `typeof Symbol()`返回的是`symbol`，由于我们无法修改`typeof`操作符，所以这个无法实现；
> 2. `Symbol('foo').toString()`返回的是字符串的`Symbol(foo)`，为了保证在对象上生成唯一的key我们使用了`generateName`来生成唯一的名称，而不是`Symbol(foo)`。
> 3. 通过`Symbol('xxx')`创建的Symbol对象，无法通过`Symbol.for('xxx')`找到，我们这里与之相符。
