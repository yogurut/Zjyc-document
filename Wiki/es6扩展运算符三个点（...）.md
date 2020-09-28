#es6  扩展运算符 三个点（...）
# 1  含义

扩展运算符（ spread ）是三个点（...）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。



```
console.log(...[1, 2, 3])
// 1 2 3
console.log(1, ...[2, 3, 4], 5)
// 1 2 3 4 5
[...document.querySelectorAll('div')]
```





```
function push(array, ...items) {
array.push(...items);
}
function add(x, y) {
return x + y;
}
var numbers = [4, 38];
add(...numbers) // 42
```



扩展运算符与正常的函数参数可以结合使用，非常灵活。



```
function f(v, w, x, y, z) { }
var args = [0, 1];
f(-1, ...args, 2, ...[3]);
```





# 2  替代数组的 apply 方法

由于扩展运算符可以展开数组，所以不再需要apply方法，将数组转为函数的参数了。



```
// ES5 的写法
function f(x, y, z) {
// ...
}
var args = [0, 1, 2];
f.apply(null, args);
// ES6 的写法
function f(x, y, z) {
// ...
}
var args = [0, 1, 2];
f(...args);
```





```
// ES5 的写法
Math.max.apply(null, [14, 3, 77])
// ES6 的写法
Math.max(...[14, 3, 77])
//  等同于
Math.max(14, 3, 77);
```



另一个例子是通过push函数，将一个数组添加到另一个数组的尾部。



```
// ES5 的写法
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
Array.prototype.push.apply(arr1, arr2);
// ES6 的写法
var arr1 = [0, 1, 2];
var arr2 = [3, 4, 5];
arr1.push(...arr2);
```



上面代码的 ES5 写法中，push方法的参数不能是数组，所以只好通过apply方法变通使用push方法。有了扩展运算符，就可以直接将数组传入push方法。下面是另外一个例子。



```
// ES5
new (Date.bind.apply(Date, [null, 2015, 1, 1]))
// ES6
new Date(...[2015, 1, 1]);
```





# 3  扩展运算符的应用

## （ 1 ）合并数组

扩展运算符提供了数组合并的新写法。



```
// ES5
[1, 2].concat(more)
// ES6
[1, 2, ...more]
var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = ['d', 'e'];
// ES5 的合并数组
arr1.concat(arr2, arr3);
// [ 'a', 'b', 'c', 'd', 'e' ]
// ES6 的合并数组
[...arr1, ...arr2, ...arr3]
// [ 'a', 'b', 'c', 'd', 'e' ]
```





## （ 2 ）与解构赋值结合

扩展运算符可以与解构赋值结合起来，用于生成数组。



```
// ES5
a = list[0], rest = list.slice(1)
// ES6
[a, ...rest] = list
下面是另外一些例子。
const [first, ...rest] = [1, 2, 3, 4, 5];
first // 1
rest // [2, 3, 4, 5]
const [first, ...rest] = [];
first // undefined
rest // []:
const [first, ...rest] = ["foo"];
first // "foo"
rest // []
```





```
const [...butLast, last] = [1, 2, 3, 4, 5];
//  报错
const [first, ...middle, last] = [1, 2, 3, 4, 5];
//  报错
```



## （ 3 ）函数的返回值

JavaScript 的函数只能返回一个值，如果需要返回多个值，只能返回数组或对象。扩展运算符提供了解决这个问题的一种变通方法。



```
var dateFields = readDateFields(database);
var d = new Date(...dateFields);
```



## （ 4 ）字符串

扩展运算符还可以将字符串转为真正的数组。



```
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```





```
'x\uD83D\uDE80y'.length // 4
[...'x\uD83D\uDE80y'].length // 3
```





```
function length(str) {
return [...str].length;
}
length('x\uD83D\uDE80y') // 3
```





```
let str = 'x\uD83D\uDE80y';
str.split('').reverse().join('')
// 'y\uDE80\uD83Dx'
[...str].reverse().join('')
// 'y\uD83D\uDE80x'
```



## （ 5 ）实现了 Iterator 接口的对象

任何 Iterator 接口的对象，都可以用扩展运算符转为真正的数组。



```
var nodeList = document.querySelectorAll('div');
var array = [...nodeList];
```



对于那些没有部署 Iterator 接口的类似数组的对象，扩展运算符就无法将其转为真正的数组。



```
let arrayLike = {
'0': 'a',
'1': 'b',
'2': 'c',
length: 3
};
// TypeError: Cannot spread non-iterable object.
let arr = [...arrayLike];
```



## （ 6 ） Map 和 Set 结构， Generator 函数

扩展运算符内部调用的是数据结构的 Iterator 接口，因此只要具有 Iterator 接口的对象，都可以使用扩展运算符，比如 Map 结构。



```
let map = new Map([
[1, 'one'],
[2, 'two'],
[3, 'three'],
]);
let arr = [...map.keys()]; // [1, 2, 3]
```





```
var go = function*(){
yield 1;
yield 2;
yield 3;
};
[...go()] // [1, 2, 3]
```





```
var obj = {a: 1, b: 2};
let arr = [...obj]; // TypeError: Cannot spread non-iterable object
```




