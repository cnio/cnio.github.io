## 浅析JavaScript中new关键字

我们知道，new关键字的作用一般用于实例化一个类。在JavaScript中，我们约定以大写字母开头的函数为一个类，也就是JavaScript中构造函数。

定义一个名为Car的类：
```js
function Car(tyre, materials, window) {
  this.tyre = tyre; // 轮胎
  this.materials = materials; //材质
  this.window = window; // 车窗
}
```

Car这个类相当于一个汽车模型，我们需要根据这个模型构造出许多“真”的汽车出来，也就是实例（instance）。

那么，造车那么麻烦，又脏又累的，谁去造呢？——new大哥

首先，我们造的车是用来卖的，卖给n个人。我们不能只造一辆车，让买车的人轮流使用一辆车。所以，我们实例化的时候需要在内存中实实在在开辟空间，不能是引用。

在JavaScript中，new大哥的造车分为四个步骤进行：

### 1、创建一个空对象`{ }`

首先new大哥需要造个汽车框架{ }，这个就是以后需要交付给客户的车框。
```js
let car1 = { };
```

### 2、链接该对象（car1）到构造函数的原型

构造函数的原型Car.prototype一般存放一些公共的东西，比如生产汽车的标准。我们交付车的时候，交付的不仅仅是车，还有一系列的标准，这样我们才能顺利开上路，不被交警拦下。
```js
let car1 = {};
car1.__proto__ = Car.prototype;
```

不过我们现在生产的可能是黑车，因为原型Car.prototype上面为空，还没有标准。不过我们可以添加标准，比如在中国行驶的汽车的方向盘在左边，我们需要更改一下汽车模型Car：
```js
Car.prototype.getStandard = function () {
    return {
        steering_wheel: 'left'     // 设置方向盘在左
    } 
};
```

### 3、将步骤1新创建的对象（car1）作为this上下文

上一步骤我们参考了生产标准，这下需要根据汽车模型生产汽车了。我们在构造函数的this对象中定义了很多属性，包括生产材质、轮胎等。我们需要把这些东西应用到新生产的汽车car1上
```js
Car.apply(car1, ['tyre', 'materials', 'window'])
```
可以把对应的参数改成想要的内容。apply方法的第一个参数即把this指向新创建的对象car1。

### 4、返回创建好的对象（car1）

这样我们的车就造好了，卖多少你说了算！
```js
return car1;
```

## 完整代码

1、构造函数

```js
function Car(tyre, materials, window) {
    this.tyre = tyre; // 轮胎 
    this.materials = materials; //材质
    this.window = window // 车窗
}

Car.prototype.getStandard = function () { 
    return { 
        steering_wheel: 'left' // 设置方向盘在左 
    } 
};
```
2.生成一个实例（new操作）
```js
let car1 = { };
car1.__proto__ = Car.prototype;

let result = Car.apply(car1, ['tyre', 'materials', 'window']);

if (typeof result === 'object' && result !== null) {
    return result;
}

return car1;
```
其中，第1、2行代码可以等价于如下代码，可以相互替换

let car1 = Object.create(Car.prototype)

需要注意6-8行代码，new操作符还有另一个特性，可以从构造函数返回一个任意的对象，它成为new操作符的返回结果。如果想让构造函数返回一个子构造函数的实例，这会十分有用。

1. 实现一个new操作符

function newOperator(Constr, args) {
    let thisValue = Object.create(Constr.prototype);
    let result = Constr.apply(thisValue, args);
    
    if (typeof result === 'object' && result !== null) {
        return result;
    }
    
    return thisValue;
}

( 完)