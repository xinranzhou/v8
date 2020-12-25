<!--
 * @Author: your name
 * @Date: 2020-12-24 09:37:49
 * @LastEditTime: 2020-12-25 09:53:16
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: /v8/doc/scope_ region.md
-->
## js中是怎样查找变量的

### js中两个链

- 原型链：用于对象查找属性，对象之间通过原型__proto__链接在一起，可是上层对象通过该隐藏属性向下层对象查找属性。
- 作用域链：用于运行时候变量的查找，类似洋葱模型，没一层代表一个作用域，由函数最终到全局对象(浏览器)window|(nodejs)global。

### 作用域

在ES6中作用域除了函数之外，{}包裹的使用let const定义的变量亦属于一个作用域中，即let const 定义的变量只能在{}中生效。与函数内部变量只能在函数内部生效一样。
所以作用域的概念可以这样定义：定义的变量只能在某个函数块（区域）中生效（被访问到）。
```
var name = 'x1';
var age = 12;

function foo() {
    bar()
    var name = 'foo'
    console.log(name)
    console.log(age)
}
function bar() {
    name = 'bar'
}
foo()

// foo
// 12
```

```
var name = 'x1';
var age = 12;

function foo() {
    var name = 'foo'
    bar()
    console.log(name)
    console.log(age)
}
function bar() {
    name = 'bar'
    console.log(age)
}
foo()

// foo
// 12
```
即使bar在foo定义name之后调用，执行bar，其实bar修改的name值是window下的，因为在定义函数的时候编译阶段其函数内部变量已经确定，即bar函数内部的name在运行前已经确定是属于window。所以即使在foo中调用时候上层作用域是foo，也不会改变内部定义时候确认的变量。
其实也好理解：试想一下，如果不在编译阶段确定函数内部的依赖，在运行时候在确定变量（定义的变量）位置，那岂不是同样的代码在不同环境下运行结果不一样。

```
var name = 'x1';
var age = 12;

function foo() {
    var name = 'foo'
    var age = 10
    bar()
    console.log(name)
    console.log(age)
}
function bar() {
    name = 'bar'
    console.log(age)
}
foo()

// foo
// 12
```
### js变量是怎样定义
关于js中的变量是先定义后使用，如var a = 1,在编译阶段会先定义变量a = undefined,在运行时在对a赋值1，所以在浏览器中输入a = 1会看到下面打印一个undefined。
与作用域相关的是，在解析阶段对于每一个作用域中的变量会先确认其上下文关系，然后在运行时在确认其值。
对于
```
var name = 'x1';
var age = 12;

function foo() {
    var name = 'foo'
    var age = 10
    bar()
    console.log(name)
    console.log(age)
}
function bar() {
    name = 'bar'
    console.log(age)
}
foo()
```
在编译阶段：
全局作用域window： name = undefined, age = undefined , foo = undefined, bar = undefind。
函数foo作用域中： name = undefined, age = undefined
函数bar作用域中： window.name window.age
### 作用域

什么是作用域？在js中就是当前运行上下，比如函数运行时候，是会在执行栈中开辟一块空间来执行函数，该空间包含来函数定义的变量，参数，以及外部变量引用的信息，最重要的一点是全局作用域上下文。
如上述例子，foo函数运行时，推入执行栈中，当运行到bar()的时候，回先在但前作用域foo中查找，没有找到关于bar的信息，再往上层作用域（windonw）中查找，找到关于bar的定义，将bar重内存中取出放到执行栈中运行，运行到name = bar的时候，查找变量name，此时bar的上层作用域链是window而不是foo。因为作用域在解析时已经确定了。所以此时的name是window下的name。
那如果要让bar中的name为foo中的name,该如何写呢？

```
var name = 'x1';

function foo() {
    var name = 'foo'
    bar(name)
}
function bar(name) {
    console.log('name', name)
}
foo()
// foo
```
只能通过传参的形式，在定义bar函数的时候，定义参数name后，在bar作用域中其实已经定义了一个name变量，在根据传入的实参重赋值name。

### 作用域链的使用场景

一般在开发中关于作用域链的查找层次一般都不回太深，另外一个使用场景就是大名鼎鼎的闭包，其返回的函数能访问上层函数中定义的变量，这就是根据作用域链来查找的。


