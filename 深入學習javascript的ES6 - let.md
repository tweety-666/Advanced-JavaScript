# 深入學習javascript的ES6 - let
###### tags: `javascript`、`ES6`、`let`
## 前言
我的文章主要是幫助自己理解的筆記，當然也希望幫助其他人學習。但內容大多是整理內化他人的文章，畢竟我沒有把握能比資深的前輩們寫得更好，所以文末會附上參考資料，讀者可以看看前輩們更詳細的文章。

另外，做成ES6筆記，但如果我不會用ES6的技巧，那也是白學了，所以會找些例題讓自己理解，或是做一次例題、從做中學。


## let
### 1. 只在block內有用。
### 2. 常用於「for循環的計數器」。
跟用var宣告變數的for循環差異為何？
```javascript=
var a = [];
for (var i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 10
```
```javascript=
var a = [];
for (let i = 0; i < 10; i++) {
  a[i] = function () {
    console.log(i);
  };
}
a[6](); // 6
```
### 3. 設置循環變數的那部分是一個父作用域，而循環體內部是一個單獨的子作用域。
```javascript=
for (let i = 0; i < 3; i++) {
  let i = 'abc';
  console.log(i);
}
// abc
// abc
// abc
```
這部分筆者有點疑問。我以為是後面的`let i = 'abc';`把`i = 0`蓋過去。
嘗試：
```javascript=
for (let i = 0; i < 3; i++) {
  console.log(i); //undefined
}
```
所以真的是分開的作用域。let的變數i只是執行次數，並不會把變數i的數字帶進下方的作用域。
換做是用var呢？也一樣，並不會把變數i的數字帶進下方的作用域。
```javascript=
for (var i = 0; i < 3; i++) {
  console.log(i); //undefined
}
```

### 4. 不會提升？
下方看起來很合理，我完全沒有意外。
```javascript=
// let 的情况
console.log(bar); //Uncaught ReferenceError: bar is not defined
let bar = 2;
```
但我看了[胡立前輩的文章](https://github.com/aszx87410/blog/issues/34)之後，好混亂阿，原來**let、const還是會提升！**
想深入理解的話，請把胡立大大的文章全看了吧XD

```javascript=
var a = 10
function test(){
  console.log(a)
  let a
}
test()
```
我以為答案會輸出10，因為log會存取到外面的`var a = 10`，答案卻是：ReferenceError: a is not defined。所以let的確提升了，只是提升後的行為跟var比較不一樣。
同場加映：[hoisting 與 JS引擎 的運作](https://hackmd.io/L8fsc6HBR2CVRemMWPVpHg?both)

小結論：
> [let 與 const 確實有提升，與 var 的差別在於提升之後，var 宣告的變數會被初始化為 undefined，而 let 與 const 的宣告不會被初始化為 undefined，而且如果你在「賦值之前」就存取它，就會拋出錯誤。]

### 5. 在block內，使用let命令宣告變數之前，該變數都是不可用的。這在語法上，稱為“暫時性死區”（temporal dead zone，簡稱TDZ）
```javascript=
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}
```

TDZ也表示typeof不再是一個百分之百安全的操作。
```javascript=
typeof x; // ReferenceError
let x;
```
上面代碼中，變數x使用let命令宣告，所以在宣告之前，都屬於x的TDZ，只要用到該變數就會報錯。因此，typeof運行時就會拋出一個ReferenceError。

作為比較，如果一個變數根本沒有被宣告，使用typeof反而不會報錯。
```javascript=
typeof undeclared_variable // "undefined"
```
思考一下，下方程式碼會跑出哪些錯，開F12跑看看。
```javascript=
function bar(x = y, y = 2) {
  return [x, y];
}

bar();
```
答案是`Uncaught ReferenceError: y is not defined`。是因為參數x默認值等於另一個參數y，而此時y還沒有宣告，屬於TDZ。如果y的默認值是x，就不會報錯，因為此時x已經宣告了。

### 6. 不允許重複宣告
```javascript=
function func() {
  let a = 10;
  let a = 1;
  //Uncaught SyntaxError: Identifier 'a' has already been declared
}
```
```javascript=
function func(arg) {
  let arg;
}
func() // 報錯，參數已經是arg，不能再用let宣告arg

function func(arg) {
  {
    let arg;
  }
}
func() // 不報錯，因為是不同的區塊
```
這邊筆者有點疑問，參數作用域跟block作用域是分開的，怎麼會報錯`Uncaught SyntaxError: Identifier 'arg' has already been declared`呢?
是因為參數作用域會被帶進下方的block作用域，所以參數已經宣告的變數，下方的block作用域內不得用let再次宣告。但是，可以用var再次宣告。
```javascript=
function func(arg) {
  var arg;
}
func() //undefined
```


---

## 參考資料
[let 和const 命令](http://es6.ruanyifeng.com/#docs/let)
[let 和const 命令](http://caibaojian.com/es6/let.html)
[我知道你懂 hoisting，可是你了解到多深？](https://github.com/aszx87410/blog/issues/34)