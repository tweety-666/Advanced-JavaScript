# 深入學習javascript的ES6 - async
###### tags: `javascript`、`ES6`、`async`、`await`
## 為何要有async？
async其實也不是ES6的東西，但是ES6的Generator的語法糖。

**演進順序：Callback → Promise  → Generator  → Async**
Callback因為有Callback Hell的問題所以有了Promise，但Promise也有Promise Hell的問題，所以有Generator，但又想更簡短、明瞭，於是有了Async跟Await。

## async狀態：resolved、rejected
當 async 函式被呼叫時，它會回傳一個 Promise。如果該 async 函式回傳了一個值，Promise 的狀態將為一個帶有該回傳值的 resolved。如果 async 函式拋出例外或某個值，Promise 的狀態將為一個帶有被拋出值的 rejected。

## await
顧名思義，await是等待的意思。async 函式內部可以使用 await 表達式，**它會暫停此 async 函式的執行，並且等待傳遞至表達式的 Promise 的解析，解析完之後會回傳解析值，並繼續此 async 函式的執行。**
```javascript=
function resolveAfter2Seconds(x) {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(x);
    }, 2000);
  });
}

async function add1(x) {
  const a = await resolveAfter2Seconds(20); 
  //遇到await會等到await後面的函式執行完並回傳值 //這邊花了2秒
  const b = await resolveAfter2Seconds(30);
  //遇到await會等到await後面的函式執行完並回傳值 //這邊也花了2秒
  return x + a + b;
}

add1(10).then(v => {
  console.log(v);  // prints 60 after 4 seconds.
});


async function add2(x) {
  const p_a = resolveAfter2Seconds(20); //異步執行花2秒
  const p_b = resolveAfter2Seconds(30); //異步執行花2秒，總共花兩秒
  return x + await p_a + await p_b;
}

add2(10).then(v => {
  console.log(v);  // prints 60 after 2 seconds.
});
```

## async和Generator的差異
async函式回傳一個Promise物件，可以使用then方法添加callback。當函式執行的時候，一遇到await就會先暫停，等到異步操作完成，再接續執行函式內後面的語句。

下方範例，一比較就會發現，async函式就是將Generator函式的星號（*）替換成async，將yield替換成await。
有一個Generator函式，依次讀取兩個文件。
```javascript=
const fs = require('fs');
const readFile = function (fileName) {
  return new Promise(function (resolve, reject) {
    fs.readFile(fileName, function(error, data) {
      if (error) return reject(error);
      resolve(data);
    });
  });
};

const gen = function* () {
  const f1 = yield readFile('/etc/fstab');
  const f2 = yield readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```
上面代碼的gen不太直觀好懂，可以寫成async函式，就是下面這樣。

```javascript=
const asyncReadFile = async function () {
  const f1 = await readFile('/etc/fstab');
  const f2 = await readFile('/etc/shells');
  console.log(f1.toString());
  console.log(f2.toString());
};
```

async有針對Generator做一些改進：

**1. async自帶執行器，Generator沒有**
async函式的執行，與一般函式一樣。不像Generator函式，需要調用next()，或者用[co module](https://www.npmjs.com/package/co)，才能真正執行。

**2. 更好懂**
async和await，比起星號和yield，語義更清楚了。async表示函式有異步操作，await表示緊跟在後面的表達式需要等待結果。

**3. 更廣的適用性**
[co module](https://www.npmjs.com/package/co)規定yield後面只能是Thunk函式或Promise，而async函式的await後面，可以是Promise和原始類型的值（數值、字串和布林值，但這時會自動轉成立即resolved的Promise物件）。

**4. 回傳值不同**
async函式的回傳值是Promise物件，這比Generator函式的回傳值是Iterator物件方便多了。你可以用then方法指定下一步的操作。
**async函式完全可以當作是多個異步操作包裝成的一個Promise物件，而await就是內部then的語法糖。**

