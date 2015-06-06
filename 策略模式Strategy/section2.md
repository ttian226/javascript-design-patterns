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