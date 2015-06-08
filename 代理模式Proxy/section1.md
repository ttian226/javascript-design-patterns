#### 代理模式实现图片预加载


```javascript
// 创建一个本体对象myImage，对外提供一个接口setSrc，给img标签设置src属性
var myImage = (function () {
	var imgNode = document.createElement("img");
	document.body.appendChild(imgNode);

	return {
		setSrc: function (src) {
			imgNode.src = src;
		}
	};
})();

// 调用接口直接设置图片src属性
myImage.setSrc("http://fabu.07073.com/uploads/images/37edc91dfe0b5906841ee4365444c869.jpg");
```

* 调低网速，可以看到在图片需要一段很长时间才能显示完全

设置代理对象，使得图片被真正加载之前将显示一张占位图片

```javascript
// 创建一个代理对象
var proxyImage = (function () {
	var img = new Image();

	img.onload = function () {
		// load完成后会再次调用本体对象的setSrc接口设置src
		myImage.setSrc(this.src);
	};

	return {
		setSrc: function (src) {
			// 调用本体对象的setSrc接口设置预加载图片
			myImage.setSrc("lazy3.png");
			// 设置Image对象的src属性，会触发Image对象的onload事件
			img.src = src;
		}
	};
})();

// 通过调用代理对象的setSrc实现预加载
proxyImage.setSrc("http://fabu.07073.com/uploads/images/37edc91dfe0b5906841ee4365444c869.jpg");
```

#### 不使用代理模式

```javascript
var MyImage = (function () {
	var imgNode = document.createElement("img");
	document.body.appendChild(imgNode);

	var img = new Image();
	img.onload = function () {
		imgNode.src = this.src;
	};

	return {
		setSrc: function (src) {
			imgNode.src = "lazy3.png";
			img.src = src;
		}
	};
})();

MyImage.setSrc("http://fabu.07073.com/uploads/images/37edc91dfe0b5906841ee4365444c869.jpg");
```

单一职责原则：
就一个类（对象，函数）而言，应该仅有一个引起它变化的原因。如果一个对象承担了多项职责，就意味着这个对象将变得巨大，引起它变化的原因可能会有多个。面向对象设计鼓励将行为分布到细粒度的对象之中，如果一个对象的职责过多，等于把这些职责耦合到了一起，当变化发生时，设计可能会遭到意外的破坏。

上段代码中MyImage对象除了负责给img节点设置src外，还要负责预加载图片。我们在处理其中的一个职责时，有可能因为其强耦合性影响另外一个职责的实现。

通过代理模式，给img节点设置src和图片预加载被分别隔离在两个对象里，它们各自的变化不影响到对方。并且由于它们接口的一致性，如果我们不需要预加载，直接调用本体接口也可以实现。