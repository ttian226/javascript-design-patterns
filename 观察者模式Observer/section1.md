#### 观察者模式（发布订阅模式）

发布订阅模式让发布者和订阅者松耦合联系到一起：当新增订阅对象者，发布者代码无需修改；同时当发布者代码修改时也不会影响之前的订阅者。

例子：一个新闻发布场景，小李和小王订阅自己喜欢的新闻，当有新的新闻发布时，会实时通知他们。

```javascript
var newsPoster = {};  //定义新闻发布对象

newsPoster.orderlist = [];   //缓存列表，存放订阅者的回调函数

// 订阅新闻
newsPoster.order = function(fn) {
    this.orderlist.push(fn);   //把订阅函数添加到缓存列表
};

// 发布新闻
newsPoster.trigger = function() {
    for (var i = 0; i < this.orderlist.length; i++) {
        this.orderlist[i].apply(this, arguments);
    }
};
```

测试代码：

```javascript
// 小李订阅函数
var xiaoLiOrder = function(title, content) {
    console.log('xiaoli receive: [title]' + title + ' [content]' + content);
};

// 小王订阅函数
var xiaoWangOrder = function(title, content) {
    console.log('xiaoWang receive: [title]' + title + ' [content]' + content);
};

// 订阅新闻，把函数分别添加到缓存列表
newsPoster.order(xiaoLiOrder);
newsPoster.order(xiaoWangOrder);

// 发布新闻，通知所有订阅者
newsPoster.trigger('sports news', 'some contents...');

// output
// xiaoli receive: [title]sports news [content]some contents...
// xiaoWang receive: [title]sports news [content]some contents...
```

以上的例子模拟了一个简单的发布-订阅模式，当仍存在问题：*无论发布什么新闻，订阅者都会收到相同的新闻*，现在的需求是订阅者只会收到自己喜欢的新闻。

例子：修改发布-订阅模型

```javascript
var newsPoster = {};  //定义新闻发布对象

newsPoster.orderlist = [];   //缓存列表，存放订阅者的回调函数

// 订阅新闻
newsPoster.order = function(key, fn) {
    if (!this.orderlist[key]) {
        this.orderlist[key] = [];
    }
    this.orderlist[key].push(fn);
};

// 发布新闻
newsPoster.trigger = function() {
    var key = Array.prototype.shift.call(arguments),    //取出第一个参数，作为key
        fns = this.orderlist[key];  //对应key的缓存函数列表

    // 如果没有订阅函数则返回
    if (!fns || fns.length === 0) {
        return false;
    }

    // 遍历缓存列表执行订阅函数
    for (var i = 0; i < fns.length; i++) {
        fns[i].apply(this, arguments);
    }
};
```

测试代码：

```javascript
// 小李订阅函数
var xiaoLiOrder = function(title, content) {
    console.log('xiaoli receive: [title]' + title + ' [content]' + content);
};

// 小王订阅函数
var xiaoWangOrder = function(title, content) {
    console.log('xiaoWang receive: [title]' + title + ' [content]' + content);
};

// 订阅新闻，把函数分别添加到缓存列表
newsPoster.order('sport', xiaoLiOrder);     // 小李订阅体育新闻
newsPoster.order('tech', xiaoWangOrder);   // 小王订阅科技新闻

newsPoster.trigger('sport', 'sports news', 'some contents...'); // 发布一条体育新闻
newsPoster.trigger('tech', 'tech news', 'some contents...');  // 发布一条科技新闻

// output
// xiaoli receive: [title]sports news [content]some contents...
// xiaoWang receive: [title]tech news [content]some contents...
```

#### 把发布订阅模式抽象出来

对上面的例子的改进版：把发布订阅模块抽象到一个observer对象中

```javascript
// 发布订阅模块
var observer = {
    // 缓存列表
    orderlist: [],
    // 订阅方法
    order: function(key, fn) {
        if (!this.orderlist[key]) {
            this.orderlist[key] = [];
        }
        this.orderlist[key].push(fn);
    },
    // 发布方法
    trigger: function() {
        var key = Array.prototype.shift.call(arguments),
            fns = this.orderlist[key];

        if (!fns || fns.length === 0) {
            return false;
        }

        for (var i = 0; i < fns.length; i++) {
            fns[i].apply(this, arguments);
        }
    }
};

// 安装发布-订阅功能，把observer属性依次赋予新对象
var installEvent = function(obj) {
    for (var i in observer) {
        if (observer.hasOwnProperty(i)) {
            obj[i] = observer[i];
        }
    }
};
```

模块的使用：

```javascript
// 创建一个新的新闻发布对象newsPoster
var newsPoster = {};

// 给newsPoster对象安装发布-订阅功能
installEvent(newsPoster);

// 下面代码同上

// 小李订阅函数
var xiaoLiOrder = function(title, content) {
    console.log('xiaoli receive: [title]' + title + ' [content]' + content);
};

// 小王订阅函数
var xiaoWangOrder = function(title, content) {
    console.log('xiaoWang receive: [title]' + title + ' [content]' + content);
};

// 订阅新闻，把函数分别添加到缓存列表
newsPoster.order('sport', xiaoLiOrder);     // 小李订阅体育新闻
newsPoster.order('tech', xiaoWangOrder);   // 小王订阅科技新闻

newsPoster.trigger('sport', 'sports news', 'some contents...'); // 发布一条体育新闻
newsPoster.trigger('tech', 'tech news', 'some contents...');  // 发布一条科技新闻

// output
// xiaoli receive: [title]sports news [content]some contents...
// xiaoWang receive: [title]tech news [content]some contents...
```

#### 取消订阅

```javascript
observer.remove = function() {
    var fns = this.orderlist[key];

    // key对应的消息没有被订阅，直接返回
    if (!fns) {
        return false;
    }

    // 不传fn，表示取消key对应消息的所有订阅
    if (!fn) {
        fns && (fns.length = 0);
    } else {
        for (var i = 0; i < fns.length; i++) {
            var _fn = fns[i];
            if (_fn === fn) {
                fns.splice(i, 1);   //删除订阅者的回调函数
            }
        }
    }
};
```

测试代码：

```javascript
// 小李取消订阅
newsPoster.remove('sport', xiaoLiOrder);

newsPoster.trigger('sport', 'sports news', 'some contents...'); // 发布一条体育新闻
newsPoster.trigger('tech', 'tech news', 'some contents...');  // 发布一条科技新闻

// output
// xiaoWang receive: [title]tech news [content]some contents...
```
