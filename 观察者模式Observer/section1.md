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

