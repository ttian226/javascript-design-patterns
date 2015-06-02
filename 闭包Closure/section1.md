
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