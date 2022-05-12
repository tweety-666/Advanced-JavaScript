# 深入學習javascript的ES6 - const
###### tags: `javascript`、`ES6`、`const`
不是很喜歡文章超～級～長～的，所以我拆分了let、const的文章。適中的長度比較好吸收，讀起來也比較讓人不煩燥。

ES5只有兩種聲明變數的方法：var和function。
ES6另外添加let和const。
## const
### 1. 一旦宣告，常量的值就不能改變
並不是變數的值不得改動，而是變數指向的那個**內存地址所保存的數據不得改動。**
對於簡單類型的數據（數值、字串、布林值），值就保存在變數指向的那個內存地址，因此等同於常量。

但對於復合類型的數據（主要是物件和陣列），變數指向的內存地址，保存的只是一個指向實際數據的pointer，const只能保證這個指針是固定的（即總是指向另一個固定的地址），至於它指向的數據結構是不是可變的，就完全不能控制了。
```javascript=
const foo = {};

// foo可以添加屬性
foo.prop = 123;
foo.prop // 123

// 將foo指向別的，就會報錯
foo = {}; // TypeError: "foo" is read-only
```
如果真的想將物件凍結，應該使用`Object.freeze`方法。
```javascript=
const foo = Object.freeze({});

// 一般狀況，下面一行不起作用；
// 嚴格模式下，就會報錯
foo.prop = 123;
```
利用函式凍結物件的屬性：
```javascript=
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```
### 2. 一旦宣告變數，就必須立即初始化，不能留到以後賦值。
~~說出口就馬上做，我想我該學學const。~~
### 3. block作用域
宣告const的block作用域內才有用。~~出生長大的地方才是家鄉的概念。~~
```javascript=
if (true) {
  const MAX = 5;
}

MAX // Uncaught ReferenceError: MAX is not defined
```

### 4. 不會提升？
跟let一樣有暫時性死區的特質。其實還是會提升，只是提升的方式跟var不一樣。簡單來說，大家都有hoisting，只是var比較自由，let和const在賦值之前是不能存取的。提升了又如何?不能存取阿QQ
```javascript=
if (true) {
  console.log(MAX); // Uncaught ReferenceError: MAX is not defined
  const MAX = 5;
}
```
小結論：
> let 與 const 也有 hoisting 但沒有初始化為 undefined，而且在賦值之前試圖取值會發生錯誤。更多討論請見：[Are variables declared with let or const not hoisted in ES6?
](https://stackoverflow.com/questions/31219420/are-variables-declared-with-let-or-const-not-hoisted-in-es6)
```javascript=
(function () {  'use strict';
  console.log(x);  // ReferenceError: x is not defined
  let x = 3;
  console.log(x);
})();
```
```javascript=
(function () {  'use strict';
  console.log(x);  //var只有變數提升。賦值沒有提升。undefined
  var x = 3;
  console.log(x);
})();
```

### 5. 不可重複宣告
```javascript=
var message = "Hello!";
let age = 25;

// Uncaught SyntaxError: Identifier 'message' has already been declared
const message = "Goodbye!";
const age = 30;
```
若用var、let或const在同個block作用域內宣告，為何會報錯？
更多深入討論請見：[How does Javascript declare function parameters?](https://stackoverflow.com/questions/46691231/how-does-javascript-declare-function-parameters/46693246#46693246)

## 頂層物件的屬性
頂層物件，在瀏覽器環境指的是window，在Node指的是global。
ES5之中，頂層物件的屬性與全域變數是一致的。
```javascript=
window.a = 1;
a // 1

a = 2;
window.a // 2
```
但這樣會有些問題：
1. 沒辦法在編譯時就報出變數未宣告的錯誤，執行時才能知道（因為全域變數可能是頂層物件的屬性創造的，而屬性的創造是動態的）。
2. 很容易不知不覺地就創建了全域變數（比如打字出錯）。
3. 頂層物件的屬性（全域）是到處可以讀寫的，這非常不利於模組化。

> ES6為了改變這一點，一方面規定，為了保持兼容性，var和function聲明的全域變數，依舊是頂層物件的屬性。
另一方面規定，let、const、class聲明的全域變數，不屬於頂層物件的屬性。從ES6開始，全域變數將逐步與頂層物件的屬性脫鉤。

```javascript=
var a = 1;
window.a // 1

let b = 1;
window.b // undefined
```

ES5 的頂層物件，本身也是一個問題，因為它在各種實現裡面是不統一的。
1. 瀏覽器裡面，頂層對像是window，但Node和Web Worker沒有window。
2. 瀏覽器和Web Worker裡面，self也指向頂層物件，但是Node沒有self。
3. Node裡面，頂層對像是global，但其他環境都不支援。

同一段程式碼為了能夠在各種環境，都能取到頂層物件，現在一般是**使用this變數**，但是有局限性。
~~this真的很難搞。~~
1. 全域環境中，this會返回頂層物件。但是，Node模組和ES6模組中，this返回的是當前模組。
2. 函式里面的this，如果函式不是作為物件的方法運行，而是單純作為函式運行，this會指向頂層物件。但是，嚴格模式下，這時this會返回undefined。
3. 不管是嚴格模式，還是普通模式，new Function('return this')()，總是會返回全域物件。但是，如果瀏覽器用了CSP（Content Security Policy，內容安全策略），那麼eval、new Function這些方法都可能無法使用。

如何獲取頂層物件？
詳請請看：[let 和const 命令](http://es6.ruanyifeng.com/#docs/let)


---

## 參考資料
[let 和const 命令](http://es6.ruanyifeng.com/#docs/let)
[let 和const 命令](http://caibaojian.com/es6/let.html)
[我知道你懂 hoisting，可是你了解到多深？](https://github.com/aszx87410/blog/issues/34)