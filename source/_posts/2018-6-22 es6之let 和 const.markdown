---
layout:     post
title:      "es6之let 和 const"
subtitle:   "冷淡仙人偏得道 买定西风一笑"
date:       2018-6-22
author:     "幽僻处的行人"
header-mask: 0.3
categories:
    - es6
catalog:    true

tags:
    - es6

---

# 1. let 命令

 let特性：
 
   1.只在let命令所在的代码块内有效。
    
  a.
  
        {
          let a = 10;
          var b = 1;
        }
        
        a // ReferenceError: a is not defined.
        b // 1
        
  b. （let 与 for循环的用法） 要求必须 等let声明语句执行完之后，变量才能使用，不然会报Uncaught ReferenceError错误。
 
        var a = [];
        for (let i = 0; i < 10; i++) {
          a[i] = function () {
            console.log(i);
          };
        }
        a[6](); // 6  如同for 循环里产生了10次i的循环并没有被更新
        a[2](); // 2
        a[4](); // 4
        a[7](); // 7
        a[0](); // 0
        
        
  2.不存在变量提升 
  
    // var 的情况
    console.log(foo); // 输出undefined
    var foo = 2;
    
    // let 的情况
    console.log(bar); // 报错ReferenceError
    let bar = 2; 
        
        
  3.暂时性死区
  
    console.log(aicoder);    // 错误：Uncaught ReferenceError ...
    let aicoder = 'aicoder.com';
    // 这里就可以安全使用aicoder
    
   有些“死区”比较隐蔽，不太容易发现。
    
    function bar(x = y, y = 2) {
      return [x, y];
    }
    
    bar(); // 报错
    //因为参数x默认值等于另一个参数y，而此时y还没有声明，属于”死区“。
    
> ES6 明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。
     总之，在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”（temporal dead zone，简称 TDZ）。
     
   
    
  4.let变量不能重复声明
  
    let a = 0;
    let a = 'sss';
    // Uncaught SyntaxError: Identifier 'a' has already been declared
    

######**ES6规定暂时性死区和let、const语句不出现变量提升，主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为。这样的错误在ES5是很常见的，现在有了这种规定，避免此类错误就很容易了。
  
######**总之，暂时性死区的本质就是，只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量。**
        
#2. 块级作用域

>为什么需要块级作用域？

   >>第一：内层变量可能会覆盖外层变量。
   
       var tmp = new Date();
       
       function f() {
         console.log(tmp);
         if (false) {
           var tmp = "hello world";
         }
       }
       
       f(); // undefined
   
   >>第二：用来计数的循环变量泄露为全局变量。
   
        var s = 'hello';
        
        for (var i = 0; i < s.length; i++) {
          console.log(s[i]);
        }
        
        console.log(i); // 5


 块级作用域特点：
 
  1.ES6允许块级作用域的任意嵌套。
    
    {{{{{let insane = 'Hello World'}}}}};
    
  2.允许在块级作用域之中声明函数。
  
    // ES6严格模式
    'use strict';
    if (true) {
      function f() {}
    }
    // 不报错
 
 >ES6 规定，块级作用域之中，函数声明语句的行为类似于let，在块级作用域之外不可引用。
 
 >>> 特别注意：
 ES6的块级作用域允许声明函数的规则，只在使用大括号的情况下成立，如果没有使用大括号，就会报错。
    
    // 不报错
    'use strict';
    if (true) {
      function f() {}
    }
    
    // 报错
    'use strict';
    if (true)
      function f() {}
      
      
#const命令
  1.const声明一个只读的常量。一旦声明，常量的值就不能改变。
 
    const PI = 3.1415;
    PI // 3.1415
    
    PI = 3;
    // TypeError: Assignment to constant variable.
 
  2.const声明的变量不得改变值，这意味着，const一旦声明变量，就必须立即初始化，不能留到以后赋值。
  
    const foo;
    // SyntaxError: Missing initializer in const declaration
  
  3.const的作用域与let命令相同：只在声明所在的块级作用域内有效。
  
    if (true) {
      const MAX = 5;
    }
    
    MAX // Uncaught ReferenceError: MAX is not defined
    
  4. const命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。
 
    if (true) {
      console.log(MAX); // ReferenceError
      const MAX = 5;
    }
    
  5. const声明的常量，也与let一样不可重复声明。
    
    var message = "Hello!";
    let age = 25;
    
    // 以下两行都会报错
    const message = "Goodbye!";
    const age = 30;
    
  6.对于复合类型的变量，变量名不指向数据，而是指向数据所在的地址。const命令只是保证变量名指向的地址不变，并不保证该地址的数据不变，所以将一个对象声明为常量必须非常小心。
  
    const foo = {};
    foo.prop = 123;
    
    foo.prop
    // 123
    
    foo = {}; // TypeError: "foo" is read-only
 
   下面是另一个例子（可以为其添加新属性。）
    
    const a = [];
    a.push('Hello'); // 可执行
    a.length = 0;    // 可执行
    a = ['Dave'];    // 报错
 
 >ES5只有两种声明变量的方法：var命令和function命令。ES6除了添加let和const命令，后面章节还会提到，另外两种声明变量的方法：import命令和class命令。所以，ES6一共有6种声明变量的方法。
 
 
    
` 感想：明白了前辈们对javascript的不断的优化的心情，感谢他们的贡献我将好好细读文档。不服厚爱！   
`    
 
 
 
 
 这里有[var、let、const 区别？](https://www.jianshu.com/p/4e9cd99ecbf5)详尽对比和分析
    

>如有侵权行为，请[点击这里](https://github.com/libaibuzai/libaibuzai/issues)联系我删除

[如发现疑问或者错误点击反馈](https://github.com/libaibuzai/libaibuzai/issues)
