---
title: Object方法整理(1)
date: 2017-02-23 11:13:32
tags: JavaScript
categories: 技术
---

最近看ES6关于对象的扩展和新关键字class的时候，发现之前对于JavaScript里面的方法有点模糊，于是重新学习了一下Object相关方法。主要参考的是
[MDN网站](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object)。

根据功能不同，我对object的方法做了分类，如下：
- **操作型:** assign, create, defineProperty, defineProperties, freeze, preventExtensions, seal, setPrototypeOf
- **判断型:** is, isExtensible, isFrozen, isSealed
- **获取型:** getOwnPropertyDescriptor, getOwnPropertyDescriptors, getOwnPropertyNames, getOwnPropertySymbols, getPrototypeOf, entries, keys, values

由于Object方法较大，篇幅较长，分成两部分。本篇主要讲操作型和判断型。

<!-- more -->

---


## Object.assign(target, ...sources)
把任意个源对象的可枚举属性拷贝给目标对象,并返回目标对象。是浅拷贝。
- target: 目标对象
- sources: 源对象

```js
var o1 = { a: 1 };
var o2 = { b: 2 };
var o3 = { c: 3 };

var obj = Object.assign(o1, o2, o3);
console.log(obj); // { a: 1, b: 2, c: 3 }
console.log(o1);  // { a: 1, b: 2, c: 3 },注意目标对象自身也会改变。
```

---

## Object.create(proto, [ propertiesObject ])
创建一个拥有指定原型和若干个指定属性的对象。
- proto: 一个对象，作为新创建对象的原型。或者为 null。
- propertiesObject: 可选。该参数对象是一组属性与值，该对象的属性名称将是新创建的对象的属性名称，值是属性描述符。注意：该参数对象不能是 undefined，另外只有该对象中自身拥有的可枚举的属性才有效，也就是说该对象的原型链上属性是无效的。

**使用Object.create实现类式继承:**
```js
//Shape - superclass
function Shape() {
  this.x = 0;
  this.y = 0;
}

Shape.prototype.move = function(x, y) {
    this.x += x;
    this.y += y;
    console.info("Shape moved.");
};

// Rectangle - subclass
function Rectangle() {
  Shape.call(this); //call super constructor.
}

//相当于Ranctangle.prototype = new Shape()
Rectangle.prototype = Object.create(Shape.prototype);

var rect = new Rectangle();

rect instanceof Rectangle //true.
rect instanceof Shape //true.

rect.move(1, 1); //Outputs, "Shape moved."
```

**使用Object.create 的 propertyObject 参数:**
```js
var o;

// 创建一个原型为null的空对象
o = Object.create(null);

o = {};
// 以字面量方式创建的空对象就相当于:
o = Object.create(Object.prototype);

o = Object.create(Object.prototype, {
  // foo会成为所创建对象的数据属性
  foo: { writable:true, configurable:true, value: "hello" },
  // bar会成为所创建对象的访问器属性
  bar: {
    configurable: false,
    get: function() { return 10 },
    set: function(value) { console.log("Setting `o.bar` to", value) }
}})


function Constructor(){}
o = new Constructor();
// 上面的一句就相当于:
o = Object.create(Constructor.prototype);
// 当然,如果在Constructor函数中有一些初始化代码,Object.create不能执行那些代码


// 创建一个以另一个空对象为原型,且拥有一个属性p的对象
o = Object.create({}, { p: { value: 42 } })

// 省略了的属性特性默认为false,所以属性p是不可写,不可枚举,不可配置的:
o.p = 24
o.p
//42

o.q = 12
for (var prop in o) {
   console.log(prop)
}
//"q"

delete o.p
//false

//创建一个可写的,可枚举的,可配置的属性p
o2 = Object.create({}, { p: { value: 42, writable: true, enumerable: true, configurable: true } });
```

---

## object.defineProperty(obj, prop, descriptor)
直接在一个对象上定义一个新属性，或者修改一个已经存在的属性， 并返回这个对象。
- obj: 需要定义属性的对象。
- prop: 需定义或修改的属性的名字。
- descriptor: 将被定义或修改的属性的描述符。

对象的属性描述主要有两种，**数据描述符**和**存取描述符**。
数据描述符是一个拥有可写或不可写值的属性。存取描述符是由一对 getter-setter 函数功能来描述的属性。描述符必须是两种形式之一；不能同时是两者。

数据描述符和存取描述符均可以有的键值：
- configurable：
  当且仅当该属性的 configurable 为 true 时，该属性描述符才能够被改变，也能够被删除。**默认为false**。
- enumerable：
  当且仅当该属性的 enumerable 为 true 时，该属性才能够出现在对象的枚举属性中。**默认为false**。

数据描述符同时具有以下可选键值：

- value：
该属性对应的值。可以是任何有效的 JavaScript 值（数值，对象，函数等）。**默认为 undefined**。
- writable：
当且仅当该属性的 writable 为 true 时，该属性才能被赋值运算符改变。**默认为 false**。

存取描述符同时具有以下可选键值：
- get
一个给属性提供 getter 的方法，如果没有 getter 则为 undefined。该方法返回值被用作属性值。**默认为 undefined**。
- set
一个给属性提供 setter 的方法，如果没有 setter 则为 undefined。该方法将接受唯一参数，并将该参数的新值分配给该属性。**默认为 undefined**。

```js
var o = {};

o.a = 1;
// 等同于 :
Object.defineProperty(o, "a", {
  value : 1,
  writable : true,
  configurable : true,
  enumerable : true
});


// 另一方面，
Object.defineProperty(o, "a", { value : 1 });
// 等同于 :
Object.defineProperty(o, "a", {
  value : 1,
  writable : false,
  configurable : false,
  enumerable : false
});
```

创建属性:
```js
//数据描述符
var o = {};
Object.defineProperty(o, "a", {
    value : 37,
    writable : true,
    enumerable : true,
    configurable : true
});

//存取描述符
var bValue;
Object.defineProperty(o, "b", {
    get : function(){ return bValue; },
    set : function(newValue){ bValue = newValue; },
    enumerable : true,
    configurable : true
});
o.b = 38;
```
修改属性:
如果描述符的 configurable 特性为false（即该特性为non-configurable），那么除了 writable 外，其他特性都不能被修改，并且数据和存取描述符也不能相互切换。如果一个属性的 configurable 为 false，则其 writable 特性也只能修改为 false。
```js
//writable
var o = {}; // 创建一个新对象

Object.defineProperty(o, "a", {value : 37,writable : false });

console.log(o.a); // 打印 37
o.a = 25; // 没有错误抛出（在严格模式下会抛出，即使之前已经有相同的值）
console.log(o.a); // 打印 37， 赋值不起作用。

//enumerable
var o = {};
Object.defineProperty(o, "a", { value : 1, enumerable:true });
Object.defineProperty(o, "b", { value : 2, enumerable:false });
Object.defineProperty(o, "c", { value : 3 }); // enumerable defaults to false
o.d = 4; // 如果使用直接赋值的方式创建对象的属性，则这个属性的enumerable为true

for (var i in o) {    
  console.log(i);  
}
// 打印 'a' 和 'd' (in undefined order)

Object.keys(o); // ["a", "d"]

o.propertyIsEnumerable('a'); // true
o.propertyIsEnumerable('b'); // false
o.propertyIsEnumerable('c'); // false

//configurable
var o = {};
Object.defineProperty(o, "a", {
    get : function(){return 1;}, 
    configurable : false
});

// throws a TypeError
Object.defineProperty(o, "a", {configurable : true}); 
// throws a TypeError
Object.defineProperty(o, "a", {enumerable : true}); 
// throws a TypeError (set was undefined previously) 
Object.defineProperty(o, "a", {set : function(){}}); 
// throws a TypeError (even though the new get does exactly the same thing) 
Object.defineProperty(o, "a", {get : function(){return 1;}});
// throws a TypeError
Object.defineProperty(o, "a", {value : 12});

console.log(o.a); // logs 1
delete o.a; // Nothing happens
console.log(o.a); // logs 1
```
获取一个对象某个属性描述符的话使用Object.getOwnPropertyDescriptor(obj, prop)。

---

## Object.defineProperties(obj, props)
在一个对象上添加或修改一个或者多个自有属性，并返回该对象。
用法同Object.defineProperty。
- obj
将要被添加属性或修改属性的对象。
- props
该对象的一个或多个键值对定义了将要为对象添加或修改的属性的具体配置。

---

## Object.freeze(obj)
冻结一个对象。具体指不能添加新的属性，不能修改已有属性的值和属性的可枚举性、可配置性、可写性，也不能删除已有属性。该方法返回被冻结的对象。
任何尝试修改该对象的操作都会失败，可能是静默失败，也可能会抛出异常（严格模式中）。
- obj 要被冻结的对象。

Object.freeze是一个浅冻结，如果被冻结对象的某个属性也是一个对象，还是可以修改该属性的，除非冻结该属性。
```js
obj = {
  internal : {}
};

Object.freeze(obj);
obj.internal.a = "aValue";

obj.internal.a // "aValue"

// 想让一个对象变的完全冻结,冻结所有对象中的对象,我们可以使用下面的函数.

function deepFreeze (o) {
  var prop, propKey;
  Object.freeze(o); // 首先冻结第一层对象.
  for (propKey in o) {
    prop = o[propKey];
    if (!o.hasOwnProperty(propKey) || !(typeof prop === "object") || Object.isFrozen(prop)) {
      // 跳过原型链上的属性和已冻结的对象.
      continue;
    }

    deepFreeze(prop); //递归调用.
  }
}

obj2 = {
  internal : {}
};

deepFreeze(obj2);
obj2.internal.a = "anotherValue";
obj2.internal.a; // undefined
```

判断一个对象是否冻结，用Object.isFrozen(obj)。

---

## Object.preventExtensions(obj)
让一个对象变的不可扩展，也就是永远不能再添加新的属性,并返回改对象。但是修改删除还是可以的。
- obj 
将要变得不可扩展的对象

如果对象obj的某个属性也是一个对象，即使obj不可扩展了，obj下面是对象的属性仍是能扩展的，除非该属性也变为不可扩展。
Object.preventExtensions 只能阻止一个对象不能再添加新的自身属性，仍然可以为该对象的原型添加属性。

```js
var nonExtensible = { removable: true };
Object.preventExtensions(nonExtensible);
Object.defineProperty(nonExtensible, "new", { value: 8675309 }); // 抛出TypeError异常
```

判断一个对象能否扩展，用Object.isExtensible(obj)。

---

## Object.seal(obj)
让一个对象密封，并返回被密封后的对象。密封对象不能添加新的属性，不能删除已有属性，以及不能修改已有属性的可枚举性、可配置性、可写性，但可以修改已有属性的值。
- obj
将要被密封的对象

如果对象obj的某个属性也是一个对象，即使obj密封了，obj下面是对象的属性仍没有别密封,这是一个浅密封。

```js
var obj = {
    prop: function () {},
    foo: "bar"
  };
var o = Object.seal(obj);
// 仍然可以修改密封对象上的属性的值.
obj.foo = "quux";

// 但你不能把一个数据属性重定义成访问器属性.
Object.defineProperty(obj, "foo", { get: function() { return "g"; } }); // 抛出TypeError异常

// 现在,任何属性值以外的修改操作都会失败.
obj.quaxxor = "the friendly duck"; // 静默失败,新属性没有成功添加
delete obj.foo; // 静默失败,属性没有删除成功
```

---

## Object.setPrototypeOf(obj, prototype)
设置一个指定的对象的原型 ( 例如,内置的 [[Prototype]]属性）到另一个对象或  null。

- obj
要设置其原型的对象。
- prototype
该对象的新原型(一个对象 或 null)。

Object.setPrototypeOf方法的作用与__proto__相同，用来设置一个对象的prototype对象。它是ES6正式推荐的设置原型对象的方法。因为__proto__并不是一个标准化的属性，虽然各大浏览器都提供了支持。

```js
//格式
Object.setPrototypeOf(obj, proto);
//等同于
function (obj, proto) {
  obj.__proto__ = proto;
  return obj;
}
//例子
let proto = {};
let obj = { x: 10 };
Object.setPrototypeOf(obj, proto);

proto.y = 20;
proto.z = 40;

obj.x // 10
obj.y // 20
obj.z // 40
```
要获取一个prototype对象，可以用Object.getPrototypeOf(obj)。

---

## Object.is(value1, value2)
用来判断两个值是否是同一个值。

与传统\==运算符相比，Object.is()比较的时候不会做类型转换；
虽然\==\=运算符也不会做类型转换，但是会把 -0 和 +0 这两个数值视为相同的，还会把两个 NaN 看成是不相等的。
 Object.is()和===的区别仅在于-0和+0，以及两个NaN的判断上。

```js
Object.is(+0, -0); //false
Object.is(NaN, NaN); //true
```

---

## Object.isExtensible(obj)
判断一个对象是否是可扩展的（是否可以在它上面添加新的属性）。

冻结和密封的对象必定不可扩展。
```js
// 新对象默认是可扩展的.
var empty = {};
Object.isExtensible(empty); // === true

// ...可以变的不可扩展.
Object.preventExtensions(empty);
Object.isExtensible(empty); // === false

// 密封对象是不可扩展的.
var sealed = Object.seal({});
Object.isExtensible(sealed); // === false

// 冻结对象也是不可扩展.
var frozen = Object.freeze({});
Object.isExtensible(frozen); // === false
```

---

## Object.isSealed(obj)
判断一个对象是否是密封的（sealed）。

密封对象是指那些不可扩展 的，且所有自身属性都不可配置的（non-configurable）且属性不可删除的对象（其可以是可写的）。冻结对象必然是密封的。

```js
// 新建的对象默认不是密封的.
var empty = {};
Object.isSealed(empty); // === false

// 如果你把一个空对象变的不可扩展,则它同时也会变成个密封对象.
Object.preventExtensions(empty);
Object.isSealed(empty); // === true

// 但如果这个对象不是空对象,则它不会变成密封对象,因为密封对象的所有自身属性必须是不可配置的.
var hasProp = { fee: "fie foe fum" };
Object.preventExtensions(hasProp);
Object.isSealed(hasProp); // === false

// 如果把这个属性变的不可配置,则这个对象也就成了密封对象.
Object.defineProperty(hasProp, "fee", { configurable: false });
Object.isSealed(hasProp); // === true

// 最简单的方法来生成一个密封对象,当然是使用Object.seal.
var sealed = {};
Object.seal(sealed);
Object.isSealed(sealed); // === true

// 一个密封对象同时也是不可扩展的.
Object.isExtensible(sealed); // === false

// 一个密封对象也可以是一个冻结对象,但不是必须的.
Object.isFrozen(sealed); // === true ，所有的属性都是不可写的
var s2 = Object.seal({ p: 3 });
Object.isFrozen(s2); // === false， 属性"p"可写

var s3 = Object.seal({ get p() { return 0; } });
Object.isFrozen(s3); // === true ，访问器属性不考虑可写不可写,只考虑是否可配置
```

---

## Object.isFrozen(obj)
判断一个对象是否被冻结（frozen）。

一个对象是冻结的（frozen）是指它不可扩展，所有属性都是不可配置的（non-configurable），且所有数据属性（data properties）都是不可写的（non-writable）。数据属性是指那些没有取值器（getter）或赋值器（setter）的属性。

```js
// 一个对象默认是可扩展的,所以它也是非冻结的.
assert(Object.isFrozen({}) === false);

// 一个不可扩展的空对象同时也是一个冻结对象.
var vacuouslyFrozen = Object.preventExtensions({});
assert(Object.isFrozen(vacuouslyFrozen) === true);

// 一个非空对象默认也是非冻结的.
var oneProp = { p: 42 };
assert(Object.isFrozen(oneProp) === false);

// 让这个对象变的不可扩展,并不意味着这个对象变成了冻结对象,
// 因为p属性仍然是可以配置的(而且可写的).
Object.preventExtensions(oneProp);
assert(Object.isFrozen(oneProp) === false);

// ...如果删除了这个属性,则它会成为一个冻结对象.
delete oneProp.p;
assert(Object.isFrozen(oneProp) === true);

// 一个不可扩展的对象,拥有一个不可写但可配置的属性,则它仍然是非冻结的.
var nonWritable = { e: "plep" };
Object.preventExtensions(nonWritable);
Object.defineProperty(nonWritable, "e", { writable: false }); // 变得不可写
assert(Object.isFrozen(nonWritable) === false);

// 把这个属性改为不可配置,会让这个对象成为冻结对象.
Object.defineProperty(nonWritable, "e", { configurable: false }); // 变得不可配置
assert(Object.isFrozen(nonWritable) === true);

// 一个不可扩展的对象,拥有一个不可配置但可写的属性,则它仍然是非冻结的.
var nonConfigurable = { release: "the kraken!" };
Object.preventExtensions(nonConfigurable);
Object.defineProperty(nonConfigurable, "release", { configurable: false });
assert(Object.isFrozen(nonConfigurable) === false);

// 把这个属性改为不可写,会让这个对象成为冻结对象.
Object.defineProperty(nonConfigurable, "release", { writable: false });
assert(Object.isFrozen(nonConfigurable) === true);

// 一个不可扩展的对象,值拥有一个访问器属性,则它仍然是非冻结的.
var accessor = { get food() { return "yum"; } };
Object.preventExtensions(accessor);
assert(Object.isFrozen(accessor) === false);

// ...但把这个属性改为不可配置,会让这个对象成为冻结对象.
Object.defineProperty(accessor, "food", { configurable: false });
assert(Object.isFrozen(accessor) === true);

// 使用Object.freeze是冻结一个对象最方便的方法.
var frozen = { 1: 81 };
assert(Object.isFrozen(frozen) === false);
Object.freeze(frozen);
assert(Object.isFrozen(frozen) === true);

// 一个冻结对象也是一个密封对象.
assert(Object.isSealed(frozen) === true);

// 当然,更是一个不可扩展的对象.
assert(Object.isExtensible(frozen) === false);
```