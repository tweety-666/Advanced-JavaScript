# 深入學習javascript的ES6 - 解構賦值 (下集)
###### tags: `javascript`、`ES6`、`Destructuring`
## 解構賦值用在哪？
最常使用的地方有：
### 1. 交換變數的值 
```javascript=
let x = 1;
let y = 2;

[x, y] = [y, x];
```
### 2. 從函式返回多個值
函式只能返回一個值，如果要返回多個值，只能將它們放在陣列或物件裡返回。
有了解構賦值，取出這些值就非常方便。
```javascript=
function example() {
  return [1, 2, 3];
}
let [a, b, c] = example();


function example() {
  return {
    foo: 1,
    bar: 2
  };
}
let { foo, bar } = example();
```
### 3. 函式參數的定義
搭配物件，參數與變數名對應起來。
```javascript=
// 參數是有順序的值，乾淨明瞭，不用解構賦值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 參數是沒有順序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```
### 4. 得到JSON數據
```javascript=
let jsonData = {
  id: 42,
  status: "OK",
  data: [867, 5309]
};

let { id, status, data: number } = jsonData;

console.log(id, status, number);
// 42, "OK", [867, 5309]
```
### 5. 函式參數的預設值
```javascript=
jQuery.ajax = function (url, {
  async = true,
  beforeSend = function () {},
  cache = true,
  complete = function () {},
  crossDomain = false,
  global = true,
  // ... more config
} = {}) {
  // ... do stuff
};
```
指定參數的預設值，就避免了在函式體內部再寫`var foo = config.foo || 'default foo';`這樣的語句
### 6. 遍歷具有Iterator接口的結構
Iterator接口的物件，都可以用for...of循環遍歷。
配合變數的解構賦值，可以很方便地得到key和value。
```javascript=
// get key
for (let [key] of map) {
  // ...
}

// get value
for (let [,value] of map) {
  // ...
}
```
### 7. 輸入module的指定方法
```javascript=
const { SourceMapConsumer, SourceNode } = require("source-map");
```
---

## 參考資料
[Iterator 和for...of 循環](http://es6.ruanyifeng.com/#docs/iterator)
[變數的解構賦值](http://es6.ruanyifeng.com/#docs/destructuring)