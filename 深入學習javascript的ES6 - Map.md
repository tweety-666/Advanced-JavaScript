# 深入學習javascript的ES6 - Map
###### tags: `javascript`、`ES6`、`Map`
## Map
傳統上只能用字串當作key。這給它的使用帶來了很大的限制。於是，ES6有了Map，類似Python跟CSharp的dictionary。Map也是key-value的集合，但是key的種類不限於字串，各種類型的值（包括物件）都可以當作key。是一種更完善的Hash 結構實現。
```javascript=
const map = new Map([
  ['name', 'YO'],
  ['title', 'Author']
]);

map.size // 2
map.has('name') // true
map.get('name') // "YO"
map.has('title') // true
map.get('title') // "Author"
```
同一個key多次賦值，後面的值將覆蓋前面的值。
```javascript=
let map = new Map();

map
.set(1, 'aaa')
.set(1, 'bbb');

map.get(1) // "bbb"
```
讀取一個未知的key，則返回undefined。
```javascript=
new Map().get('YOYOYO')
// undefined
```
只有對同一個物件的引用，Map才將其視為同一個key。
下方的set和get方法，表面是針對同一個key，但實際上這兩個記憶體位置是不一樣的，因此get方法無法讀取該key，返回undefined。
```javascript=
const map = new Map();
map.set(['a'], 555);
map.get(['a']) // undefined
```
Map 的key實際上是跟記憶體位置綁定的，只要記憶體位置不一樣，就視為兩個key。這就解決了同名屬性碰撞（clash）的問題。

> 如果Map的key是一個簡單類型的值（數字、字符串、布爾值），則只要兩個值嚴格相等，Map將其視為一個鍵，比如0和-0就是一個鍵，布爾值true和字符串true則是兩個不同的鍵。
> 另外，undefined和null也是兩個不同的鍵。雖然NaN不嚴格相等於自身，但Map將其視為同一個鍵。

```javascript=
let map = new Map();

map.set(-0, 123);
map.get(+0) // 123

map.set(true, 1);
map.set('true', 2);
map.get(true) // 1

map.set(undefined, 3);
map.set(null, 4);
map.get(undefined) // 3

map.set(NaN, 123);
map.get(NaN) // 123
```

## 一般方法
1. （1）size屬性
size屬性返回Map的成員總數。
2. set(key, value)
設置後返回整個Map結構。如果key已經有值，則key值會被更新，否則就新生成該key。
3. get(key)
如果找不到key，返回undefined。
4. has(key)
has方法返回一個boolean，表示某個鍵是否在當前Map 對象之中。
5. delete(key)
delete方法刪除某個key，返回true。如果刪除失敗，返回false。
6. clear()
clear方法清除所有成員，沒有返回值。

## 遍歷方法
* keys()：返回Map成員內所有key。
* values()：返回Map成員內所有value。
* entries()：返回所有成員。
* forEach()：遍歷Map的所有成員。特別注意的是，**Map的遍歷順序就是插入順序。**

Map結構轉為Array結構，比較快速的方法是使用擴展運算符（...）。
```javascript=
const map = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

[...map.keys()]
// [1, 2, 3]

[...map.values()]
// ['one', 'two', 'three']

[...map.entries()]
// [[1,'one'], [2, 'two'], [3, 'three']]

[...map]
// [[1,'one'], [2, 'two'], [3, 'three']]
```
結合Array的map方法、filter方法，可以實現Map的遍歷和過濾（Map本身沒有map和filter方法）。
```javascript=
const map0 = new Map()
  .set(1, 'a')
  .set(2, 'b')
  .set(3, 'c');

const map1 = new Map(
  [...map0].filter(([k, v]) => k < 3)
);
// Map: {1 => 'a', 2 => 'b'}

const map2 = new Map(
  [...map0].map(([k, v]) => [k * 2, '_' + v])
    );
// Map: {2 => '_a', 4 => '_b', 6 => '_c'}
```

Map是可遍歷的結構，所以可以用forEach()。


---
# 參考資料
[Set 和Map 數據結構](http://es6.ruanyifeng.com/#docs/set-map#Map)