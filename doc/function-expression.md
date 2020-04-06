## 函数表达式vs函数声明
函数作为javascript中一等公民，可以作为参数，返回值，赋值给变量，同时可以隐藏变量还可以实现变量的隔离（IIFE）避免全局变量污染，最早实现模块化开发的方式。通常在开发中定义函数有两种方式，一种是函数声明，一种是函数表达式。
- 函数声明 是通过function关键字定义
```javascript
function foo() {
    console.log('foo')
}
```
- 函数表达
```javascript
var foo = function () {
    console.log('foo')
}
```