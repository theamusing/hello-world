# hw5
Object.hasOwn

和

Object.prototype.hasOwnProperty
的功能和区别
## 功能
先来看一个简单的例子
```
//an example
let a = {name:"dog", size:"small"};
let b = {name:"cat"};
Object.setPrototypeOf(b, a);
Object.hasOwn(b, "size"); //false
Object.hasOwn(b, "name"); //true
b.hasOwnProperty("size"); //false
b.hasOwnProperty("name"); //true
```
由上述例子可以看出，两者的功能都是判断一个对象是否有一个属性，这个属性必须是他自己的而非从原型中继承来的。比如例子中的b将自己的原型prototype设置为了a，那么b继承了a中的属性“size”，但是上述两个方法认为这个属性不在b中，因为“size”是在a中声明的。至于“name”，因为b中本身就有“name”属性，所以可以被这两个方法找到。

那么，这两者有什么区别呢？
## 区别
我们先来看Object.prototype.hasOwnProperty。

这个函数是什么意思呢？当我们用一个obj（如上文中的b）调用它时，由于obj是由构造函数Object创造的，obj的原型就是Object.prototype。所以我们实际上是在调用Object.prototype上的函数hasOwnProperty。如果这个原型链没有被改变，我们就能成功找到原型链上的这个函数。

然而，由于object的原型链可能被更改，我们未必能够成功调用这个函数。
来看下面的例子：
```
Object.setPrototypeOf(b,null);
b.hasOwnProperty("name"); //error: 没有这个函数
```
由于b原型被更改成了null，hasOwnProperty就不在b的原型链上了，也就不能找到这个函数。

不过，如果我们让b直接调用定义在Object.prototype上的函数，就可以正常使用。
```
Object.prototype.hasOwnProperty.call(b,"name"); //true
```

再来看Object.hasOwn()。由于这个函数是定义在Object上的，所以即使obj的原型链被改变，仍然可以正常调用。
```
Object.setPrototypeOf(b,null);
Object.hasOwn(b,"name"); //true
```
## 总结
一般情况下两个函数都能够找到一个obj上的属性，不过当obj的原型链被改写时，Object.prototype.hasOwnProperty可能会失效，而Object.hasOwn能够正常调用。

顺带一提，查看hasOwnProperty的property descriptor可以发现，hasOwnProperty是不可见的，但是可重写的，也就是说我们可以人为改写这个函数来导致错误的结果。

