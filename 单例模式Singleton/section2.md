#### javascript的全局变量也是一种单例模式

```javascript
var a = {};
```
* 使用全局变量会导致命名空间的污染，例如再全局其它地方又声明了一个变量a

使用命名空间可以减少全局污染

```javascript
var namespace1 = {
	a: function () {

	},
	b: function () {

	}	
};
```

使用闭包

```javascript
var user = (function () {
	// 私有变量
    var name = "tiantian",
        age = 2;

    return {
        getUserInfo: function () {
            console.log("name:" + name + " age:" + age);
        }
    };
})();

// 调用外部接口
user.getUserInfo();
```

#### Example1

利用单例模式创建唯一的登陆窗口

```javascript
// 类LoginWin模拟一个登陆窗口
function LoginWin() {
    this.name = "登陆窗口";
    this.width = 300;
    this.height = 100;
}

LoginWin.prototype.show = function () {
    // ...
}

// 利用闭包创建唯一实例
var createLoginWin = (function () {
	// 由于win被外部引用，所以win在内存中一直存在，用以判断实例是否已经创建
    var win;
    return function () {
        if (!win) {
            win = new LoginWin();
        }
        return win;
    }
})();

// 创建一个新的LoginWin的实例
var win1 = createLoginWin();
// 返回之前创建的实例
var win2 = createLoginWin();
//这里win1,win2执行同一个实例
console.log(win1 === win2) //true
```

#### Example1的不足

* createLoginWin做了两件事，1：判断实例是否存在
* 2：创建LoginWin的实例

```javascript
var createLoginWin = (function () {
    var win;
    return function () {
        if (!win) {
            win = new LoginWin();
        }
        return win;
    }
})();
```

处理单例的逻辑如下：

```javascript
var obj;
if (!obj) {
	obj = xxx;
}
```

现在把这个逻辑抽象出来

```javascript
// getSingle只是处理单例逻辑
var getSingle = function (fn) {
    var instance;
    return function () {
        if (!instance) {
            return instance = fn.apply(this, arguments);
        }
        return instance;
    }
};

// getNewLoginWin返回LoginWin的实例，这个方法是用来作为getSingle的参数传入的
var getNewLoginWin = function () {
    return new LoginWin();
};

// createLoginWin返回getSingle中匿名函数的引用
var createLoginWin = getSingle(getNewLoginWin);

// 调用匿名函数，返回LoginWin的实例。
var win1 = createLoginWin();
var win2 = createLoginWin();

console.log(win1 === win2) //true
```

再利用上面的单例模型创建一个消息窗口

```javascript
// 类MsgWin模拟消息窗口，带一个参数msg
function MsgWin(msg) {
    this.name = "消息窗口";
    this.width = 100;
    this.height = 500;
    this.msg = msg;
}

MsgWin.prototype.show = function () {
    // ...
};

// getNewMsgWin返回一个MsgWin的实例
var getNewMsgWin = function (msg) {
    return new MsgWin(msg);
};

// 利用getSingle返回匿名函数的引用
var createMsgWin = getSingle(getNewMsgWin);

// 这里通过给匿名函数传参以达到给构造函数传参的目的，
// 因为fn.apply(this, arguments)。这里的arguments为匿名函数的参数数组
var win1 = createMsgWin("abc");
var win2 = createMsgWin("efg");

console.log(win1 === win2) //true
console.log(win2.msg); //abc
```



