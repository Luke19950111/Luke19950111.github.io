---
title: Set和Map数据结构
date: 2019-09-20 11:21:50
tags: [ES6, Set, Map, 数据结构]
categories:
- JavaScript
- ES6
---
<center>Set和Map零碎的笔记</center>
<!-- more -->

***

[阮一峰 ECMAScript 6 入门教程 原文](http://es6.ruanyifeng.com/#docs/set-map)

## Set

1. `Set`是ES6提供的新的数据结构，类似数组，成员唯一，没有重复的值。
2. 通过`add()`方法向`Set`结构加入成员，Set结构不会添加重复的值。
```javascript
const s = new Set();

[2, 3, 5, 4, 5, 2, 2].forEach(x => s.add(x));

for (let i of s) {
  console.log(i);
}
// 2 3 5 4
```
3. `Set`可接受**数组**或**类似数组对象**作为参数，可用作数组去重。
```javascript
// 去除数组的重复成员
[...new Set([1, 2, 3, 4, 4])]
//[1, 2, 3, 4]

//去除字符串里重复字符
[...new Set('ababbc')].join('')
// "abc"
```
4. 向`Set`加入值的时候，不会发生类型转换，`5`和`"5"`是两个不同的值。
5. `NaN`等于自身
```javascript
let set = new Set();
let a = NaN;
let b = NaN;
set.add(a);
set.add(b);
set // Set {NaN}
```
6. 两个对象总是不相等
```javascript
let set = new Set();

set.add({});
set.size // 1

set.add({});
set.size // 2
```
7. `Set`实例的属性：
 - `Set.prototype.constructor`：构造函数，默认就是Set函数
 - `Set.prototype.size`：返回Set实例的成员总数
8. `Set`实例的操作方法：
 - `Set.prototype.add(value)`：添加某个值，返回Set结构本身
 - `Set.prototype.delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功
 - `Set.prototype.has(value)`：返回一个布尔值，表示该值是否为Set的成员
 - `Set.prototype.clear()`：清除所有成员，没有返回值
9. `Array.from`方法可以将Set结构转为数组。
```javascript
//数组去重
function dedupe(array) {
  return Array.from(new Set(array));
}

dedupe([1, 1, 2, 3]) // [1, 2, 3]
```
10. `Set`结构的遍历方法，遍历顺序就是插入顺序。
 - `Set.prototype.keys()`：返回键名的遍历器
 - `Set.prototype.values()`：返回键值的遍历器
 - `Set.prototype.entries()`：返回键值对的遍历器
 - `Set.prototype.forEach()`：使用回调函数遍历每个成员
11. 数组的`map`和`filter`方法可间接用于Set。
```javascript
let set = new Set([1, 2, 3]);
set = new Set([...set].map(x => x * 2));
// 返回Set结构：{2, 4, 6}

let set = new Set([1, 2, 3, 4, 5]);
set = new Set([...set].filter(x => (x % 2) == 0));
// 返回Set结构：{2, 4}
```
12. 使用Set可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）。
```javascript
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);

// 并集
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}

// 差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}
```

## WeakSet

1. `WeakSet`结构与`Set`类似，也是不重复的值的集合。
2. `WeakSet`的成员只能是对象，而不能是其他类型的值。
3. `WeakSet`中的对象都是弱引用，即垃圾回收机制不考虑`WeakSet`对该对象的引用。
4. **垃圾回收机制依赖引用计数**，如果一个值的引用次数不为`0`，垃圾回收机制就不会释放这块内存。结束使用该值之后，有时会忘记取消引用，导致内存无法释放，进而可能会引发`内存泄漏`。WeakSet里面的引用，都不计入垃圾回收机制，所以就不存在这个问题。因此，WeakSet适合临时存放一组对象，以及存放跟对象绑定的信息。只要这些对象在外部消失，它在WeakSet里面的引用就会自动消失。
5. `WeakSet`的成员是不适合引用的，因为它会随时消失。
6. `WeakSet`内部有多少个成员，取决于垃圾回收机制有没有运行，运行前后很可能成员个数是不一样的，而垃圾回收机制何时运行是不可预测的，因此ES6规定**WeakSet不可遍历**。
7. `WeakSet`是一个构造函数，可以使用new命令，创建 WeakSet 数据结构。
```javascript
const ws = new WeakSet();
```
8. `WeakSet`可以接受一个数组或类似数组的对象作为参数。
```javascript
const a = [[1, 2], [3, 4]];
const ws = new WeakSet(a);
// WeakSet {[1, 2], [3, 4]}
```
9. `WeakSet`结构有以下三个方法:
 - `WeakSet.prototype.add(value)`：向WeakSet实例添加一个新成员
 - `WeakSet.prototype.delete(value)`：清除WeakSet实例的指定成员
 - `WeakSet.prototype.has(value)`：返回一个布尔值，表示某个值是否在WeakSet实例之中


## Map

1. `Map`数据结构类似于对象，也是键值对集合。
2. `Object`提供了**字符串-值**的对应。
3. `Map`提供了**值-值**的对应，各种类型的值（包括对象）都可以当做是键，是一种更完善的Hash结构实现。
4. `Map`构造函数可以接受**每个成员都是一个双元素的数组的数据结构**作为参数，`Set`和`Map`都可以用来生成新的Map。
```javascript
//Map构造函数接受数组作为参数，实际上执行的是下面的算法

const items = [
  ['name', '张三'],
  ['title', 'Author']
];

const map = new Map();

items.forEach(
  ([key, value]) => map.set(key, value)
);
```
5. 如果对同一个键多次赋值，后面的值将覆盖前面的值。
```javascript
const map = new Map();

map
.set(1, 'aaa')
.set(1, 'bbb');

map.get(1) // "bbb"
```
6. 如果读取一个未知的键，则返回`undefined`
```javascript
new Map().get('qwertyuiopo')
// undefined
```
7. `Map`的键实际上是跟**内存地址**绑定的，只要内存地址不一样，就视为两个键。
```javascript
//两个 ['a'] 是两个不同的数组实例，内存地址是不一样的
const map = new Map();

map.set(['a'], 555);
map.get(['a']) // undefined
```
8. 解决了同名属性碰撞（clash）的问题，使用别人库时，用对象做键名，就不用担心自己的属性与别人的属性同名。
9. `0`和`-0`就是一个键，`布尔值true`和`字符串true`则是两个不同的键，`undefined`和`null`也是两个不同的键，`NaN`和`NaN`视作同一个键。
10. Map实例的属性和操作方法：
 - `size`属性返回Map结构的成员总数
 - `Map.prototype.set(key, value)`：`set`方法设置键名key对应的键值为value，然后返回整个Map结构。如果key已经有值，则键值会被更新，否则就新生成该键
 - `set`方法返回的是当前的Map对象，因此可以采用链式写法
 ```javascript
 let map = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c')
 ```
 - `Map.prototype.get(key)`：`get`方法读取key对应的键值，如果找不到key，返回`undefined`
 - `Map.prototype.has(key)`：`has`方法返回一个布尔值，表示某个键是否在当前Map对象之中
 - `Map.prototype.delete(key)`：`delete`方法删除某个键，成功返回`true`，失败返回`false`
 - `Map.prototype.clear()`：`clear`方法清除所有成员，没有返回值
11. 遍历方法：
 - `Map.prototype.keys()`：返回键名
 - `Map.prototype.values()`：返回键值
 - `Map.prototype.entries()`：返回所有成员
 - `Map.prototype.forEach()`：遍历Map的所有成员
 - Map的遍历顺序就是插入顺序
12. `Map`可使用**扩展运算符**转为数组结构，间接使用数组的`map`和`filter`方法。
```javascript
const map0 = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');

const map1 = new Map(
  [...map0].filter(([k, v]) => k < 3)
);
// 产生 Map 结构 {1 => 'a', 2 => 'b'}

const map2 = new Map(
  [...map0].map(([k, v]) => [k * 2, '_' + v])
    );
// 产生 Map 结构 {2 => '_a', 4 => '_b', 6 => '_c'}

```
13. `forEach`方法还可以接受第二个参数，用来绑定this。
```javascript
const reporter = {
  report: function(key, value) {
    console.log("Key: %s, Value: %s", key, value);
  }
};

map.forEach(function(value, key, map) {
  this.report(key, value);
}, reporter);

//上面代码中，forEach方法的回调函数的this，就指向reporter
```

## WeakMap

1. `WeakMap`结构与Map结构类似，也是用于生成键值对的集合。
```javascript
// WeakMap 可以使用 set 方法添加成员
const wm1 = new WeakMap();
const key = {foo: 1};
wm1.set(key, 2);
wm1.get(key) // 2

// WeakMap 也可以接受一个数组，
// 作为构造函数的参数
const k1 = [1, 2, 3];
const k2 = [4, 5, 6];
const wm2 = new WeakMap([[k1, 'foo'], [k2, 'bar']]);
wm2.get(k2) // "bar"
```
2. `WeakMap`只接受对象作为键名（null除外），不接受其他类型的值作为键名。
3. `WeakMap`的键名所指向的对象，不计入垃圾回收机制。
4. `WeakMap`弱引用的只是键名，而不是键值。键值依然是正常引用。
```javascript
//下面代码中，键值 obj 正常引用
//即使WeakMap外部消除了对 obj 的引用，WeakMap内部的引用依然存在

const wm = new WeakMap();
let key = {};
let obj = {foo: 1};

wm.set(key, obj);
obj = null;
wm.get(key)
// Object {foo: 1}
```
5. `WeakMap`只有四个方法可用：`get()`、`set()`、`has()`、`delete()`。