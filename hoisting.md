# hoisting
###### tags: `javascript`、`ES6`、`hoisting`
## 什麼是hoisting?
在「創造階段」所做的，在執行環境(Execution Contexts)為變數及函式保留記憶體空間的動作，就被稱為提升( Hoisting )。
例如:
```javascript=
// global是一個Execution Contexts
console.log(a) // undefined
var a = 5
console.log(a) // 5


test(123) // 123
function test(v){
  console.log(v)
}

function test2(){ // 這function scope內 是一個Execution Contexts
  console.log(v) // undefined
  var v = 3
}
test2()
```
> 變數跟函式的宣告都會提升，賦值不會。

### let 、const 的提升
let 、const 兩個是在 ES6 之後才出現，這兩個沒辦法像使用 var 那樣自由自在(但是var這麼漂泊也是有壞處)，在函式宣告之前就使用，乍看之下，在這兩個變數上並不會有提升的動作。但是其實是有的(有跟沒有一樣，但總之就是有)，只是 JS 在變數正式被賦值之前不讓你使用而已。

在賦值之前，使用 let 宣告變數的語彙環境之前的區域，被稱為 TDZ (Temporal Dead Zone) 。

## 為什麼需要hoisting?
1. 一定要先宣告變數才可以使用
2. 一定要先宣告函式才可以使用

以上兩個我覺得都是好習慣，但最重要的是...

**3. 達成function互相呼叫**

每個函式有自己的作用域，有自己的執行環境(Execution Contexts)，以下簡稱EC。
每個EC都會有相對應的variable environment（以下簡稱VE），想像成是個Memory heap，把宣告的變數都先放到小抽屜，外面貼上便利貼(變數)，每個小抽屜看外面就知道是什麼變數，要取用時就打開小抽屜，才知道裡面是什麼值。

例如說`var a = 1 `這一句，之前有講過可以分成左右兩塊：

`var a `去VE裡面新增一個屬性叫做 a（如果沒有 a 這個屬性的話）並初始化成 undefined
`a = 1`：先在VE裡面找到叫做 a 的屬性，找到之後設定為 10
如果這塊EC內的VE裡面找不到，它會透過 scope chain 不斷往上尋找(包含global EC)，如果每一層EC都找不到就會拋出錯誤。

## JS不是單執行緒的直譯式語言嗎?怎麼達成提升?
JS如果真的一行行跑，在執行第n行的時候根本不知道n+1行是什麼，想hoisting是不可能的。
簡單來說，幾乎每種語言都不是單純只靠compiler或interpreter，基本上兩種都需要，才能將程式碼翻譯成電腦讀得懂的機器碼。
只是因為JS引擎內的interpreter做工占比比較多，所以被稱為直譯式語言。
也就是說，JS引擎內的compiler，在編譯階段就先做好hoisting這件事情。

## 思考時間
想想以下程式碼，最後會跑出甚麼結果。
```javascript=
function bigBoss(){
  function littleBrother() {
    return 'I am the boss!';
  }
  return littleBrother();
  function littleBrother() {
    return 'Hi!';
  }
}


bigBoss();
```