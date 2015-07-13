#### 作为对象的方法调用

当函数作为对象的方法被调用时，`this`指向该对象。

```javascript
var myObject = {
    name: 'wangxu',
    getName: function() {
        return this.name;
    }
};

var name = myObject.getName();
console.log(name);  //'wangxu'
```

#### 作为普通函数调用

当函数不作为对象的属性被调用时，即`普通函数模式`，此时的`this`总是指向全局对象。
在浏览器的Javascript里，这个全局对象是`windows`对象。

例子1：

```javascript
window.name = 'globalName';

var getName = function() {
    return this.name;
};

var name = getName();
console.log(name);  //'globalName'
```
例子2：

```javascript
window.name = 'globalName';

var myObject = {
    name: 'wangxu',
    getName: function() {
        return this.name;
    }
};

// 在全局作用域中定义getName指向myObject.getName的引用
var getName = myObject.getName;

// 由于getName在全局作用域中被调用，所以函数内部的this为windows对象，而不是myObject对象
var name = getName();
console.log(name);  //'globalName'
```

例子3：

```html
<body>
    <div id="div1">div1</div>
</body>
```

```javascript
window.id = 'winid';

// 注：click事件需要在div节点生成后调用。
document.getElementById('div1').onclick = function() {
    // 在这里this指向div节点
    console.log(this.id);   //'div1'

    // 创建that保存div节点的引用，目的是在callback中使用。
    var that = this;

    // callback函数中的this指向windows对象
    var callback = function() {

        // 由于this指向windows，this.id = winid
        console.log(this.id);   //'winid'

        // 通过that访问到div节点的id
        console.log(that.id);
    };
    callback();
};
```

#### 构造器调用

Javascript可以从构造器中创建对象。当用`new`运算符调用函数时，该函数会返回一个对象。通常情况下，构造器里的`this`就指向这个返回的对象。

例1：

```javascript
var MyClass = function() {
    this.name = 'wangxu';
};

var obj = new MyClass();
var name = obj.name;
console.log(name);  //'wangxu'
```

例2：

```javascript
// 构造器显式的返回一个对象
var MyClass = function() {
    this.name = 'wangxu';
    return {
        name: 'tiantian'
    };
};

var obj = new MyClass();

// 这里的obj.name不是this.name，而是返回对象的name
var name = obj.name;
console.log(name);
```

例3：

```javascript
var MyClass = function() {
    this.name = 'wangxu';
    return {
        age: 35
    };
};

var obj = new MyClass();

// 由于返回的对象没有name属性，所以obj.name返回undefined
var name = obj.name;
console.log(name);  //undefined
```

#### `Function.prototype.call`或`Function.prototype.apply`调用

通过`Function.prototype.call`或`Function.prototype.apply`可以动态的改变传入函数的this。

```javascript
var obj1 = {
    name: 'wangxu',
    getName: function() {
        return this.name;
    }
};

var obj2 = {
    name: 'tiantian'
};

var name1 = obj1.getName();
console.log(name1);     //'wangxu'

var name2 = obj1.getName.apply(obj2);
console.log(name2);     //'tiantian'
```


```javascript
document.getElementById = (function(func) {
    return function() {
        return func.apply(document, arguments);
    };
})(document.getElementById);

var getId = document.getElementById;

document.addEventListener('DOMContentLoaded', complete, false);

function complete(e) {
    var div = getId('div1');
    console.log(div);
}
```
