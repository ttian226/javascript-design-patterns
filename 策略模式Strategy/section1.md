#### 一个计算奖金的例子

奖金 = 绩效 * 工资数

通常的代码实现：

```javascript
var calculateBonus = function (performanceLevel, salary) {
	if (performanceLevel === "S") {
		return salary * 4;
	}

	if (performanceLevel === "A") {
		return salary * 3;
	}

	if (performanceLevel === "B") {
		return salary * 2;
	}
};

calculateBonus("B", 20000);		//40000
calculateBonus("S", 6000);		//24000
```

* 函数比较庞大
* 函数缺乏弹性，如果增加了一种新的绩效等级C，需要修改函数内部，违反开放-封闭原则
* 复用性差


#### 使用策略模式重构代码

模仿传统面向对象语言中的实现

```javascript

// 策略类
var performanceS = function () {};

performanceS.prototype.calculate = function (salary) {
	return salary * 4;
}

var performanceA = function () {};

performanceA.prototype.calculate = function (salary) {
	return salary * 3;
};

var performanceB = function () {};

performanceB.prototype.calculate = function (salary) {
	return salary * 2;
};

// 奖金类
var Bonus = function () {
	this.salary = null;
	this.strategy = null;
};

Bonus.prototype.setSalary = function (salary) {
	this.salary = salary;
};

Bonus.prototype.setStrategy = function (strategy) {
	this.strategy = strategy;
};

Bonus.prototype.getBonus = function () {
	return this.strategy.calculate(this.salary);
};

// 使用
var bonus = new Bonus();

// 设置工资
bonus.setSalary(10000);
// 设置策略对象
bonus.setStrategy(new performanceS());
console.log(bonus.getBonus());	//40000

// 设置新的策略对象
bonus.setStrategy(new performanceA());
console.log(bonus.getBonus());	//30000
```

使用javascript版本的策略模式

```javascript
// 定义策略对象
var strategies = {
	S: function (salary) {
		return salary * 4;
	},
	A: function (salary) {
		return salary * 3;
	},
	B: function (salary) {
		return salary * 2;
	}
};

// 函数calculateBonus接收数据调用策略对象
var calculateBonus = function (level, salary) {
	return strategies[level](salary);
};

calculateBonus("S", 20000);  //80000
calculateBonus("A", 10000);  //30000
```