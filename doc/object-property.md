### V8是怎样对js对象储存与优化对象属性访问
##### 通常对象储存方式有两种
- 排序属性（elements），为线性数据结构（线性数据结构在取数据时候可以通过索引的方式获取元素，所以访问效率高，但是删除其中一个元素或者增加一个元素都会引起重新计算内存）
> ECMAScript 规范中定义了数字属性应该按照索引值大小升序排列，字符串属性根据创建时的顺序升序排列
- 常规属性（properties）常规属性就是通常以字符串作为key时的属性，所以所有的常规属性都是储存在properties属性中，数据结构在10个内元素时候为线性结构，大于10个会以非线性形式储存
```javascript

function Foo() {
    this[100] = 'test-100'
    this[1] = 'test-1'
    this["B"] = 'bar-B'
    this[50] = 'test-50'
    this[9] =  'test-9'
    this[8] = 'test-8'
    this[3] = 'test-3'
    this[5] = 'test-5'
    this["A"] = 'bar-A'
    this["C"] = 'bar-C'
}
var bar = new Foo()


for(key in bar){
    console.log(`index:${key}  value:${bar[key]}`)
}
```
运行该代码会打印出
```javascrpit

index:1  value:test-1
index:3  value:test-3
index:5  value:test-5
index:8  value:test-8
index:9  value:test-9
index:50  value:test-50
index:100  value:test-100
index:B  value:bar-B
index:A  value:bar-A
index:C  value:bar-C
```
在内存中储存情况
![avatar](https://static001.geekbang.org/resource/image/af/75/af2654db3d3a2e0b9a9eaa25e862cc75.jpg)

##### V8是怎样优化对象属性的访问
- 获取对象属性

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 在v8内部对于每一个对象都会分配两个属性elements属性储存排序属性，properties属性储存常规属性。那么在访问对象属性到时候就会多了一层属性访问。比如想要访问obj.a,大概会是这样obj.properties.a。同理排序属性也是会先到elements。

- 优化策略

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 在v8内部，在对象属性少于10个时候，会直接把对象到属性分配到对象上面。
![avatar](https://static001.geekbang.org/resource/image/f1/3e/f12b4c6f6e631ce51d5b4f288dbfb13e.jpg)
如上图，当只有三个常规属性时候，v8会直接把属性直接作为对象本身属性，而不是储存在properties中，这样到化访问就会少了一层。当属性值大于10之后，剩下到属性同样会被储存在properties中，所以如果是20个属性，那么就有10个在properties中，此时properties中的数据结构为线性结构，如果对象有21个属性，那么properties中的数据结构为非线性。
```javascript

function Foo(property_num, element_num) {
    //添加可索引属性
    for (let i = 0; i < element_num; i++) {
        this[i] = `element${i}`
    }
    //添加常规属性
    for (let i = 0; i < property_num; i++) {
        let ppt = `property${i}`
        this[ppt] = ppt
    }
}
const bar = new Foo(10,10)
```
** 上面的测试代码可以在调试工具memory中查看当前对象的快照**
![avatar](https://static001.geekbang.org/resource/image/d2/d3/d2a123d127a2895d9f0d09be61cc55d3.png)
可以在`filter`中过滤出当前对象Foo
![avator](https://static001.geekbang.org/resource/image/2b/89/2b4ee447d061f72026ca38d6dfc25389.png)

1 当常规属性为100时候，properties数据结构为线性结构
```javascript
const bar = new Foo(20,10)
```
![avatar](https://static001.geekbang.org/resource/image/49/86/49c1f8e735e5b7772f3d54fb53eae386.png)

2 当常规属性为100时候，properties数据结构为非线性
```javascript
const bar = new Foo(100,10)
```
![avatar](https://static001.geekbang.org/resource/image/da/69/dab6d6e2291117781e4294f27113d469.png)

总结：
1 对象属性尽量不要太多10个以内最好
2 能不用数字作为key就不用，因为访问会多一层elements属性
3 尽量不要delete属性，线性数据结构删除内容会导致内存重计算