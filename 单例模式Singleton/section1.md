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
以上两段代码执行结果完全相同。

