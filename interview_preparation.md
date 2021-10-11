#### 介绍 JS 的基本数据类型

```
String, Number, Boolean, Null, Undefined, Symbol, BigInt
```

#### JavaScript 有几种值的类型

```
有两种，基本类型(Primitive)和引用类型(Reference).　主要类型的值为简单存储，引用类型则存储为对象(Object)，本质就是数据在内存中的地址(References to locations in memory).

部分人认为JavaScript与其他语言一样，区分主要和引用类型是把主要类型存放在栈内(Stack)，引用类型存放在堆(Heap).

但其实JS有一个叫变量对象(Variable object)的东西．主要类型的值直接存储在变量对象上，引用类型的值则为指针存储在变量对象上．
```

#### typeof null 返回什么？如何判断 null?

```
返回"object", TC39承认这其实是个Bug. 导致这个Bug的原因可以解释为null是一个空对象指针(empty object pointer), 所以返回"object".

判断null直接强对比即可: value === null，返回值为true或false．
```

#### null 和 undefined 有什么区别？

```
null和undefined都只有一个值，区别是null可以理解为空，是一个空对象．

undefined代表未定义，变量声明未赋值的情况下为undefined.

undefined不是一个保留字，所以会造成命名空间冲突，影响对undefined的判断，正确获取undeinfed做法为使用void 0
```

#### undeclared 和 undefined 有什么区别？

```
undeclared是变量未声明，属于错误信息，undefined是变量已声明但尚未赋值.
```

#### 什么是构造函数(constructor function)？

```
构造函数跟普通函数没有任何区别，任何函数配合new关键字使用会变成构造函数．构造函数区分与普通函数的方式为首字母大写．

构造函数的优点是通过一个构造函数创建的所有对象包含相同的方法和properties．

通过构造构造函数创建的对象均为此构造函数的实例(instances).

构造函数存在意义主要也就是简化创建多个拥有相同方法(methods)和相同属性(properties)的对象．

当函数通过new关键字被当作构造函数执行时，会发生如下操作：
1. 一个新的空对象被创建
2. 新对象的内置属性[[prototype]]指向构造函数的prototype property
3. 该新对象被设置为构造函数this的指向地
4. 除非构造函数手动指定了返回的对象，否则永远返回新构建的对象；如果何构造函数返回值为基本类型，会被忽略；
```

```js
// Person构造函数接受一个name参数，并赋值给this指针
function Person(name) {
  // this对象会在Person被调用时自动创建，并指向被Person构造的对象
  this.name = name;
  this.sayName = function () {
    console.log(this.name);
  };
}
```

#### 判断对象是否为某个对象实例的方法？

```
方法一：使用instanceof．

方法二：使用constructor．每个实例对象在被创建时都会拥有一个constructor的property，constructor包含一个引用类型的值，指向创建此对象的构造函数．
所有普通对象(generic objects)的constructor指向的均为Object．

通过自定义构造函数(custom constructor)创建的对象，constructor指向构造函数本身．
```

```js
function Person() {}
const person1 = new Person();
const person2 = new Person(); //无参数可以省略括号
console.log(person1 instanceof Person); // true
console.log(person2 instanceof Person); // true
console.log(person1.constructor === Person); // true
console.log(person2.constructor === Person); // true
```

#### 判断数组(Array)的方法

```js
const arr = [];
console.log(arr instanceof Array;) // true
console.log(Array.isArray(arr);) // true
```

#### 为什么基本类型(primitive)可以使用方法？

```
因为有primitive wrapper types，为了方便操作String, Number, Boolean，primitive wrapper types会在读取String, Number, Boolean时自动创建．
```

#### 为什么 typeof function 返回 function？

```
function在JS里也是对象，唯一区别是function有一个特殊的内置属性(internal property)[[call]]，表示为可执行的．ECMAScript协议规定typeof遇到包含[[call]]的对象返回function．
```

#### 闭包(closure)

```
闭包是JS机制的一个特性．

闭包首先避不开作用域(scope)，作用域是JS在编译过程中前期完成的工作，我们无法干预，也无法搭建作用域之间的桥梁．

作用域的作用主要是为了安全性，减少全局变量，就是说减少混乱程度．在JS里，所有函数都有自己的文本环境和作用域，外界无法访问．

但是函数内的信息如变量等，在函数执行完毕后就无法访问了，但是我们有时候可能需要持续访问某个函数内部的信息，比如把变量私有化等等，闭包提供了这样一个特性，就是外层函数执行完毕后出栈后，内层的函数还可以持续访问外层函数内的信息．

滥用闭包可能会导致内存消耗过大．
```

```js
function closure() {
  let counter = 0;
  return function () {
    if (counter > 6 || counter === null) {
      counter = null;
      return;
    }
    counter++;
    console.log(counter);
  };
}

const test = closure();

for (let i = 0; i < 15; i++) {
  test();
}
// output: 1 2 3 4 5 6 7
```

#### This 指针

```
两个常见误区：第一，this指针不指向函数本身．第二，this指针跟函数的作用域(scope)没有关系．

this是执行上下文的一个属性．

this是一个指针根据函数被调用时的上下文环境决定指向，是runtime-binding而非author-time binding．

当一个函数被调用时，先会创建执行语境(execution context)，执行语境是一种活动记录(activation record)，记录包含函数被调用的位置(执行栈call-stack)，
函数是如何被调用的，有什么参数被传进来了等等信息．执行语境的其中一个属性(property)就是this指针(执行语境本身也是一个对象)．this指针为一次性，函数每次被调用都会改变this的指向．

This的指向遵循几条规则：
1. 默认绑定(Default Binding)，单独调用函数时，this永远指向global object．

2. 隐式绑定(Implicit Binding)，当函数作为方法(method)被调用时，this指向最后一个方法所在的对象

3. 直接绑定(Explicit Binding)，使用apply或call．对比隐式绑定，直接绑定无需更改对象本身(mutate)．

4. 强绑定(Hard Binding)，使用bind()．

5. 当函数被作为构造函数执行时，this会指向新创建的对象．

优先级为：new -> call/apply/bind -> method call -> default binding
```

#### bind()实现

```js
// simplified
function bind(fn, obj) {
  return function () {
    return fn.apply(obj, arguments);
  };
}

// es6
const myBind = (fn, obj) => {
  return function () {
    return fn.apply(obj, arguments);
  };
};
```

#### call()和 apply()的区别？

```
两个方法的作用完全一致，区别在与call()第二个参数是string，apply()是一个数组．
```

#### 原型链(prototype chain)

```
首先解释一下为什么会有原型(prototype)．构造函数可以批量生产对象实例(instance)，不过构造函数不会减少代码冗余(code redundancy)，因为每个实例都拥有相同的方法和属性，如果使用构造函数创建了100个对象，那么就有100个相同的函数做同样的事情，内存消耗非常大．原型(prototype)就是来解决这个问题的．

所有对象实例都拥有一个内置[[Prototype]]，该属性指向原型对象．

当使用new创建一个新对象时，构造函数的原型属性(prototype)被赋值给对象的内置[[Prototype]]．这样的话所有的对象实例都可以共享一个方法，可以有效减少冗余度，并大幅度减少内存消耗．

绝大部分JS引擎在所有对象上提供了__proto__属性，可以用来读写内置[[Prototype]]，不过写入行为很危险，不建议．

原型是一个共享的对象，拥有可以被多个对象共同访问的方法(methods)和属性(properties)．
```

#### 如何判断某对象拥有某个属性(property)？

```
使用obj.hasOwnProperty()方法可以判断，hasOwnProperty不会去原型链上寻找．而in操作符会在原型链上寻找，所以结果不一定准确．
```

#### 如何判断某个 property 是在原型链上而不是本身拥有？

```js
function hasPrototypeProperty(object, name) {
  return name in object && !object.hasOwnProperty(name);
}
```

#### 继承

```
可以通过原型链的方式继承，也可以通过构造函数来实现．
```

#### Promise

```
处理多层回调函数的一种手段．

```

#### JS 在 V8 引擎执行后发生了什么？

```

```

#### 函数声明(declaration)和函数表达式(expression)的区别？

```
表达式函数为匿名函数，因为函数对象本身并没有名字．
声明函数会被hoisted至所在文本(context)最顶端，表达式函数不会．因为声明函数的名字是已知的．
```

#### 什么是 First Class Function？

```
First Class Function就是一切可以对object操作都可以对function进行．
例如可以把函数赋值给一个变量，可以被添加在对象上，可以被当成参数传递进另一个函数等等．
```

#### for-in 和 for-of 的区别？

```
for in遍历数组会遍历所有可枚举的属性包括原型上的，并且遍历顺序有可能不是按照数组实际顺序．
for of遍历数组的元素值，不包含原型上的东西．

for in遍历对象的建名，可以遍历到原型方法．获取对象属性可以使用Object.keys()
```

#### 对象属性(Object Property)的类型有几种？

```
有两种，一种叫data properties，包含一个值．
另一种是accessor properties，这种类型不包含任何值，而是定义一个函数，当对象被读取的时候会被执行(getter)，或者当对象被写入的时候执行(setter)．
```

#### 禁止操作对象的方法？

```
第一种：使用Object.preventExtensions()．所有对象都有内置的[[Extensible]]属性，如果[[Extensible]]为true时可以添加新的properties，为false时则不能．需要注意的是，[[Extensible]]只能防止添加新的properties．

第二种：封存(Sealing)，使用Object.seal()方法可以封存对象．被封存的对象[[Configurable]]属性会被设置为false，因此不能添加，移除和更改对象类型．

第三种：冻结(Frezzing)，使用Object.freeze()方法冻结对象，被冻结的对象只能读取(read-only)，对象一旦被冻结不能解冻．
```

#### AJAX 是什么？

```
Asynchronous JavaScript and XML的缩写，指通JS实现异步通信，从服务器获取XML文档中提取数据，再更新当前网页的部分实现局部刷新．

1. 创建一个异步调用对象，XMLHttpRequest．
2. 创建新的HTTP请求，指定方法，URL，以及验证方式．
3. 设置响应HTTP请求的函数．
4. 发送HTTP请求．
5. 获取异步调用返回的数据．
6. 使用JS和DOM实现局部刷新．
```

#### 访问一个网站发生了什么？

```
1. 首先对url进行解析，分析所需的传输协议和请求资源路径．
2. 判断请求资源是否有缓存，如果有且没过期就直接用，否则发起新的请求．
3. 下一步是通过dns解析IP地址．
4. 然后是一系列的网络操作，比如获取主机mac地址什么的，因为数据传输需要主机mac地址，然后tcp建立链接，三次握手．如果是https还有一个四次握手．
5. 请求到达服务器后返回html文件，浏览器接受到后对html进行解析开始渲染．
6. 首先开始构建dom树，然后根据解析到的css文件构建cssom树，如果遇到script标签判断是否有defer或者async属性，要不然家在和执行会造成页面的渲染和阻塞．当htmldom和cssom构建完成后开始布局．
7. 完成布局后使用浏览器的ui接口对页面进行绘制．
8. tcp断开链接．
```

#### Post 和 Get 的区别？

```
一般Get请求用于对服务器资源不会产品影响的场景，比如请求一个网页．

Post一般用于对服务器资源会产生影响的情景，比如注册用户等操作．

因为场景不同，Get一般会被缓存，Post则不会．

报文格式，Get的主体部分为空，Post报文主体一般为像服务器发送的数据．
```

#### 常用数据结构

```
数组：在内存中按顺序排列，每个元素都有一个下标，读取速度快，删除和添加不方便．
链表：每个数据都有下一个数据的指针，内存结构较好，添加和删除性能高，查找慢．
栈：先进后出
哈系：键值方式存储数据，便于查找．JS对象就是哈希．

```
