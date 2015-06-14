#### 一个简单的迭代器实现

* 假设有each方法，调用方式如下，接收两个参数：一个数组，一个回调函数
* 回调函数包含两个参数：i索引，n索引对应的value

```javascript
each([1, 2, 3], function(i, n) {
    console.log(i + ':' + n);
});
```
完整的代码如下

```javascript
var each = function(arr, callback) {
    for (var i = 0; i < arr.length; i++) {
        callback.call(arr[i], i, arr[i]);
    }
};

each([1, 2, 3], function(i, n) {
    console.log(i + ':' + n);
});

// output
// 0:1
// 1:2
// 2:3
```

#### 外部迭代器

比较两个数组是否相等

```javascript
var Iterator = function(obj) {
    var current = 0;

    var next = function() {
        current++;
    };

    var isDone = function() {
        return current >= obj.length;
    };

    var getCurrItem = function() {
        return obj[current];
    };

    return {
        next: next,
        isDone: isDone,
        getCurrItem: getCurrItem
    };
};

var compare = function(iterator1, iterator2) {
    while (!iterator1.isDone() && !iterator2.isDone()) {
        if (iterator1.getCurrItem() !== iterator2.getCurrItem()) {
            console.log('iterator1和iterator2不相等');
            return;
        }
        iterator1.next();
        iterator2.next();
    }
    console.log('iterator1和iterator2相等');
};

var iterator1 = Iterator([1, 2, 3]);
var iterator2 = Iterator([1, 2, 3]);

compare(iterator1, iterator2);
```

#### jQuery.each迭代器的实现

$.each不仅仅可以迭代数组也可以迭代对象

```javascript
jQuery.each = function(obj, callback, args) {
    var value,
        i = 0,
        length = obj.length,
        isArray = Array.isArray(obj);

    if (isArray) {
        // 数组，类数组对象
        for (; i < length; i++) {
            //callback(索引, 值)
            value = callback.call(obj[i], i, obj[i]);

            if (value === false) {
                break;
            }
        }
    } else {
        // 对象
        for (i in obj) {
            //callback(属性, 值)
            value = callback.call(obj[i], i, obj[i]);

            if (value === false) {
                break;
            }
        }
    }

    return obj;
};
```

#### 终止迭代器

上面$.each中约定如果回调函数的执行结果返回false，则提前终止循环。

```javascript
if (value === false) {
    break;
}
```

改写之前的例子

```javascript
var each = function(arr, callback) {
    for (var i = 0; i < arr.length; i++) {
        var value = callback.call(arr[i], i, arr[i]);
        if (value === false) {
            break;
        }
    }
};

each([1, 2, 3, 4, 5], function(i, n) {
    if (i > 2) {
        return false;
    }
    console.log(i + ':' + n);
});
```

