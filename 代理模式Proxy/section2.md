#### 缓存代理

缓存代理可以为一些开销大的运算结果提供暂时的缓存，下次运算时，如果传递进来的参数和之前的一致，则可以直接返回前面的存储结果

一个计算乘积的例子：

不使用代理

```javascript
var mult = function () {
	var a = 1;
	for (var i = 0; i < arguments.length; i++) {
		a = a * arguments[i];
	}
	return a;
};

mult(2, 3);		//6
mult(2, 3, 4);	//24
```

使用缓存代理

```javascript
var proxyMult = (function () {
	var cache = {};
	return function () {
		var args = Array.prototype.join.call(arguments, ",");
		if (args in cache) {
			return cache[args];
		}
		return cache[args] = mult.apply(this, arguments);
	};
})();

proxyMult(1, 2, 3, 4);	//24
proxyMult(1, 2, 3, 4);	//24 这次读取缓存
```

#### 缓存代理也可以用于ajax请求数据

#### 通过代理合并http请求

不使用缓存时，每次勾选checkbox给服务器发送一次请求

```html
<body>
	<input type="checkbox" id="1"></input>1
	<input type="checkbox" id="2"></input>2
	<input type="checkbox" id="3"></input>3
	<input type="checkbox" id="4"></input>4
	<input type="checkbox" id="5"></input>5
	<input type="checkbox" id="6"></input>6
	<input type="checkbox" id="7"></input>7
	<input type="checkbox" id="8"></input>8
	<input type="checkbox" id="9"></input>9
</body>
```

```javascript
var synchronousFile = function(id) {
    console.log('开始同步文件，id为：' + id);
};

var checkbox = document.getElementsByTagName("input");

for (var i = 0; i < checkbox.length; i++) {
	var c = checkbox[i];
	c.onclick = function () {
		if (this.checked === true) {
			synchronousFile(this.id);
		}
	};
}
```
* 现在每次选中checkbox的时候都会给服务端发送一次请求，可以预见，如此频繁的网络请求会带来相当大的开销
* 解决方案，通过一个代理函数proxySynchronousFile来收集一段时间内的请求，最后一次性发送给服务器。
* 比如等待2秒后发送一次数据，如果不是对实时性要求非常高的系统，2秒的延迟不会带来太大的副作用，却能大大减轻服务器的压力。

```javascript
// ids可以为单个id，也可是由逗号分隔的id
var synchronousFile = function(ids) {
    console.log('开始同步文件，id为：' + ids);
};

// 通过闭包，timer对所有的调用者只在内存中维持一份
var proxySynchronousFile = (function() {
    var cache = [],
        timer;

    return function(id) {
        cache.push(id);
        if (timer) {
            return;
        }

        timer = setTimeout(function() {
            var ids = cache.join(',');
            synchronousFile(ids);
            clearTimeout(timer);
            timer = null;
            cache.length = 0;
        }, 5000);
    };
})();

var checkbox = document.getElementsByTagName('input');

for (var i = 0; i < checkbox.length; i++) {
    var c = checkbox[i];
    c.onclick = function() {
        if (this.checked === true) {
            proxySynchronousFile(this.id);
        }
    };
}
```
