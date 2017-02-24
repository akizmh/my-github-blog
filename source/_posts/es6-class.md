---
title: class -《ECMAScript 6 入门》学习笔记
date: 2017-02-20 09:41:00
tags: ES6
categories: 技术
---

最近在看阮一峰的[《ECMAScript 6 入门》](http://es6.ruanyifeng.com/)。以下内容为学习笔记和摘要，代码大部分也用了那边。

#### 基本用法
ES5中我们创建一个类一般使用构造函数，ES6里面可以用class关键字实现，是一种语法糖。
ES5 写法：
```js
function Point(x, y) {
  this.x = x;
  this.y = y;
}

Point.prototype.toString = function () {
  return '(' + this.x + ', ' + this.y + ')';
};

```
ES6写法：
```js
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
```
<!-- more -->
其中
```js
Point === Point.prototype.constructor // true
var pt = new Point();
pt.__proto__ === Point.prototype; //true
```
**注意：** 由于类的实例共享同一个原型对象，即：
```js
var p1 = new Point(2,3);
var p2 = new Point(3,2);

p1.__proto__ === p2.__proto__
```
所以可以通过实例的__proto__属性为Class添加方法,并且会影响其他实例。
```js
var p1 = new Point(2,3);
var p2 = new Point(3,2);

p1.__proto__.printName = function () { return 'Oops' };

p1.printName() // "Oops"
p2.printName() // "Oops"

var p3 = new Point(4,2);
p3.printName() // "Oops"
```

#### class的继承
通过extends关键字实现。
```js
class ColorPoint extends Point {}
```
大括号内如果没有代码，相当于拷贝了一个point类。大括号内可以这样扩展。
```js
class ColorPoint extends Point {
  constructor(x, y, color) {
    super(x, y); // 调用父类的constructor(x, y)
    this.color = color;
  }

  toString() {
    return this.color + ' ' + super.toString(); // 调用父类的toString()
  }
}
```
子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类没有自己的this对象，而是继承父类的this对象，然后对其进行加工。如果不调用super方法，子类就得不到this对象。因而在使用super方法之前是不能用this关键字的。

ES6的继承实现实际上是这样的，子类原型作为一个父类的实例，子类的__proto__属性指向父类。因此有:
```js
class A {}
class B extends A {}

B.prototype.__proto__ === A.prototype;  //true
B.__proto__ === A;  //true
```

#### super关键字
除了前面继承中super用作一个方法外，super还可以作为一个对象使用。
1.作为函数调用时，super达标父类的构造函数，且只能运行于子类的构造函数中。
**注意：**super()内部的this指向子类。
2.作为对象使用时，指向父类的原型对象。
```js
class A {
  p() {
    return 2;
  }
}

class B extends A {
  constructor() {
    super();
    console.log(super.p()); // 2
  }
}

let b = new B();
```

#### class的静态方法
类中定义的方法都能被实例继承，但是如果方法名前加入static关键字，表示这个方法是该类的静态方法，无法被实例继承，只能通过类来调用。
```js
class Foo {
  static classMethod() {
    return 'hello';
  }
}

Foo.classMethod() // 'hello'

var foo = new Foo();
foo.classMethod()
// TypeError: foo.classMethod is not a function
```js
但是静态方法可以被子类继承，也可以从super对象上调用。

#### class的静态属性
目前只能通过以下形式来实现：
```js
class Foo {
}

Foo.prop = 1;
Foo.prop // 1
```
ES7中有一个关于静态属性的提案，已得到babel转码支持。

#### new.target属性
new关键字用来创建一个类的实例，new.target在构造函数中使用，返回new命令作用于的那个构造函数。通过这个属性可以用来得知构造函数是如何调用的。
```js
function Person(name) {
  if (new.target !== undefined) {
    this.name = name;
  } else {
    throw new Error('必须使用new生成实例');
  }
}

// 另一种写法
function Person(name) {
  if (new.target === Person) {
    this.name = name;
  } else {
    throw new Error('必须使用new生成实例');
  }
}

var person = new Person('张三'); // 正确
var notAPerson = Person.call(person, '张三');  // 报错
```




