---
title: Object方法整理(2)
date: 2017-02-24 19:53:40
tags: JavaScript
categories: 技术
---

接上篇，本篇介绍Object上的获取型方法以及Oject原型上定义的方法。

## Object.getOwnPropertyDescriptor(obj, prop)
返回指定对象上一个自有属性对应的属性描述符。（自有属性指的是直接赋予该对象的属性，不需要从原型链上进行查找的属性）

- **obj:** 要查看的对象
- **prop:** 一个属性名称，该属性的属性描述符将被返回

如果指定的属性存在于对象上，则返回其属性描述符（property descriptor），否则返回 undefined。 

```js
var o, d;

o = { get foo() { return 17; } };
d = Object.getOwnPropertyDescriptor(o, "foo");
// d is { configurable: true, enumerable: true, get: /*访问器函数*/, set: undefined }

o = { bar: 42 };
d = Object.getOwnPropertyDescriptor(o, "bar");
// d is { configurable: true, enumerable: true, value: 42, writable: true }

o = {};
Object.defineProperty(o, "baz", { value: 8675309});
d = Object.getOwnPropertyDescriptor(o, "baz");
// d is { value: 8675309, writable: false, enumerable: false, configurable: false }
```

---

<!-- more -->

## Object.getOwnPropertyDescriptors(obj)
用来获取一个对象的所有自身属性的描述符。
如果指定对象没有任何自身属性，则返回空对象。 

```js
//浅拷贝一个对象
Object.create(
  Object.getPrototypeOf(obj), 
  Object.getOwnPropertyDescriptors(obj) 
);
```
上述代码与Object.assign()相比， 后者只能拷贝源对象的可枚举的自身属性，无法拷贝属性的特性们，而且访问器属性会被转换成数据属性，也无法拷贝源对象的原型，上述代码可以实现上面说的这些。

---

## Object.getOwnPropertyNames(obj)
返回一个由指定对象的所有**自身属性**的属性名（包括不可枚举属性）组成的数组。数组中枚举属性的顺序与通过 for...in 循环（或 Object.keys）迭代该对象属性时一致。

```js
var arr = ["a", "b", "c"];
console.log(Object.getOwnPropertyNames(arr).sort()); // ["0", "1", "2", "length"]

// 类数组对象
var obj = { 0: "a", 1: "b", 2: "c"};
console.log(Object.getOwnPropertyNames(obj).sort()); // ["0", "1", "2"]

//不可枚举属性
var my_obj = Object.create({}, {
  getFoo: {
    value: function() { return this.foo; },
    enumerable: false
  }
});
my_obj.foo = 1;

console.log(Object.getOwnPropertyNames(my_obj).sort()); // ["foo", "getFoo"]
```
因为Object.getOwnPropertyNames(obj)只会获取自身属性，所以继承来的属性并不会获得。
```js
function ParentClass() {}
ParentClass.prototype.inheritedMethod = function() {};

function ChildClass() {
  this.prop = 5;
  this.method = function() {};
}

ChildClass.prototype = new ParentClass;
ChildClass.prototype.prototypeMethod = function() {};

console.log(
  Object.getOwnPropertyNames(
    new ChildClass()  // ["prop", "method"]
  )
);
```

---

## Object.getOwnPropertySymbols(obj)
返回一个数组，该数组包含了指定对象自身的（非继承的）所有 symbol 属性键。

该方法和 Object.getOwnPropertyNames() 类似，但后者返回的结果只会包含字符串类型的属性键，也就是传统的属性名。

```js
var obj = {};
var a = Symbol("a");
var b = Symbol.for("b");

obj[a] = "localSymbol";
obj[b] = "globalSymbol";

var objectSymbols = Object.getOwnPropertySymbols(obj);

console.log(objectSymbols.length); // 2
console.log(objectSymbols)         // [Symbol(a), Symbol(b)]
console.log(objectSymbols[0])      // Symbol(a)
```

---

## Object.getPrototypeOf(object)
返回指定对象的原型（也就是该对象内部属性[[Prototype]]的值）。
```js
var proto = {};
var obj = Object.create(proto);
Object.getPrototypeOf(obj) === proto; // true
```

---

## Object.entries(obj)
Object.entries方法返回一个包含由给定对象所有**自身可枚举**属性的属性名和属性值组成的 [属性名，属性值] 键值对的数组。

```js
var obj = { foo: "bar", baz: 42 };
console.log(Object.entries(obj)); // [ ['foo', 'bar'], ['baz', 42] ]

// 类数组对象
var obj = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.entries(obj)); // [ ['0', 'a'], ['1', 'b'], ['2', 'c'] ]

// 带有随机（非顺序）排列属性名的类数组对象
var an_obj = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.entries(an_obj)); // [ ['2', 'b'], ['7', 'c'], ['100', 'a'] ]

// getFoo是不可枚举属性
var my_obj = Object.create({}, { getFoo: { value: function() { return this.foo; } } });
my_obj.foo = "bar";
console.log(Object.entries(my_obj)); // [ ['foo', 'bar'] ]

// 非对象参数会被强行视为对象
console.log(Object.entries("foo")); // [ ['0', 'f'], ['1', 'o'], ['2', 'o'] ]
```

---

## Object.keys(obj)
返回一个由给定对象的所有**可枚举自身属性**的属性名组成的数组，数组中属性名的排列顺序和使用for-in循环遍历该对象时返回的顺序一致 (顺序一致不包括数字属性)（**两者的主要区别是 for-in 还会遍历出一个对象从其原型链上继承到的可枚举属性**）。

```js
var arr = ["a", "b", "c"];
alert(Object.keys(arr)); // 弹出"0,1,2"

// 类数组对象
var obj = { 0 : "a", 1 : "b", 2 : "c"};
alert(Object.keys(obj)); // 弹出"0,1,2"

// getFoo是个不可枚举的属性
var my_obj = Object.create({}, { getFoo : { value : function () { return this.foo } } });
my_obj.foo = 1;

alert(Object.keys(my_obj)); // 只弹出foo
```

---

## Object.values(obj)
用法同Object.keys,不过返回的是所有的自身可枚举属性值的数组。目前处于ES7提案中。

---

## Object.prototype.hasOwnProperty()
返回一个布尔值，检测一个对象是否含有特定的自身属性；和 in 运算符不同，该方法会忽略掉那些从原型链上继承到的属性。
> obj.hasOwnProperty(prop)
- **prop:** 要检测的属性  字符串 名称或者 Symbol。

下面的例子演示了 hasOwnProperty 方法对待自身属性和继承属性的区别：
```js
o = new Object();
o.prop = 'exists';
o.hasOwnProperty('prop');             // 返回 true
o.hasOwnProperty('toString');         // 返回 false
o.hasOwnProperty('hasOwnProperty');   // 返回 false
```

```js
function ParentClass() {this.age = 12;}
ParentClass.prototype.inheritedMethod = function() {};

function ChildClass() {
  this.prop = 5;
  this.method = function() {};
}

ChildClass.prototype = new ParentClass;
ChildClass.prototype.prototypeMethod = function() {};

var child = new ChildClass();
for (var name in child) {
    if (child.hasOwnProperty(name)) {
        console.log(name); //prop, method
    }
    else {
        alert(name); // age,prototypeMethod,inheritedMethod
    }
}
```

---

## Object.prototype.isPrototypeOf()
用于测试一个对象是否存在于另一个对象的原型链上。
> prototypeObj.isPrototypeOf(object)
- **object:** 在该对象的原型链上搜寻

```js
function Fee() {
  // . . .
}

function Fi() {
  // . . .
}
Fi.prototype = new Fee();

function Fo() {
  // . . .
}
Fo.prototype = new Fi();

function Fum() {
  // . . .
}
Fum.prototype = new Fo();

var fum = new Fum();

console.log(Fi.prototype.isPrototypeOf(fum));
```

---

## Object.prototype.propertyIsEnumerable()
返回一个布尔值，表明指定的属性名是否是当前对象**可枚举的自身属性**。

> obj.propertyIsEnumerable(prop)

- **prop:** 需要测试的属性名。

```js
//自定义对象和引擎内置对象
var a = ['is enumerable'];

a.propertyIsEnumerable(0);          // 返回 true
a.propertyIsEnumerable('length');   // 返回 false

Math.propertyIsEnumerable('random');   // 返回 false
this.propertyIsEnumerable('Math');     // 返回 false

//自身属性和继承属性
var a = [];
a.propertyIsEnumerable('constructor');         // 返回 false

function firstConstructor() {
  this.property = 'is not enumerable';
}

firstConstructor.prototype.firstMethod = function() {};

function secondConstructor() {
  this.method = function method() { return 'is enumerable'; };
}

secondConstructor.prototype = new firstConstructor();
secondConstructor.prototype.constructor = secondConstructor;

var o = new secondConstructor();
o.arbitraryProperty = 'is enumerable';

o.propertyIsEnumerable('arbitraryProperty');   // 返回 true
o.propertyIsEnumerable('method');              // 返回 true
o.propertyIsEnumerable('property');            // 返回 false

o.property = 'is enumerable';

o.propertyIsEnumerable('property');            // 返回 true

// 这些返回fasle，是因为，在原型链上propertyIsEnumerable不被考虑
// (尽管最后两个在for-in循环中可以被循环出来)。
o.propertyIsEnumerable('prototype');   // 返回 false (根据 JS 1.8.1/FF3.6)
o.propertyIsEnumerable('constructor'); // 返回 false
o.propertyIsEnumerable('firstMethod'); // 返回 false
```

---

 其他还有[Object.prototype.toLocaleString()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toLocaleString)、[Object.prototype.toString()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)和[Object.prototype.valueOf()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf)等方法，很少用到，就不赘述了。