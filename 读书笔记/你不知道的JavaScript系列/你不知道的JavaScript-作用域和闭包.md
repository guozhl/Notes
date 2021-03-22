在学习今天的内容之前，先来看一道经典的面试题。
示例 1:
```javascript
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = function () {
    console.log(i);
  };
}

data[0]();  // 3
data[1]();  // 3
data[2]();  // 3
```

示例 2:
```javascript
var data = [];

for (var i = 0; i < 3; i++) {
  data[i] = (function (i) {
      return function(){
          console.log(i);
      }
  })(i);
}

data[0]();	// 0
data[1]();	// 1
data[2]();	// 2
```

示例 3:
```javascript
var data = [];

for (let i = 0; i < 3; i++) {
  data[i] = function () {
    console.log(i);
  };
}

data[0]();	// 0
data[1]();	// 1
data[2]();	// 2
```

思考一下这三种示例代码有何不同？为什么会导致结果不一样？

下面来一探究竟吧～

### 作用域和作用域链
##### 理解作用域
简单来说，作用域就是变量的可作用范围。
```javascript
function funcA() {
    var a = 'a';
    console.log(a);  // a
    console.log(b);  // ReferenceError: b is not defined
}

function funcB() {
    var b = 'b';
    console.log(b);  // b
    console.log(a);  // ReferenceError: a is not defined
}

funcA();
funcB();
```
在上面代码中，变量 a 在函数 funcA 中定义，则 a 的可作用范围在函数 funcA 中。函数 funcB 同理。
所以在函数 funcA 中 访问不到变量 b。在函数 funcB 中访问不到变量 a。

##### 理解作用域链
作用域链，就是作用域的一种继承关系。
```javascript
function funcA() {
    var a = 'a';
    console.log(a);  // a
    console.log(b);  // ReferenceError: b is not defined

    function funcB() {
        var b = 'b';
        console.log(b);  // b
        console.log(a);  // a
    }

    funcB();
}

funcA();
funcB();  // ReferenceError: funcB is not defined
```
在作用域部分中我们看到，变量的作用范围局限在所定义自己的函数内。若我们将 funcB 定义在 funcA 中，则 funcB 就会继承 funcA 的作用域。

而在全局作用域下就访问不到 funcB 了，因为它被定义在了 funcA 内。

### 声明提升
我们来将上面的代码做一下修改
```javascript
function funcA() {
    console.log(a);  // undefined
    var a = 'a';
    console.log(c);  // ReferenceError: c is not defined
    
    function funcB() {
        console.log(b);  // undefined
        var b = 'b';
        console.log(a);  // a
        console.log(b);  // b
    }

    funcB();
}

funcA();
```
从代码中我们看到，第二行执行 ```console.log(a)``` 这行代码时，变量 a 还没有被定义，而是在下一行被定义了。但是代码并没有报错，而是打印出 undefined。

但是在第四行执行打印从未被定义过的变量 c 时 ```console.log(c)``` ，程序报错 ReferenceError: c is not defined。

这是为什么呢？

我们直觉上会认为 JavaScript 代码是一行一行执行的，但实际上，编译器会先对代码进行编译，然后再一行一行的执行。

> 当你看到 var a = 2; 时，可能会认为这是一个声明。但 JavaScript 实际上会将其看成两个声明：var a; 和 a = 2;。第一个定义声明是在编译阶段进行的。第二个赋值声明会被留在原地等待执行阶段。
>
> ——《你不知道的JavaScript》

所以在编译阶段，上面的代码会变成这样：
```javascript
function funcA() {
    // 变量 a 被声明提升
    var a;
    // 函数 funcB 被声明提升
    function funcB() {
        // 作用域 funcB 下 变量 b 被声明提升
        var b;
        console.log(b);  // undefined
        b = 'b';
        console.log(a);  // a
        console.log(b);  // b
    }
    console.log(a);  // undefined
    a = 'a';

    funcB();
}

funcA();
```
我们看到，不仅变量的声明被提升，函数的声明也被提升了。在函数 funcB 中，也同样存在声明提升。

在执行阶段，代码会一行一行的进行执行：
先执行  ```console.log(a);```，发现 a 只被声明了，未赋值，所以输出 undefined。
接下来执行 ```a = 'a';```，给变量 a 赋值。
然后执行函数 funcB 里的内容。同理外部作用域。

有个小重点：函数声明的优先级大与变量声明。
```javascript
funcA();

var funcA;

function funcA() {
    console.log('1');
}

funcA = function() {
    console.log('2');
}
```
这里同时声明了变量 funcA 和函数 funcA，但是最后执行的结果是打印出 '1'。

因为函数声明会被提升到普通变量之前，普通变量则会被认为是重复声明，而被忽略。

### 理解闭包
##### 思考问题
下面回到最初的问题，为什么三种示例代码的结果会不一样？

先看示例1:

我们知道 for 循环在 i 不符合条件时停止循环，也就是 i=3 的时候。
在执行完 for 语句后，开始调用 data 数组里被赋值的方法。这个时候，for 语句作用域里的 i 值为 3，所以导致执行 data 数组方法时的结果都为 3。

归根结底，这些方法共享了同一个作用域，也就是 for 语句里的作用域。

那么，我们如何解决呢？

我们应该给每个函数创建一个作用域，使他们互不干扰。

再看示例2:

我们在给 data 数组赋值的时候，为每一项用一个立即执行函数包起来，这样就给每一个函数都创建了独立的作用域，每个作用域都有自己独立的变量 i，这样就可以达到我们预期的效果，数组的每一项输出不同的 i 值。

##### 什么是闭包
实际上，在示例1中，我们在外部执行 ```data[0]()``` 时引用了 for 语句内部的变量 i，这就是闭包的效果。

而在示例2中，我们则为数组的每一项都创建了一个闭包作用域。

> 当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。
> 闭包使得函数可以继续访问定义时的词法作用域。
>
> ——《你不知道的JavaScript》

再看一个例子:
```javascript
function funcA() {
    var a = '1';
    function funcB() {
        console.log(a);
    }

    return funcB;
}

var func = funcA();

func();  // '1'
```
根据作用域规则，函数 func 是不应该能访问到声明在函数 funcA 中的变量 a 的，但是执行 func，却打印出 a 的结果。

这是因为我们将函数 funcB 作为引用从 funcA 中返回，从而在 funcA 中达到了闭包的效果。

##### let 和闭包
那么，示例3是怎么回事呢？

在ES6中，引入了 let 关键字用以声明变量。而用 let 声明的变量，不会被提升。

不仅仅是这样，let 还将变量绑定到所在的任意作用域中。

也就是说，在使用 let 关键字声明的 for 循环中，let 将每一个 i 都绑定在了每一次的循环中，从而达到了为每次循环的创建独立作用域的目的。
