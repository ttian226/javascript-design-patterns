#### call和apply的区别

`call`和`apply`唯一的区别就是传入参数的形式不同。

`apply`接收两个参数，第一个参数指定了函数体内`this`对象的指向，第二个参数为一个带下标的集合，这个集合可以是数组，也可以为类数组。apply方法把这个集合中的元素作为参数传递给被调用的函数。

```javascript
function add(x, y) {
    return x + y;
}

add.apply(null, [1, 2]);
```

`call`使用的参数数量不固定，第一个参数同`apply`相同。从第二个参数开始，每个参数被依次传入函数。

```javascript
add.call(null, 1, 2);
```

无论是`apply`还是`call`当第一个参数为`null`时，函数体内的`this`会指向默认的宿主对象，在浏览器中则是`window`

