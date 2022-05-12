# 深入學習javascript的ES6 - Generator
###### tags: `javascript`、`ES6`、`Generator`
## Generator
在學Generator之前，先看看一般函式長怎樣：
```javascript=
function DoSth(){
  //do something
}
```
Generator在外表上，多了一個`*`。
```javascript=
function* DoSth(){
  //do something
}
```
寫成這樣也可以：
```javascript=
function　*DoSth(){
  //do something
}
```
Generator和一般函式的差異是，**Generator可以讓函式暫停，之後再接續執行。**
一般函式只能從頭執行到尾，無法暫停。
另外，**Generator回傳的是Iterator物件**。

## yield
至於怎麼讓Generator暫停？Generator內部多了一個`yield`可以使用。透過`yield`，可以把東西從Generator函式內部丟出來，並且暫停執行。yield除了丟值，還可以接受值，所以丟完以後，現在會等下一個值丟進來。
```javascript=
function* GeneratorExample() {
    for (var i=0; i<=10; i++) {
        console.log(i)
        yield i
    }
}
```
當我們呼叫GeneratorExample()時，會得到一個iterator，把他當成一個迭代器就好了，這個迭代器內部有個next方法。當我們每次呼叫這個iterator的next()時，就會執行GeneratorExample()，一直到出現`yield`關鍵字的地方才暫停，直到下次呼叫next()。
```javascript=
function* generator(i) {
  yield i;
  yield i + 10;
}

var gen = generator(10);
console.log(gen.next().value);
// expected output: 10
console.log(gen.next().value);
// expected output: 20
```
**yield的特性：會先執行右邊的程式碼，等待下一次的呼叫，並且賦值給左邊。**
```javascript=
function *foo() {
  for (let i = 1; i <= 3; i++) {
    let x = yield `等等！ i = ${i}`;
    console.log(x);
  }
}

setTimeout(() => {
  console.log('終於到這了');
}, 1);

var a = foo();
console.log(a); // foo {<closed>}

var b = a.next();
console.log(b); // {value: "等等！ i = 1", done: false}

var c = a.next();
console.log(c); // {value: "等等！ i = 2", done: false}

var d = a.next();
console.log(d); // {value: "等等！ i = 3", done: false}

var e = a.next();
console.log(e); // {value: undefined, done: true}

// 終於到這了
```
## return
一般函式執行時遇到return，return出來之後，就無法再接續執行return後的程式。在generator內也是一樣。
```javascript=
function* yieldAndReturn() {
  yield "Y";
  return "R";
  yield "unreachable";
}

var gen = yieldAndReturn()
console.log(gen.next()); // { value: "Y", done: false }
console.log(gen.next()); // { value: "R", done: true }
console.log(gen.next()); // { value: undefined, done: true }
```
最後的`value: undefined`是因為已經return出來了，無法再接續執行return後的程式。所以`gen.next()`找不到`yield`給的值。找不到值，所以是`undefined`。

## next()
next()會返回一個物件，裡面包含著兩個properties，分別是value和done。
* value：
前面提到，Generator內部多了一個`yield`可以使用。透過`yield`，可以把東西從Generator函式內部丟出來，並且暫停執行。value就是Generator函式內，`yield`丟出來的「值」。
* done：
done是個boolean值，假如這個generator完全被執行完的話，done就會變成true，反之則為false。這裡要注意的是當執行到最後一個`yield`時，done仍然會是false，再執行一次才會得到done為true的結果。

generator仍然是一個function，一樣可以在裡面return東西，如此在執行到return這一行時，next()就會返回value為return的東西，並且done為true。

next()是個方法，可以放參數，參數就是`yield`丟出來的值。但通常第一個next()不放參數，因為它前面也沒有yield可以丟值給它當參數。
```javascript=
function *foo(x) {
  var y = 2 * (yield (x + 1));
  var z = yield (y / 3);;
  return (x + y + z);
}

var it = foo(5); // 取得iterator物件，不執行

console.log( it.next() ); // { value:6, done:false }
// 第一次呼叫 x: 5, y: undefined, z: undefined
// 執行到 var y 那邊停住，傳出 yield(x+1) = 6


console.log( it.next( 12 ) );   // { value:8, done:false }
// 送 12 進去所以 y = 2 * 12 = 24，第二次呼叫 x:5, y: 24, z: undefined
// 到 var z 那邊停住，輸出 8 等待輸入...

console.log( it.next( 13 ) );   // { value:42, done:true }
// 送 13 進去所以 z = 13 所以第三次呼叫 x: 5, y: 24, z: 13
// 第三次完成並取得 return value 42
```

## yield*
yield*是一個結合yield跟Generator特性的東西。
下方程式碼中，`generator(10)`執行到了`yield* anotherGenerator(i);`，就會跑進`anotherGenerator(i)`這個Generator函式，執行`anotherGenerator(i)`內的每個`yield`。
```javascript=
function* anotherGenerator(i) {
  yield i + 1;
  yield i + 2;
  yield i + 3;
}

function* generator(i) {
  yield i; //i代入10，yield丟出10，暫停並等待接受外部值
  //下一個gen.next()沒傳值進來，所以還是10
  yield* anotherGenerator(i);
  //跑到anotherGenerator(i)內，
  // yield i + 1; //參數i是10，10+1，這邊丟出11，暫停並等待
  // 下一個gen.next()沒傳值進來，所以還是11
  // yield i + 2; //上一個yield還是11，11+1，這邊丟出12，暫停並等待
  // 下一個gen.next()沒傳值進來，所以還是12
  // yield i + 3; //上一個yield還是12，12+1，這邊丟出13，暫停並等待
　//anotherGenerator(i)執行完了
  yield i + 10;　//10+10
}

var gen = generator(10);

console.log(gen.next().value); // 10
console.log(gen.next().value); // 11
console.log(gen.next().value); // 12
console.log(gen.next().value); // 13
console.log(gen.next().value); // 20
```
**yield*的return無作用**：
```javascript=
function * gen1 () {
  yield 1
  yield* gen2() //yield*的return無作用
  yield 5
}

function * gen2 () {
  yield 2
  yield 3
  yield 4
  return 'will not be iterated'
}

for (let value of gen1()) {
  console.log(value)
}
// 1
// 2
// 3
// 4
// 5
```

---

# 參考資料
[淺入淺出 Generator Function](https://denny.qollie.com/2016/05/08/es6-generator-func/)
[ES6 Generators 基礎教學](http://andyyou.logdown.com/posts/276655-es6-generators-teaching)
[[Javascript] Promise, generator, async與ES6](http://huli.logdown.com/posts/292655-javascript-promise-generator-async-es6)
[[Javascript] ES6 Generator基礎](http://huli.logdown.com/posts/292331-javascript-es6-generator-foundation)
[你懂 JavaScript 嗎？#25 產生器（Generator）](https://cythilya.github.io/2018/11/01/generator/)
[function*](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Statements/function*)
[JavaScript异步编程：Generator与Async](https://juejin.im/post/5aeed63b5188256735646bd6)
