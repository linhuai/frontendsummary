#**前端总结**#


----------


>一、函数使用
>　1.1 函数声明和函数表达式
>　1.2 函数封装（自调用函数、闭包）
>　1.3 函数属性（arguments、callee）
>　1.4 构造函数

>二、函数技巧
>　2.1 改变函数作用域（call/apply/bind）
>　2.2 for循环中的setTimeout问题修复


###**一、函数使用**
　　字符串和数组都是用来存储数据。需要做一些特定的事情的时候，我们就会会用函数封装起来。

####**1.1 函数声明和函数表达式**

　　声明一个函数可以用 **函数声明** 或者 **函数表达式**。

　　函数表达式必须先声明后调用
　　函数内的变量需要使用 var 声明，否则会污染全局变量

    // 函数声明式
    function foo( ) {
        ...some code
    }
    
    // 函数表达式
    var foo = function ( ) {
        ...some code
    } 

    // 变量提升，函数内部的变量如果未使用 var 声明，执行后会导致变量成为全局变量
    var age = 22;
    function showAge() {
        age = 23;
        console.log(age);
    }
    showAge(); // 23
    console.log(age) // 23 | 测试全局变量 age 的值

####**1.2 函数封装（自调用函数、闭包）**

　　实际项目中会引入很多外部JS文件，为了避免彼此命名冲突。通常各自都会对内部函数进行封装。（现在更好的方案是使用模块化）

　　**立即执行函数：**使用立即执行函数，把整个JS文件的代码都封装在内。

　　这样做的好处是不会污染全局变量，可以很好的减少命名冲突。
最后把对外访问的接口挂载在window上。

    (function() {
        //...code
        window.myFunc = myFunc; // 把对外访问的接口挂载在 window 上
    })()


　　**闭包封装：**如果你内部使用的是函数表达式，并且不用var声明，变量依旧会泄露到全局。

    // foo.js
    （funtion(){
        function myFunc() {
            //定义 showName 方法
            function showName( name ) {
                console.log(name);
            }
            
            // 定义 showAge 方法
            function showAge( age ) {
                console.log(age);
            }
            
            //返回一个对象
            return {
                showName: showName,
                showAge: showAge
            }
        }
        window.myFunc = myFunc;
    })




####**1.3 函数属性（arguments、callee）**

　　arguments 是用来存放实参的，可以通过下标访问实参的值。
　　callee指向当前执行的函数，可以在递归的时候用。
　　具体递归场景以及代码，请见MDN。

    callee不能实现尾递归
    callee在ES5严格模式中禁止使用
    caller指向调用当前函数的函数（已废弃）

    function show(name, age) {
        console.log(name);  // foo
        console.log(age);   // 23
        console.log(arguments); // ['foo', 23]
        console.log(arguments.callee) // function show(name, age){}
    }
    show('foo', 23);

####**1.4 构造函数**

　　ES6可以使用Class实现继承，以后在单独总结ES6的时候会提到。
　　推荐一个非常好的ES6系列教程，深入浅出ES6

  ...

###**二、函数技巧**
####**2.1 改变函数作用域（call/apply/bind）**

　　当需要改变上下文的 this 的时候，可以使用 call/apply/bind。

　　三种方法的第一个参数都是 this 的上下文(context);
　　apply 第二个参数是数组，call 和 bind 后面都是接单个参数;
　　call 和 apply 默认会自动执行，bind 需要在后面加()来自动执行;
　　bind是ES5语法，支持IE9+

    /* 以下以数组合并为例子
     * 使用不同的方法之前，请确保 a 和 b 为默认值
     */
     var a = [1, 2, 3];
     var b = [4, 5, 6];
     a.push(b); //直接使用 push(不符合预期)
     console.log(a); // [1, 2, 3, Array[3]]
    
    // call 方法（ 需要逐个输入，不太方法)
    Array.prototype.push.call(a, 4, 5, 6);
    console.log(a); // [1, 2, 3, 4, 5, 6]
    
    // apply 方法( 第二个参数为数组，很适合)
    Array.prototype.push.apply(a, b);
    console.log(a); // [1, 2, 3, 4, 5, 6]
    
    // bind 方法
    Array.prototype.push.bind(a, 4, 5, 6)() // 这里需要添加自动执行
    console.log(a); // [1, 2, 3, 4, 5, 6];
    

####**2.2 for循环中的setTimeout问题修复**

　　setTimeout有自己的this。如果在外层放一个for循环，意味着会一次性执行完，而没有起到延时的作用。
　　解决方案是**使用闭包**。此处主要参考JavaScript 秘密花园

　　setTimeout 是一个定时执行函数。接受两个参数，第一个是执行的函数，第二个是延迟执行的时间。通常用在登陆之后，提示几秒钟之后跳转到首页（现在基本不这样做了）。
　　setInterval 和 setTimeout 基本一致。只是第二个时间参数，表示的是每多长事件执行一次。
第二个参数的时间单位是毫秒（1000表示为1秒）



    // 使用闭包前
    for(var i = 0; i<10; i++) {
        setTimeout(function(){
            console.log(i);
        },1000)  // 10
    }
    // 使用闭包后
    for(var i = 0; i < 10; i++ ) {
        setTimeout((function(i){
            return function(){
                console.log(i); // 0 1 2 3 4 5 6 7 8 9
            }
        })(i);
    }




