#### call和apply的区别

`call`和`apply`唯一的区别就是传入参数的形式不同。

`apply`接收两个参数，第一个参数指定了函数体内`this`对象的指向，第二个参数为一个带下标的集合，这个集合可以是数组，也可以为类数组。apply方法把这个集合中的元素作为参数传递给被调用的函数。

```javascript
function add(x, y) {
    return x + y;
}

add.apply(null, [1, 2]);
```

`call`使用的参数数量不固定，第一个参数同`apply`相同。从第二个参数开始，每个参数被依次传入函数。

```javascript
add.call(null, 1, 2);
```

无论是`apply`还是`call`当第一个参数为`null`时，函数体内的`this`会指向默认的宿主对象，在浏览器中则是`window`

#### call和apply的用途

例：改变this指向

```javascript
window.name = 'window';

var obj1 = {
    name: 'wangxu'
};

var obj2 = {
    name: 'tiantian'
};

function getName() {
    console.log(this.name);
}

// 函数内部this指向window，this.name = window.name
getName();  //'window'

// 函数内部this指向obj1，this.name = obj1.name
getName.call(obj1); //'wangxu'

// 函数内部this指向obj2，this.name = obj2.name
getName.call(obj2); //'tiantian'
```

例：

```html
<body>
    <div id="div1">div1</div>
</body>
```

```javascript
window.id = 'winid';

document.getElementById('div1').onclick = function() {
    
    var callback = function() {
        // 这里的this指向div，而不是window
        console.log(this.id);   //'div1'
    };

    // 通过call把callback函数内部this指向div
    callback.call(this);
};
```

例：借用其它对象的方法

```javascript
// 函数内部的arguments是类数组对象
function test() {
    console.log(arguments);     //[1, 2]
    console.log(arguments instanceof Array);    //false

    // 这里如果直接调用push方法会报错，因为arguments对象没有原型方法push
    //arguments.push(3);
}

test(1, 2);
```

```javascript
function test() {
    Array.prototype.push.call(arguments, 3);
    console.log(arguments);     //[1, 2, 3]
}

test(1, 2);
```

例子：模拟`Function.prototype.bind`

```javascript
// 创建原型方法bindEx
Function.prototype.bindEx = function(context) {
    // 创建self变量保存this对象
    var self = this;

    // 返回一个新的函数
    return function() {
        // 用context对象来修正函数内部的this
        return self.apply(context, arguments);
    };
};

// 使用bindEx
var obj = {
    name: 'wangxu'
};

function getName() {
    return this.name;
}

var func = getName.bindEx(obj);
var name = func(); 
console.log(name);  //'wangxu'
```

例子：模拟`Function.prototype.bind`，并对参数做些处理

```javascript
// 创建原型方法bindEx
Function.prototype.bindEx = function() {
    // 创建self变量保存this对象
    var self = this,

        // 从arguments中取第一个参数作为上下文对象
        context = [].shift.call(arguments),

        // 去剩余的部分作为参数数组
        args = [].slice.call(arguments);

    // 返回一个新的函数
    return function() {
        // newArr来保存新函数的参数数组
        var newArr = [].slice.call(arguments);

        // 合并原函数参数数组到argArr
        var argArr = args.concat(newArr);

        // 用context对象来修正函数内部的this
        return self.apply(context, argArr);
    };
};

window.name = 'win';

var obj = {
    name: 'wangxu'
};

function getName(a, b, c, d) {
    var arr = [a, b, c, d];
    console.log(this.name);
    console.log(arr);
}

var func = getName.bindEx(obj, 1, 2);
func(3, 4);

// output
// 'wangxu'
// [1, 2, 3, 4]
```

