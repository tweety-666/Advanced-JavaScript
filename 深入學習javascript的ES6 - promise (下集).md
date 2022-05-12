# 深入學習javascript的ES6 - promise (下集)
###### tags: `javascript`、`ES6`、`promise`
這篇會著重於catch()。
> Promise.prototype.catch方法是.then(null, rejection)或.then(undefined, rejection)的別名，用於指定發生錯誤時的callback function。
> 
接續上集，報錯的時候，catch會捕捉到錯誤，但程式會因error停住而不往下嗎？
```javascript=
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
  //沒有定義x，所以會跑reject去報錯
    resolve(x + 2);
  });
};

someAsyncThing().then(function() {
  console.log('everything is great');
  //沒錯誤的話，會跑出這行
});

setTimeout(() => { console.log("YO") }, 1000);
// Uncaught (in promise) ReferenceError: x is not defined
// YO
```
上面範例中，1秒後還是會跑出`YO`，意思是，**Promise內部的錯誤不會影響到Promise外部。**

那如果順序是先catch再then呢？
把上方的例子改寫，可以發現catch先捕捉到錯誤，接著的then也有執行。
```javascript=
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    resolve(x + 2);
  });
};

someAsyncThing()
.catch(function(error) {
  console.log('oh no', error);
})
.then(function() {
  console.log('carry on');
});
// oh no [ReferenceError: x is not defined]
// carry on
```
下方沒出錯，所以會跳過catch，執行then()。那萬一這個then後面執行的程式有出錯，它前面的catch會捕捉到錯誤？
```javascript=
Promise.resolve()
.catch(function(error) {
  console.log('oh no', error);
})
.then(function() {
  console.log('carry on');
});
// carry on
```
```javascript=
const someAsyncThing = function() {
  return new Promise(function(resolve, reject) {
    // x沒聲明跟定義，會報錯
    resolve(x + 2);
  });
};

someAsyncThing().then(function() {
  return someOtherAsyncThing();//成功才會執行這行
}).catch(function(error) {//報錯// oh no [ReferenceError: x is not defined]
  console.log('oh no', error);
  y + 2;// y沒聲明跟定義，會報錯，但後面沒有catch接住這錯誤
  //因為有錯所以也不會執行後面的then
}).then(function() {
  console.log('carry on');
});
// oh no [ReferenceError: x is not defined]
```
從上方可以看出前面的catch不會接住後方所出的錯誤，要接住error就在函式後面給一個catch去捕捉錯誤。可改寫為：
```javascript=
someAsyncThing().then(function() {
  return someOtherAsyncThing();//成功才會執行這行
}).catch(function(error) {// x沒聲明跟定義，會報錯，被這個catch捕捉到error
//這邊的catch只會捕捉到前方出現的錯誤。後方的錯誤都不關它的管轄了。
  console.log('oh no', error);
  // y沒聲明跟定義，會報錯，後面又有一個catch接住這錯誤
  y + 2;
}).catch(function(error) {
  console.log('carry on', error);
});
// oh no [ReferenceError: x is not defined]
// carry on [ReferenceError: y is not defined]
```


---
## 參考資料
[Promise 對象](http://es6.ruanyifeng.com/#docs/promise)
[[Javascript] Promise, generator, async與ES6](http://huli.logdown.com/posts/292655-javascript-promise-generator-async-es6)