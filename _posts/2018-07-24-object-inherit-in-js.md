---
layout: post
title: "JavaScript中的对象和继承"
subtitle: "大家都知道，JavaScript没有类（es5）这个概念，跟传统的面向对象语言比如Java也有所不同，JavaScript有其独特的对象和继承的方式。"
author: "Volare"
header-style: text
tags:
  - JavaScript
---

## 对象

### 工厂模式

```javascript
function createPerson(name) {
  var o =new Object()
  o.name = name
  o.sayName = function () {
    console.log(this.name)
  }
  return o
}
var person1 = createPerson('wang')
```

工厂模式有一个很大的缺点，就是不知道一个对象的类型，所以后面就有了构造函数。

### 构造函数

利用构造函数重写上面的例子

```javascript
function Person(name) {
  this.name = name
  this.sayName = function () {
    console.log(this.name)
  }
}
var person1 = new Person('wang')
```

要创建一个Person的新实例，就要用到new关键字，使用new的过程实际上是这样的：

1. 创建一个空的新对象
2. 将构造函数的作用域赋值给新对象（即将this指向该新对象）
3. 执行代码
4. 返回新对象

可以检测实例的类型

```javascript
console.log(person1 instanceof Object) // true
console.log(person1 instanceof Person) // true
```

不过这种构造函数存在一个问题，就是当你每new一个实例的时候，构造函数里面的每个方法都会重新创建一遍。

**不同实例上的同名函数是不想等的。**

### 组合构造函数模式和原型模式

这种方式是ECMAScript中使用最广泛、认同度最高的一种创建自定义类型的方法。

```javascript
function Person(name) {
  this.name = name
}
Person.prototype.sayName = function() {
  console.log(this.name)
}
var person1 = new Person('wang')
```



## 继承

### 原型链继承

```javascript
function SuperType() {
  this.property = true
}
SuperType.prototype.getSuperValue = function () {
  return this.property
}
function SubType() {
  this.subProperty = false
}
// 继承SuperType
SubType.prototype = new SuperType()
SubType.prototype.getSubValue = function () {
  return this.subproperty
}
var instance = new SubType()
console.log(instance.getSuperValue()) // true
console.log(instance.getSubValue())  // false
```

这种继承有两个问题

1. 包含引用类型值的原型属性会被所有实例共享。比如上面的代码，如果SuperType的property是引用类型，那么我们对insance1.property的修改能够通过instance2.property反映出来。
2. 创建子类实例的时候，不能向超类型的构造函数中传递参数。

鉴于以上的问题，实践中很少单独使用原型链。

### 构造函数继承

```javascript
function SuperType(name) {
  this.name = name
}
function SubType(name) {
  // 继承
  SuperType.call(this,name)
}
var instance1 = new SubType('wang')
```

构造函数解决原型链继承的两个问题，然而，方法都在构造函数中定义，就无法达到函数复用的目的了。而且，父类型原型中定义的方法，子类型是"看不到的"。因此构造函数继承也是很少单独使用。

### 组合继承

指的是将原型链和构造函数组合到一块。

```javascript
function SuperType(name) {
  this.name = name
}
SuperType.prototype.sayName = function () {
  console.log(this.name)
}
function SubType(name,age) {
  // 继承属性
  SuperType.call(this, name)
  this.age = age
}
// 继承方法
SubType.prototype = new SuperType()
SubType.prototype.constructor = SubType
SubType.prototype.sayAge = function () {
  console.log(this.age)
}
var instance = new SubType('wang', 20)
insatnce.sayName() // wang
instance.sayAge() // 20
```

组合继承避免了原型链和构造函数继承的缺陷，融合了它们的优点，成为JavaScript中最常用的继承模式，而且，instanceof和isPrototypeOf()也能够用于识别基于组合继承创建的对象。

缺点：调用了两次父类构造函数，生成了两份实例 。

### 寄生组合式继承

```javascript
function SuperType(name) {
  this.name = name
}
SuperType.prototype.sayName = function () {
  console.log(this.name)
}
function SubType(name,age) {
  // 继承属性
  SuperType.call(this, name)
  this.age = age
}
function inheritPrototype(subType, superType) {
    var prototype = object(superType.prototype)
    prototype.constructor = subType
    subType.prototype = prototype
}
inheritPrototype(SubType, SuperType)
SubType.prototype.sayAge = function () {
  console.log(this.age)
}
var instance = new SubType('wang', 20)
insatnce.sayName() // wang
instance.sayAge() // 20
```

寄生组合式继承是引用类型最理想的继承模式。



**注：参考书籍《JavaScript高级程序设计第3版》**