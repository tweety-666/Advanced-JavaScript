# 深入學習javascript的ES6 - 解構賦值 (上集)
###### tags: `javascript`、`ES6`、`Destructuring`
## 解構賦值怎麼用？
### 1. 匹配模式
只要等號兩邊的模式相同，左邊的變數就會被賦予對應的值。
沒給值就會是`undefined`。
如果等號的右邊不是陣列（或者嚴格地說，不是可遍歷的結構），那麼將會報錯。
```javascript=
//以前
let a = 1;
let b = 2;
let c = 3;
//Destructuring
let [a, b, c] = [1, 2, 3];

//更多範例
let [foo, [[bar], baz]] = [1, [[2], 3]];
foo // 1
bar // 2
baz // 3

let [ , , third] = ["foo", "bar", "baz"];
third // "baz"

let [x, , y] = [1, 2, 3];
x // 1
y // 3

let [head, ...tail] = [1, 2, 3, 4];
head // 1
tail // [2, 3, 4]

let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []

//更難的範例
let [a, [b], d] = [1, [2, 3], 4];
a // 1
b // 2
d // 4
```
對於Set 結構，也可以使用陣列的解構賦值。

```javascript=
let [x, y, z] = new Set(['a', 'b', 'c']);
x // "a"
```
事實上，只要某種數據結構具有Iterator接口，都可以採用陣列形式的解構賦值。

```javascript=
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

let [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5

//用於object
let { foo, bar } = { foo: 'aaa', bar: 'bbb' };
foo // "aaa"
bar // "bbb"

let { baz } = { foo: 'aaa', bar: 'bbb' };
baz // undefined
```
物件的解構與陣列有一個重要的不同。陣列的元素是按次序排列的，變數的取值由它的位置決定；而**物件的屬性沒有次序，變數必須與屬性同名，才能取到正確的值**。

如果變數名與屬性名不一致，必須寫成下面這樣。

```javascript=
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"

let obj = { first: 'hello', last: 'world' };
let { first: f, last: l } = obj;
f // 'hello'
l // 'world'
```
這實際上說明，物件的解構賦值是下面形式的簡寫。

```javascript=
let { foo: foo, bar: bar } = { foo: 'aaa', bar: 'bbb' };
```
對於object來說，真正被賦予值的是object的value。
```javascript=
let { foo: baz } = { foo: 'aaa', bar: 'bbb' };
baz // "aaa"
foo // error: foo is not defined
```
解構也可以用於嵌套結構的物件。

```javascript=
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p: [x, { y }] } = obj;
x // "Hello"
y // "World"
```
注意，這時p是模式，不是變數，因此不會被賦值。如果p也要作為變數賦值，可以寫成下面這樣。

```javascript=
let obj = {
  p: [
    'Hello',
    { y: 'World' }
  ]
};

let { p, p: [x, { y }] } = obj;
x // "Hello"
y // "World"
p // ["Hello", {y: "World"}]
```
下方代碼有三次解構賦值，分別是對loc、start、line三個屬性的解構賦值。注意，最後一次對line屬性的解構賦值之中，只有line是變數，loc和start都是模式，不是變數。
```javascript=
const node = {
  loc: {
    start: {
      line: 1,
      column: 5
    }
  }
};

let { loc, loc: { start }, loc: { start: { line }} } = node;
line // 1
loc  // Object {start: Object}
start // Object {line: 1, column: 5}
```
更難的範例：
```javascript=
let obj = {};
let arr = [];

({ foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true });

obj // {prop:123}
arr // [true]
```
**object的解構賦值可以取到繼承的屬性。**

```javascript=
const obj1 = {};
const obj2 = { foo: 'bar' };
Object.setPrototypeOf(obj1, obj2);

const { foo } = obj1;
foo // "bar"
```
### 2. Iterator接口是什麼？
Iterator，中文稱做遍歷器。JS到了ES6後，數據結構有4種：
1. Array
2. Object
3. Map
4. Set

這樣就需要一種統一的接口機制，來處理所有不同的數據結構。
遍歷器（Iterator）就是這樣一種機制。它是一種接口，為各種不同的數據結構提供統一的訪問機制。任何數據結構只要部署Iterator接口，就可以依次處理該數據結構的所有成員。
詳情請看：[深入學習javascript的ES6 - Iterator 和 for...of 循環](https://hackmd.io/p_fJXmfER3OnLcR8XUwbyQ)

### 3. 默認值
```javascript=
let [foo = true] = [];
foo // true

let [x, y = 'b'] = ['a']; // x='a', y='b'
let [x, y = 'b'] = ['a', undefined]; // x='a', y='b'
```
ES6內部使用嚴格相等運算符（===），判斷一個位置是否有值。
所以，只有當一個陣列成員嚴格等於undefined，默認值才會生效。
```javascript=
let [x = 1] = [undefined];
x // 1

let [x = 1] = [null];
x // null
```
```javascript=
function f() {
  console.log('aaa');
}

let [x = f()] = [1];
```
上面代碼中，因為x能取到值，所以函數f根本不會執行。上面的代碼其實等價於下面的代碼。

```javascript=
let x;
if ([1][0] === undefined) {
  x = f();
} else {
  x = [1][0];
}
```
默認值可以引用解構賦值的其他變數，但該變數必須已經聲明。

```javascript=
let [x = 1, y = x] = [];     // x=1; y=1
let [x = 1, y = x] = [2];    // x=2; y=2
let [x = 1, y = x] = [1, 2]; // x=1; y=2
let [x = y, y = 1] = [];     // ReferenceError: y is not defined
```
上面最後一個表達式之所以會報錯，是因為x用y做默認值時，y還沒有聲明。
### 4. 已經聲明的變數用於解構賦值
```javascript=
let x;
{x} = {x: 1};
// SyntaxError: syntax error
```
上面代碼的寫法會報錯，因為JavaScript引擎會將`{x}`理解成一個代碼塊，從而發生語法錯誤。只有不將大括號寫在行首，避免JavaScript將其解釋為代碼塊，才能解決這個問題。
應該寫成：
```javascript=
let x;
({x} = {x: 1});
```
### 5. 解構賦值允許等號左邊不放置任何變數名
```javascript=
({} = [true, false]);
({} = 'abc');
({} = []);
```
### 6. 以對陣列進行物件屬性的解構
由於陣列本質是特殊的物件，因此可以對陣列進行物件屬性的解構。
```javascript=
let arr = [1, 2, 3];
let {0 : first, [arr.length - 1] : last} = arr;
first // 1
last // 3
```
奇怪，按照順序，last的值應該是2。
上面程式對陣列進行物件解構。陣列arr的0 key對應的value是1，[arr.length - 1]就是2 key，last對應的value是3。

### 7. 字串的解構賦值
字串也可以？不是說有辦法被遍歷的結構才可以嗎？這是因為此時，字串被轉換成了一個類似陣列的物件。
```javascript=
let [x, y] = 'Hi';
console.log(x, y); // 'H', 'i'
```
### 8. Number、Boolean的解構賦值
解構賦值時，如果等號右邊是數值和布林值，則會先轉為物件。
```javascript=
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```
上面代碼中，數值和布林值的包裝對像都有toString屬性，因此變數s都能取到值。

**解構賦值的規則是，只要等號右邊的值不是對像或陣列，就先將其轉為物件。**
由於undefined和null無法轉為物件，所以對它們進行解構賦值，都會報錯。

### 9. function的解構賦值
```javascript=
//範例1
function add([x, y]){
  return x + y;
}
add([1, 2]); // 3

//範例2
[[1, 2], [3, 4]].map(([a, b]) => a + b);
// [ 3, 7 ]
```
更難的範例：
```javascript=
//範例3 {x = 0, y = 0}是給x、y參數默認值
function move({x = 0, y = 0} = {}) {
  return [x, y];
}
move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, 0] //找不到參數y，給參數默認值0
move({}); // [0, 0] //找不到參數x、y，給參數默認值0
move(); // [0, 0]  //找不到參數x、y，給參數默認值0

//範例4
//下面是為函數move的參數指定默認值，而不是為變數x和y指定默認值
function move({x, y} = { x: 0, y: 0 }) {
  return [x, y];
}

move({x: 3, y: 8}); // [3, 8]
move({x: 3}); // [3, undefined] //找不到參數y，沒給{x,y}默認值，所以給undefined
move({}); // [undefined, undefined]
move(); // [0, 0] 
//可以理解為：跑這段{x, y} = { x: 0, y: 0 }，x跟y為0，也就是move({0,0})
```


---

## 參考資料
[Iterator 和for...of 循環](http://es6.ruanyifeng.com/#docs/iterator)
[變數的解構賦值](http://es6.ruanyifeng.com/#docs/destructuring)
