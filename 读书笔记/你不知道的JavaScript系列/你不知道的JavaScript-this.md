对于如何理解 this，我们最常见的答案是：谁调用它就指向谁。
那么，如何理解那个 who呢？

在理解之前，要先记住一句话：
__this实际上是在函数被调用时发生的绑定，它指向什么完全取决于函数在哪里被调用。__

### this 绑定规则
this 的绑定规则有4种：
1. 默认绑定
2. 隐式绑定
3. 显式绑定
4. new 绑定

##### 调用位置
在理解 this 的绑定规则之前，需要先了解 _调用位置_ 和 _调用栈_。

调用位置就是指函数在哪里被调用的，例如下面的代码：
```javascript
function baz() {
  // 当前调用栈是：baz
  // 因此，当前调用位置是全局作用域
  
  console.log( "baz" );
  bar(); // <-- bar的调用位置
}

function bar() {
  // 当前调用栈是：baz --> bar
  // 因此，当前调用位置在baz中
  
  console.log( "bar" );
  foo(); // <-- foo的调用位置
}

function foo() {
  // 当前调用栈是：baz --> bar --> foo
  // 因此，当前调用位置在bar中

  console.log( "foo" );
}

baz(); // <-- baz的调用位置
```

##### 默认绑定
默认绑定可以简单理解为在全局作用域下调用函数的绑定规则。
例如：
```javascript
function foo() {
  console.log(this.a);
}

var a = 2;

foo();  // 2
```
此时的 foo 函数在全局作用域下被调用，即执行 foo() 相当于 window.foo()，所以，foo 函数里的 this，指向全局作用域下的 window。

##### 隐式绑定
隐式绑定则需要考虑函数调用的上下文环境。
例如：
```javascript
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
  foo: foo
};

obj.foo();  // 2
```
此时函数 foo 的调用上下文环境是 obj，所以，this 便指向 obj 所在的环境。

##### 显式绑定
显式绑定即我们通常使用的 call 和 apply，将 this 显式的指定到一个对象上。
```javascript
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2
};

foo.call(obj);
```

##### new 绑定
在我们使用 new 时，实际上是调用了一个函数，它会执行下面的操作：
1. 创建（或者说构造）一个新对象。
2. 这个新对象会被执行[[Prototype]]连接。
3. 这个新对象会绑定到函数调用的 this。
4. 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。

思考下面的代码：
```javascript
function foo(a) {
  this.a = a;
}

var bar = new foo(2);

console.log(bar.a);  // 2
```
使用 new 来调用 foo(...) 时，我们会构造一个新对象并把它绑定到 foo(...) 调用中的 this 上。

### 箭头函数绑定
箭头函数是 ES6 的新语法，它的 this 不使用以上的任意一种绑定规则，而是根据外层作用域来决定的。

```javascript
function foo() {
  // 返回一个箭头函数
  return (a) => {
    // this 继承自 foo()
    console.log(this.a);
  }
}

var obj1 = {
  a: 1
};

var obj2 = {
  a: 2
};

var bar = foo.call(obj1);

bar.call(obj2);  // 1
```
在上面代码中，定义 bar 时，已经将 foo 中的 this 绑定到 obj1，而箭头函数里的 this，继承自 foo 函数。所以接下来无论如何调用，foo 中的 this 都会指向 obj1。
