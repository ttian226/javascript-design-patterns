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
		myImage.setSrc(this.src);
	};

	return {
		setSrc: function (src) {
			myImage.setSrc("lazy3.png");
			img.src = src;
		}
	};
})();

// 通过调用代理对象的setSrc实现预加载
proxyImage.setSrc("http://fabu.07073.com/uploads/images/37edc91dfe0b5906841ee4365444c869.jpg");
```