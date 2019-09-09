# let 和 const



## let

### 基本用法

+ `let` 命令，同 `var` 一样，用于声明变量，但是所声明的变量，只在 `let`  命令所在的代码块有效	

  ```js
  {
      var a = 10;
      let b = 1;
  }
  console.log(a); // 10
  console.log(b); // ReferenceError: b is not defined
  ```

+ `for` 循环的计数器，就很适合 `let` 命令

  ```js
  for (let i = 0; i < 10; i++) {
    // ...
  }
  console.log(i); // ReferenceError: i is not defined
  ```

+ 观察两端代码输出的区别

  ```js
  var a = [];
  for (var i = 0; i < 10; i++) {
    a[i] = function () {
      console.log(i);
    };
  }
  a[6](); // 10
  ```

  + `i` 使用 `var` 命令来声明的，所以在全局范围内有效，即全局中只有一个 `i`
  + `console.log(i)` 中的 `i` 也是全局的  `i`
  + 所有数组 `a` 的成员里面的 `i`，指向的都是同一个 `i`

  ```js
  var a = [];
  for (let i = 0; i < 10; i++) {
    a[i] = function () {
      console.log(i);
    };
  }
  a[6](); // 6
  ```

  变量`i`是`let`声明的，当前的`i`只在本轮循环有效，所以每一次循环的`i`其实都是一个新的变量，所以最后输出的是`6`。

  

### 不存在变量提升

+ `var `命令存在“变量提升”的现象，即变量在声明之前使用，值为`undifined`

+ `let`命令要求，所有的变量必须在声明之后使用，否则会跑出错误

  ```js
  // var的情况
  console.log(foo); // undefined
  var foo = 10;
  
  // let的情况
  console.log(bar); // bar is not defined
  let bar = 10;
  ```



### 暂时性死区

+ 只要块级作用域内存在 `let` 命令，他所声明的变量就绑定了这个区域，不再受到外部影响

  ```js
  var tmp = 123;
  
  if (true) {
    tmp = 'abc'; // ReferenceError
    let tmp;
  }
  ```

+ ES6 规定，如果区块中存在着 `let` 和 `const`命令，这个区块 对这些命名声明的变量，一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

+ **暂时性死区（temporal dead zone，简称 TDZ）**本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。

  ```js
  if (true) {
    // TDZ开始
    tmp = 'abc'; // ReferenceError
    console.log(tmp); // ReferenceError
  
    let tmp; // TDZ结束
    console.log(tmp); // undefined
  
    tmp = 123;
    console.log(tmp); // 123
  }
  ```



### 不允许**重复声明**

+ `let`不允许在相同作用域内，重复声明同一个变量。

  ```js
  // 报错
  function func() {
    let a = 10;
    var a = 1;
  }
  
  // 报错
  function func() {
    let a = 10;
    let a = 1;
  }
  ```

  因此，不能在函数内部重新声明参数。

  ```js
  function func(arg) {
    let arg;
  }
  func() // 报错
  
  function func(arg) {
    {
      let arg;
    }
  }
  func() // 不报错
  ```

  

## 块级作用域

### 为什么需要块级作用域

在ES5中只有全局作用域和函数作用域，没有块级作用域，这样会带来很多不合理的场景

+ 场景1：内层变量可能会覆盖外层变量

  ```js
  var temp = new Date()
  function f () {
      console.log(tmp)
      if (false) {
          var temp = 'hello world'
      }
  }
  f() // ReferenceError: tmp is not defined
  ```

+ 场景2：用来计数的循环变量泄露为全局变量。

  ```js
  var s = 'hello'
  for (var i = 0; i < s.length; i++){
      console.log(s[i])
  }
  console.log(i) // 5
  ```



### ES6 的块级作用域

`let`实际上为 JavaScript 新增了块级作用域。

```js
function f1 () {
    let n = 5
    if (true) {
       let n = 10
    }
    console.log(n)
}
f1() // 5
```

上述代码有两个块级作用域，都声明了变量`n`，运行输出 5。这表明外层代码块不受内层代码块的影响。



ES6 允许块级作用域的任意嵌套

```js
{{{
    { let n = 10 }
    console.log(n) // ReferenceError: n is not defined
}}}
```



### 块级作用域和函数声明

ES6 明确允许在块级作用域中声明函数。ES6 规定，块级作用域中的函数声明语句的行为类似与 `let`,在块级作用域之外不可引用

但是不建议在块级作用域中声明函数，如果非要，也应该写成表达式

```js
// 块级作用域内部的函数声明语句，建议不要使用
{
  let a = 'secret';
  function f() {
    return a;
  }
}

// 块级作用域内部，优先使用函数表达式
{
  let a = 'secret';
  let f = function () {
    return a;
  };
}
```



另外 ES6 的块级作用域必须有 花括号{}

```js
// 第一种写法，报错
if (true) let x = 1;

// 第二种写法，不报错
if (true) {
  let x = 1;
}
```



## const 命令

### 基本用法

+ `const`声明一个只读的常量。一旦声明，常量的值就不能改变。

  ```js
  const PI = 3.1415
  console.log(PI)
  
  PI = 3
  // TypeError: Assignment to constant variable.
  ```

+ `const` 声明的变量不能改变值，意味着初始化时必须赋值

  ```js
  const foo;
  // SyntaxError: Missing initializer in const declaration
  ```

+ 与 `let` 相同， `const` 声明的变量，也只在块级作用域内有效

  ```js
  if (true) {
      const MAX = 5;
  }
  
  MAX // ReferenceError: MAX is not defined
  ```

+ `const`命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。

  ```js
  if (true) {
    console.log(MAX); // ReferenceError
    const MAX = 5;
  }
  ```

+ `const`声明的常量，也与`let`一样不可重复声明。

  ```js
  var message = "Hello!";
  let age = 25;
  
  // 以下两行都会报错
  const message = "Goodbye!";
  const age = 30;
  ```



### 本质

`const` 本质上并不是保证变量的值不改变，而是保证变量指向的那个栈内存地址的所保存的数据不能改变。

当变量是基本数据类型时，值保存的就是变量指向的那个栈内存地址，因此等同于变量；

当变量是引用数据类型时，在栈内存中保存的指向堆内存的指针是不能变的，即对象是可以增删属性，只不过不能重新赋给一个新对象，因为这样的话，指向堆内存地址的那个指针就会改变，这个是不被允许的

```js
const foo = {}
foo.prop = 123
console.log(foo.prop) //123

foo = {}; //TypeError: Assignment to constant variable.
```

```js
const a = [];
a.push('Hello'); // 可执行
a.length = 0;    // 可执行
a = ['Dave'];    // TypeError: Assignment to constant variable.
```



如果真的想将对象冻结，应该使用 `object.freeze` 方法

```js
'use strict' // 严格模式
const foo = Object.freeze({})

foo.prop = 123 // TypeError: Cannot add property prop, object is not extensible
```

除了将对象本身冻结，对象的属性也应该冻结。下面是一个将对象彻底冻结的函数。

```js
var constantize = (obj) => {
    Object.freeze(obj)
    Object.keys(obj).forEach( (key, i) => {
        if ( typeof obj[key] === 'object') {
            constantize(obj[key])
        }
    } )
}
```



## 顶层对象的属性

所谓顶层对象，在浏览器中，指的是 `window` 对象，在 Node 中指的是 global 对象

ES5 之中，顶层对象的属性与全局变量是等价的。这会带来各种问题

```js
window.a = 1;
a // 1

a = 2;
window.a // 2
```

ES6 为了改变这一点，一方面规定，为了保持兼容性，`var`命令和`function`命令声明的全局变量，依旧是顶层对象的属性；另一方面规定，`let`命令、`const`命令、`class`命令声明的全局变量，不属于顶层对象的属性。也就是说，从 ES6 开始，全局变量将逐步与顶层对象的属性脱钩。

```js
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 1

let b = 1;
window.b // undefined
```







