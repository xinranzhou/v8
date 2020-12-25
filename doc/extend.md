<!--
 * @Author: your name
 * @Date: 2020-12-21 09:19:03
 * @LastEditTime: 2020-12-21 10:02:03
 * @LastEditors: Please set LastEditors
 * @Description: 描述对象是是如何实现继承
 * @FilePath: /v8/doc/extend.md
-->

### javascript是基于原型继承的语言

与其他如java c# c++等语言在面向对象编程中是基于类的设计，通过组合class、friend、protected、private、interface等大量关键字即可实现类的继承。与之不同的javascript是根据原型的方式即可实现对象的继承。

### javascript是如何实现继承

#### 什么是继承
继承可简单描述为一个对象通过某种方式可以使用另一个对象的属性和方法。如对象obj1能调用obj2的getName方法。
```
var obj1 = {
    name: 'xxx'
}
var obj2 = {
    getName: function () { console.log(this.name)}
}
```
那么该如何实现呢？
此时就要用到对象的隐藏属性__proto__实现obj1继承obj2，
```
obj1.__proto__ = obj2
```
这样的话就能调用obj1.getName方法了。

所以javascript中实现继承的根基就是__proto__,简称对象的原型，其指向内存中的某个对象，该对象就没称之为原型对象。
因为javascript中查找属性是沿着__proto__查找的，所以上例中，，当调用obj1.getName()时候会先在obj1的属性中查找，没有找到该属性值，沿着对象的原型__proto__查找到原型对象，此时的原型对象就是obj2，该对象下面有getName属性，所以能够执行该方法。即实现了obj1调用了obj2的方法。

#### 构造函数是如何创建对象
上述中，我们知道javascript实现继承的本质是对象的原型__proto__，该属性是一个隐藏属性，只是在浏览器中开了一个口子让我们可以访问到这个属性，实际业务中我们是不应该使用该属性来实现继承的。
通常开发中经常用到的创建对象的方法
```
function Person(name, age) {
    this.name = name
    this.age = age
}
const per = new Person('xiaoming', 12)
```
那么new 关键字其实质又是什么呢？其实际在js底层悄悄的实现来下面三部曲：
```
cosnt obj = {}
obj.__proto__ = Person.prototype
Person.apply(obj, ['xiaoming', 12])
```
`obj.__proto__ = Person.prototype`这一步实现了生成的对象的原型指向了函数的原型对象。这也就实现了创建对象继承函数对象的原型对象。即Person.prototype,如果`Person.prototype.getAge = function() { return this.age } `,那么所以由构造函数Person创建的对象都继承与Person.prototype。都能访问到Person.prototype上的所有属性和方法。

小结一下：
javascript中实现继承的根本是对象的原型__proto__，该属性指向一个内存中的对象（原型对象）。在访问对象的属性时候，会沿着对象的原型查找属性，直到查找到null,改查找的路径就像链条一样，所以就称为原型链。
