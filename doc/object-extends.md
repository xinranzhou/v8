## 原型链，对象是如何实现继承的

#### 什么是继承
简单来说，继承就是一个对象可以访问另外一个对象的属性和方法。

在编程语言中继承的实现最典型的就是分为基于类的设计和基于原型的设计两种
- 基于类的设计 

最典型的语言就是java, c++,该类设计最大的特点就是提供类复杂的规则与关键字（ class extends private interface等）进行组合实现继承。

- 基于原型设计
javascript 通过原型属性实现继承的，虽然在es6规范中可以通过class extends关键字实现，但这只是一种语法糖，最终会被编译成基于原型属性的形式运行。

#### 原型是怎样继承的
每一个javascript对象都有一个隐藏属性__proto__，该属性被称为对象的原型(prototype)，把__proto__指向内存中一个对象，称为原型对象，那么对象就可以访问该原型对象上的属性和方法，比如两个对象A和B，如果把对象B的__proto__属性指向A， 那么B就可以访问A中的属性和方法，这样就实现B继承A
```javascript
var A = {
    name: 'zhang san'
}
var B = {
    age: 20
}
B.__proto__ = A
console.log(B.name) // zhang san
```
但是在实际项目中不应该使用这样的方式继承，因为__proto__其实是一个隐藏属性，只是在浏览器中提供可访问口子，其次这样继承会有很大的性能问题。
实际开发中 是通过构造函数的方方式来实现继承。
``` javascript
function Foo (name, age) {
    this.name = name
    this.age = age
}
Foo.prototype.getName = function() {
    console.log(this.name)
}
var a = new Foo('张三', 20)
var b = new Foo('李四', 20)
```
在javascript中通过关键字new 后面跟一个函数调用的方式，可以创建并返回一个对象，所以上述代码中会创建两个对象a b,同时都可访问getName，这儿就是继承函数prorotype属性上的getName方法。其实现继承的步骤是这样的（new关键字在v8中是怎样运行的）：
1. 创建一个对象 a = {}
2. `a.__proto__ = Foo.prototype` // a对象的原型指向函数Foo的prototype
3. `Foo.call(a, '张三', 20)` //调用函数Foo，并且把this指向对象a,并且填充属性name age

上述代码描述来构造函数是通过new创建对象，函数的prototype属性实现继承
#### 总结
1. javscript通过原型的方式实现继承，每一个对象都有一个隐藏属性__proto__,是实现继承的基石
2. 构造函数实现继承是通过函数的prototype属性
3. 原型链就是沿着__proto__所指向的对象一路查找访问的属性，直到找到之后返回，最终会找到null

