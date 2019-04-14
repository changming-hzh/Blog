## 块级作用域
通过var声明变量存在变量提升：
```JavaScript
if (condition) {
    var value = 1
}
console.log(value)
```
初学者可能会认为当变量condition为true时，才会创建value，如果condition为false是，不会创建value，结果应该是报错，然而因为JavaScript存在变量提升的概念，代码等同于：
```JavaScript
var value
if (condition) {
    value = 1
}
console.log(value) // undefined
```
所有当condition为false，输入结果为undefined。

ES5 只有全局作用域和函数作用域，其中变量提升也分成两种情况，一种全局声明的变量，提升会在全局最上面，上面就属于全局变量声明，一种是函数中声明的变量，提升在函数的最上方：
```JavaScript
function fn() {
    var value
    if (condition) {
        value = 1
    }
    console.log(value) // undefined
}

console.log(value) // Uncaught ReferenceError: value is not defined
```
所有当condition为false，函数内输入结果为undefined，函数输入就会报错Uncaught ReferenceError: value is not defined。函数的变量提升是根据最近的外层函数提升，没有函数就为全局下提升。

为规范变量使用控制，ECMAScript 6 引入了块级作用域。
块级作用域就是 {} 之间的区域

## let 和 const

我们来整理一下 let 和 const 的特点：
1. 不存在变量提升
```JavaScript
if(condition) {
    let value = 'value'
}
console.log(value) // Uncaught ReferenceError: value is not defined
```
不管 conditon 为 true 或者 false ，都无法输出value，结果为 Uncaught ReferenceError: value is not defined

2. 重复声明报错
 ```JavaScript
 let value = 'value'
 let value = 'value'
 ```
 重复声明同一个变量，会直接报错 Uncaught SyntaxError: Identifier 'value' has already been declared

3. 不绑定在全局作用域上
```JavaScript
var value = 'value'
console.log(window.value) // value
```
在来看一下let声明：
```JavaScript
let value = 'value'
console.log(window.value) // undefined
```

### let 和 const 的区别:

const声明一个只读的常量。一旦声明，常量的值就不能改变。
```JavaScript
const value = 1

value = 2 // Uncaught TypeError: Assignment to constant variable.
```
上面代码表明改变常量的值会报错。

const声明的变量不得改变值，这意味着，const一旦声明变量，就必须立即初始化，不能留到以后赋值。
```JavaScript
const foo;
// SyntaxError: Missing initializer in const declaration
```
上面代码表示，对于const来说，只声明不赋值，就会报错。

对于对象的变量，变量指向是数据指向的内存地址。const只能保证数据指向内存地址不能改变，并不能保证该地址下数据不变。
```JavaScript
const data = {
    value: 1
}

// 更改数据
data.value = 2
console.log(data.value) // 2

// 更改地址
data = {} // Uncaught TypeError: Assignment to constant variable.
```
上述代码中，常量 data 存储的是一个地址，这里地址指向一个对象。不可变的只是这个地址，即不能将 data 指向另一个地址，但是对象本身是可以变的，所以依然为其更改或添加新属性。


## 暂时性死区
在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。

let 和 const 声明的变量不会被提升到作用域顶部，如果在声明之前访问这些变量，会导致报错：
```JavaScript
console.log(typeof value); // Uncaught ReferenceError: value is not defined
let value = 1;
```
这是因为 JavaScript 引擎在扫描代码发现变量声明时，要么将它们提升到作用域顶部(遇到 var 声明)，要么将声明放在 TDZ 中(遇到 let 和 const 声明)。访问 TDZ 中的变量会触发运行时错误。只有执行过变量声明语句后，变量才会从 TDZ 中移出，然后方可访问。

看似很好理解，不保证你不犯错：
```JavaScript
var value = "global";

// 例子1
(function() {
    console.log(value);

    let value = 'local';
}());

// 例子2
{
    console.log(value);

    const value = 'local';
};
```
两个例子中，结果并不会打印 "global"，而是报错 Uncaught ReferenceError: value is not defined，就是因为 TDZ 的缘故。

## 常见面试题
```JavaScript
for(var i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i)
    })
}
// 3
// 3
// 3
```
上述代码中，我们期望输出0，1，2三个值，但是输出结果是 3，3，3 ，不符合我们的预期。

解决方案如下：
使用闭包解法
```JavaScript
for(var i = 0; i < 3; i++) {
    (function(i) {
        setTimeout(() => {
            console.log(i)
        })
    })(i)
}
// 0
// 1
// 2
```

ES6 的 let 解法
```JavaScript
for(let i = 0; i < 3; i++) {
    setTimeout(() => {
        console.log(i)
    })
}
// 0
// 1
// 2
```
上述代码中，变量 i 是 let 声明的，当前的i只在本轮循环有效，所以每一次循环的i其实都是一个新的变量，所以最后输出的是0，1，2。你可能会问，如果每一轮循环的变量i都是重新声明的，那它怎么知道上一轮循环的值，从而计算出本轮循环的值？这是因为 JavaScript 引擎内部会记住上一轮循环的值，初始化本轮的变量i时，就在上一轮循环的基础上进行计算.

另外，for循环还有一个特别之处，就是设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。
```JavaScript
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```
上面代码正确运行，输出了 3 次abc。这表明函数内部的变量i与循环变量i不在同一个作用域，有各自单独的作用域。

如果尝试将 let 改成 const 定义：
```JavaScript
for (const i = 0; i < 3; i++) {
  console.log(i);
}
// 0
// Uncaught TypeError: Assignment to constant variable.
```
上述代码中，会先输出一次 0,然后代码就会报错。这是由于for循环的执行顺序造成的，i 定义为 0，然后执行 i < 3比较，符合条件执行循环主体，输出一次 0, 然后执行 i++，由于 i 使用const定义的只读变量，代码执行报错。

说完了普通的for循环，我们还有for…in循环呢~

那下面的结果是什么呢？
```JavaScript
const object = {a: 1, b: 1, c: 1};
for (const key in object) {
    console.log(key)
}
// a
// b
// c
```
上述代码中，虽然使用 const 定义 key 值，但是代码中并没有尝试修改 key 值，代码正常执行，这也是普通for循环和for…in循环的区别。

## Babel编译
在 Babel 中是如何编译 let 和 const 的呢？我们来看看编译后的代码：
```JavaScript
let value = 1;
```
编译为:
```JavaScript
var value = 1;
```
我们可以看到 Babel 直接将 let 编译成了 var，如果是这样的话，那么我们来写个例子：
```JavaScript
if (false) {
    let value = 1;
}
console.log(value); // Uncaught ReferenceError: value is not defined
```
如果还是直接编译成 var，打印的结果肯定是 undefined，然而 Babel 很聪明，它编译成了：
```JavaScript
if (false) {
    var _value = 1;
}
console.log(value);
```
我们再写个直观的例子：
```JavaScript
let value = 1;
{
    let value = 2;
}
value = 3;
```
```JavaScript
var value = 1;
{
    var _value = 2;
}
value = 3;
```
本质是一样的，就是改变量名，使内外层的变量名称不一样。

那像 const 的修改值时报错，以及重复声明报错怎么实现的呢？

其实就是在编译的时候直接给你报错……

那循环中的 let 声明呢？
```JavaScript
var funcs = [];
for (let i = 0; i < 10; i++) {
    funcs[i] = function () {
        console.log(i);
    };
}
funcs[0](); // 0
```
Babel 巧妙的编译成了：
```JavaScript
var funcs = [];

var _loop = function _loop(i) {
    funcs[i] = function () {
        console.log(i);
    };
};

for (var i = 0; i < 10; i++) {
    _loop(i);
}
funcs[0](); // 0
```

## 项目实践
在我们实际项目开发过程中，应该默认使用 let 定义可变的变量，使用 const 定义不可变的变量，而不是都使用 var 来定义变量。同时，变量定义位置也有一定区别，使用
var 定义变量都会在全局顶部或者函数顶部定义，防止变量提升造成的问题，对于使用 let 和 const 定义遵循就近原则，即变量定义在使用的最近的块级作用域中。

## ES6系列文章
ES6系列文章地址：https://github.com/changming-hzh/Blog

 