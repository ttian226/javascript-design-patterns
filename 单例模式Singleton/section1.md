#### 单例模式

保证一个类仅有一个实例，并提供一个访问它的全局访问点

#### Example1

```javascript
var Singleton = function (name) {
    this.name = name;
    this.instance = null;
};

Singleton.prototype.getName = function () {
    console.log(this.name);
};

Singleton.getInstance = function (name) {
    if (!this.instance) {
        this.instance = new Singleton(name);
    }
    return this.instance;
};

var a = Singleton.getInstance("sven1");
var b = Singleton.getInstance("sven2");

console.log(a === b);	//true
b.getName();	//sven1
```

#### Example2

```javascript
var Singleton = function (name) {
    this.name = name;
};

Singleton.prototype.getName = function () {
    console.log(this.name);
};

// 立即执行表达式，把instance通过闭包隐藏起来
Singleton.getInstance = (function () {
    var instance = null;

    return function (name) {
        if (!instance) {
            instance = new Singleton(name);
        }
        return instance;
    };
})();

var a = Singleton.getInstance("sven1");
var b = Singleton.getInstance("sven2");

console.log(a === b);	//true
b.getName();	//sven1
```
* 以上两段代码都不用显示的new一个对象。增加类的不透明性，Singleton类的使用者必须知道这是一个单例类
* 两段代码的意义并不大

#### Example3

```javascript
var Create = (function () {
    var instance;

    var CreateDiv = function (html) {
        if (instance) {
            return instance;
        }
        this.html = html;
        this.init();
        return instance = this;
    };

    CreateDiv.prototype.init = function () {
        var div = document.createElement("div");
        div.innerHTML = this.html;
        document.body.appendChild(div);
    };

    return CreateDiv;
})();


var a = new Create("sven1");
var b = new Create("sven2");

console.log(a === b);   //true
```

需要显示new对象，结果是只创建了一个dom对象:

```html
<div>sven1</div>
```

* 使用自执行的匿名函数和闭包，让这个匿名函数返回真正的Singleton构造方法，增加了程序的复杂度，阅读起来不舒服
* CreateDiv的够着函数实际上负责了两件事情，第一是创建对象和执行init方法，第二是保证只有一个对象。

#### Example4

```javascript
var CreateDiv = function (html) {
    this.html = html;
    this.init();
};

CreateDiv.prototype.init = function () {
    var div = document.createElement("div");
    div.innerHTML = this.html;
    document.body.appendChild(div);
};

var ProxySingletonCreateDiv = (function () {
    var instance;

    return function (html) {
        if (!instance) {
            instance = new CreateDiv(html);
        }
        return instance;
    };
})();

var a = new ProxySingletonCreateDiv("sven1");
var b = new ProxySingletonCreateDiv("sven2");

console.log(a === b);   //true
```

* 把负责管理单例的逻辑移到了代理类ProxySingletonCreateDiv中
* CreateDiv变成了一个普通的类，它和代理类组合起来达到了单例的效果。
