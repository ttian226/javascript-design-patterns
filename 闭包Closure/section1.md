
#### Example1

* 立即调用表达式会立即执行，不需要test()调用
* 显示调用test()只是会调用一个匿名函数
* 局部变量i不会随着调用的结束而被回收，而是一直保存在内存中

```javascript
var test = (function () {
    console.log("test func");
    var i = 0;
    return function () {
        console.log(i);
        return i++;
    }
})();

console.log("........");
test();		//0
test();		//1
test();		//2

// output
// test func
// ........
// 0
// 1
// 2
```

#### Example2

* test不是立即调用的，需要显示调用test()返回匿名函数的引用，如：var a = test()，这时a指向匿名函数
* a()调用这个匿名函数，局部变量i对于a来说一直会常驻内存中
* var b = test()，再次调用test，b会返回一个新的匿名函数的拷贝，并且i对于b也是唯一的

```javascript
var test = function () {
    console.log("test func");
    var i = 0;
    return function () {
        console.log(i);
        return i++;
    }
};

console.log("........");
var a = test();
a();	//0
a();	//1
a();	//2

var b = test()
b();	//0

// output
// ........
// test func
// 0
// 1
// 2
// test func
// 0
```

#### Example3

* 循环结束后i=3

```javascript
var arr = [1, 2, 3];
var obj = {};

var test = function () {
    for (var i=0; i<arr.length; i++) {
        obj[i] = function () {
            console.log(i);
        };
    }
}

test();

var fn0 = obj[0];
var fn1 = obj[1];
var fn2 = obj[2];

fn0();   //3
fn1();   //3
fn2();   //3
```

* 在for循环里创建了一个立即调用函数表达式
* fn0,fn1,fn2分别指向了匿名函数的引用
* fn0(),fn1(),fn2()都访问了i(这个i是位于这个匿名函数的上层作用域链,它会被保存在内存中，对于每一个函数引用来说i是唯一的)

```javascript
var arr = [1, 2, 3];
var obj = {};

var test = function () {
    for (var i=0; i<arr.length; i++) {
        (function (i) {
            obj[i] = function () {
                console.log(i);
            };
        })(i);  //i作为参数传给立即调用函数
    }
};

test();

var fn0 = obj[0];
var fn1 = obj[1];
var fn2 = obj[2];

fn0();   //0
fn1();   //1
fn2();   //2
```


```javascript
var arr = [1, 2, 3];
var obj = {};

var test = function () {
    for (var i=0; i<arr.length; i++) {
        (function (i) {
            obj[i] = function () {
                console.log(i++);
            };
        })(i);  //i作为参数传给立即调用函数
    }
};

test();

var fn0 = obj[0];
var fn1 = obj[1];
var fn2 = obj[2];

// 两次fn0()访问同一个内存中的i值
fn0();  //0
fn0();  //1

// 两次fn1()访问同一个内存中的i值
fn1();  //1
fn1();  //2

// 两次fn2()访问同一个内存中的i值
fn2();  //2
fn2();  //3
```