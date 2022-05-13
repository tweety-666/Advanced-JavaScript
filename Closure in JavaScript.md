# Closure in JavaScript
###### tags: `javascript`、`closure`
## 什麼是閉包(closure)?
真是無法顧名思義的一個名詞，閉包代表函式記得並存取scope的能力。
越看越模糊。直接上範例:
```javascript=
function a() {
  let grandpa = 'grandpa'
  return function b() {
    let father = 'father'
    return function c() {
      let son = 'son'
      return `${grandpa} -> ${father} -> ${son}`
    }
  }
}

a()
```
## 為什麼要有閉包?
如果執行到c函式，照理來說上面的a跟b函式的scope內的變數應該已經被銷毀了。為什麼c還能存取到?
因為閉包的特性，讓本來應該要被垃圾回收機制給銷毀的變數(grandpa跟father)還保留，並且讓c可以存取到那些scope的變數。

想像一下，你正要丟垃圾(a跟b)，結果發現這邊有scope chain連結到c函式的執行環境(scope chain: a連著b連著c)，因為c還要用，那就先不要丟吧。

## scope chain
scope chain是在函式被定義的當下決定的。所以上述的程式碼例子中，a、b、c的範圍鏈已經決定好，並不是執行階段才決定。
 
## setTimeout
setTimeout也具有閉包的特性。
```javascript=
function callMeMaybe() {
    const callMe = 'Hi!'; // 照理來說應該銷毀了
    setTimeout(function() {
        console.log(callMe); // 執行 四秒後可以存取到callMe
    }, 4000);
}

callMeMaybe();
```
### 變數提升?
callMeMaybe這個函式執行到setTimeout，setTimeout被放到callback queue，等待被執行，此時callMeMaybe已經執行完，程式已經跑到`const callMe = 'Hi!'`，所以等setTimeout執行，可以存取到callMe。
```javascript=
function callMeMaybe() {
    setTimeout(function() {
        console.log(callMe); // 執行 四秒後可以存取到callMe
    }, 4000);
    const callMe = 'Hi!'; //提升?
}

callMeMaybe();
```
## 封裝
OOP(物件導向程式設計)內的其中一個大原則:封裝，JS要如何做到?
其實透過閉包，就可以達成。
### 封裝的等級: private、protected、public。
* private
    只能被自己的class存取，繼承的物件不能存取。
* protected
    class自己本身和繼承的物件都可以存取。
* public
    任何人都可以存取。
```javascript=
function counter(){
  var count = 0 // 自己才能存取到

  function innerCounter(){
    return count++
  }

  return innerCounter // 曝露出去
}

var countFunc = counter();  // 執行counter() 得到被暴露出的innerCounter

console.log( countFunc() );   // 相當於執行innerCounter() // 1
console.log( countFunc() );   // 相當於執行innerCounter() // 2
```