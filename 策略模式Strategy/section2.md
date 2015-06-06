#### 一个表单验证的例子

```html
<form action="register" id="registerForm" method="post">
	请输入用户名：<input type="text" name="username" />
	请输入密码：<input type="text" name="password" />
	请输入手机号码：<input type="text" name="phonenumber" />
	<button>提交</button>
</form>
```
通常验证表单的方式

```javascript
var registerForm = document.getElementById("registerForm");

registerForm.onsubmit = function () {
	if (registerForm.username.value === "") {
		alert("用户名不能为空");
		return false;
	}

	if (registerForm.password.value.length < 6) {
		alert("密码长度不能少于6位");
		return false;
	}

	if (!/^1[3|5|8][0-9]{9}$/.test(registerForm.phonenumber.value)) {
		alert("手机号码格式不正确");
		return false;
	}
}
```

#### 使用策略模式重构表单校验

```javascript
// 策略对象
var strategies = {
	isNonEmpty: function (value, errorMsg) {
		if (value === "") {
			return errorMsg;
		}
	},
	minLength: function (value, length, errorMsg) {
		if (value.length < length) {
			return errorMsg;
		}
	},
	isMobile: function (value, errorMsg) {
		if (!/^1[3|5|8][0-9]{9}$/.test(value)) {
			return errorMsg;
		}
	}
};

// 校验类
var Validator = function () {
	// 用来存储策略列表
	this.cache = [];
};

Validator.prototype.add = function (dom, rule, errorMsg) {
	// 把rule转化为数组如["isNonEmpty"]或["minLength", length]
	var ary = rule.split(":");
	
	// 把匿名函数加入cache中
	this.cache.push(function () {
		// 取得strategy名，此时ary为[]或[length]
		var strategy = ary.shift();
		// 把input value放入ary最前面，此时ary为[dom.value]或[dom.value, length]
		ary.unshift(dom.value);
		// 把errormsg加入ary最后面，此时ary为[dom.value, errormsg]或[dom.value, length, errormsg]
		ary.push(errorMsg);
		// 执行策略函数
		return strategies[strategy].apply(dom, ary);
	});
};

Validator.prototype.start = function () {
	// 遍历cache执行策略函数
	for (var i = 0; i < this.cache.length; i++) {
		var validatorFunc = this.cache[i],
			msg = validatorFunc();
		if (msg) {
			return msg;
		}
	}
};
// 使用
var registerForm = document.getElementById("registerForm");

var validataFunc = function () {
	// 创建一个校验类对象
	var validator = new Validator();

	// 添加校验规则
	validator.add(registerForm.username, "isNonEmpty", "用户名不能为空");
	validator.add(registerForm.password, "minLength:6", "密码长度不能少于6位");
	validator.add(registerForm.phonenumber, "isMobile", "手机号码格式不正确");

	var errorMsg = validator.start();
	return errorMsg;
};

registerForm.onsubmit = function () {
	var errorMsg = validataFunc();
	if (errorMsg) {
		alert(errorMsg);
		return false;
	}
};
```

#### 进一步改进

* 之前的表单校验，一个输入框只能对应一种校验规则，通过改进可以对一个输入框添加多个校验规则。
* 例如对用户的校验既检查是否为空，又检查长度不小于10

```javascript
// 策略对象
var strategies = {
	isNonEmpty: function (value, errorMsg) {
		if (value === "") {
			return errorMsg;
		}
	},
	minLength: function (value, length, errorMsg) {
		if (value.length < length) {
			return errorMsg;
		}
	},
	isMobile: function (value, errorMsg) {
		if (!/^1[3|5|8][0-9]{9}$/.test(value)) {
			return errorMsg;
		}
	}
};

// 校验类
var Validator = function () {
	// 用来存储策略列表
	this.cache = [];
};

Validator.prototype.add = function (dom, rules) {
	var i = 0,
		cnt = rules.length,
		self = this;

	for (; i < cnt; i++) {
		var rule = rules[i];
		// 这里使用闭包吧rule封闭起来，具体原因参考闭包（section1 example4）
		(function (rule) {
			var strategyAry = rule.strategy.split(":"),
				errorMsg = rule.errorMsg;

			self.cache.push(function () {
				var strategy = strategyAry.shift();
				strategyAry.unshift(dom.value);
				strategyAry.push(errorMsg);
				return strategies[strategy].apply(dom, strategyAry);
			});
		})(rule);
	}
};

Validator.prototype.start = function () {
	// 遍历cache执行策略函数
	for (var i = 0; i < this.cache.length; i++) {
		var validatorFunc = this.cache[i],
			msg = validatorFunc();
		if (msg) {
			return msg;
		}
	}
};

// 使用
var registerForm = document.getElementById("registerForm");

var validataFunc = function () {
	// 创建一个校验类对象
	var validator = new Validator();

	// 添加校验规则
	// 这里对username添加两种校验规则
	validator.add(registerForm.username, [{
		strategy: "isNonEmpty",
		errorMsg: "用户名不能为空"
	}, {
		strategy: "minLength:10",
		errorMsg: "用户名长度不能小于10位"
	}]);

	validator.add(registerForm.password, [{
		strategy: "minLength:6",
		errorMsg: "密码长度不能小于6位"
	}]);

	validator.add(registerForm.phonenumber, [{
		strategy: "isMobile",
		errorMsg: "手机号码格式不正确"
	}]);

	var errorMsg = validator.start();
	return errorMsg;
};


registerForm.onsubmit = function () {
	var errorMsg = validataFunc();
	if (errorMsg) {
		alert(errorMsg);
		return false;
	}
};
```