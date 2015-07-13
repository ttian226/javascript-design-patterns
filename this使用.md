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

// click事件需要在div节点生成后调用。
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
