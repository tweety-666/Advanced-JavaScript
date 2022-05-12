# 深入學習javascript的ES6 - for...of，和for-loop、for-in、forEach的介紹
###### tags: `javascript`、`ES6`、`for...of`、`for...in`、`for loop`、`forEach`
## for loop
需要的變數比較多。
```javascript=
var a = [1,2,3,4,5],
len =a.length;
for(var i=0 ; i < len ; i++){
console.log(a[i]);
}
```
## forEach
比for loop簡潔，但不能使用breack中斷循環，也不能使用return語句返回到外層函式。
```javascript=
var array1 = ['a', 'b', 'c'];

array1.forEach(function(element) {
  console.log(element);
});

```
搭配箭頭函式：
```javascript=
var array1 = ['a', 'b', 'c'];

array1.forEach((element)=> {
  console.log(element);
});

// expected output: "a"
// expected output: "b"
// expected output: "c"
```
## for-in
for-in除了遍歷Array元素以外,還會遍歷自定義屬性。
缺點：
1. index的值不是實際的數字，而是字串“0”、“1”、“2”，此時很可能在無意之間進行字串算數計算，例如：“2” + 1 == “21” 。
2. for-in還會遍歷自定義的屬性，Array原型鏈上的屬性都能被訪問到。
3. for-in按照隨機順序遍歷Array。
```javascript=
for (var index in a) { // for-in按照隨機順序遍歷Array，所以建議別這麼做
  console.log(a[index]);
}
```

## for-of
ES6的產物，用來解決for-in的問題。另外，與forEach()不同的是，它可以正確響應break、continue和return語句。
```javascript=
for (var value of myArray) {
  console.log(value);
}
```

## for-in和for-of的比較
**1. 推薦在循環物件屬性的時候，使用for...in,在遍歷陣列的時候的時候使用for...of。
2. for...in循環出的是key，for...of循環出的是value。
3. for...of不能循環普通的物件，需要和Object.keys()搭配使用。
4. for-of不僅支持Array，還支持大多數陣列對象，例如DOM [NodeList對象]。for-of也支持字串遍歷，它將字串視為一系列的Unicode來進行遍歷。**
```javascript=
for (var chr of "ab") {
  console.log(chr);
}
//a
//b
```
## for-of補足for-in的缺陷
用範例演示：
```javascript=
let aArray = ['a',123,{a:'1',b:'2'}]
```
使用for...in：

```javascript=
for(let index in aArray){
    console.log(`${aArray[index]}`);
    //output:
    //"a"
    //"123"
    //"[object Object]"
}
```
使用for...of：
```javascript=
for(var value of aArray){
    console.log(value);
    //output:
    //"a"
    //123
    //[object Object] {
    //   a: "1",
    //   b: "2"
    // }
}
```
看起來很像，那為什麼說for...of修復了for...in的缺陷和不足。
假設我們在Array內，添加一個屬性name: `aArray.name = 'demo'`,再分別查看上面寫的兩個循環：

```javascript=
for(let index in aArray){
    console.log(`${aArray[index]}`); //aArray.name也被循環了
}
//output:
// "a"
// "123"
// "[object Object]"
// "demo"

for(var value of aArray){
    console.log(value);
}
//output:
// "a"
// 123
// [object Object] {
//   a: "1",
//   b: "2"
// }
```
看看aArray實際上內容到底是什麼樣子：
```javascript=
let aArray = ['a',123,{a:'1',b:'2'}];
aArray.name = 'demo';
console.log(aArray)
//output:
// ["a", 123, [object Object] {
//   a: "1",
//   b: "2"
// }]
```
`aArray.name = 'demo'`只是添加屬性。不是給陣列多添加一個元素。
下方寫法才是在陣列內添加元素。
```javascript=
let aArray = ['a',123,{a:'1',b:'2'}];
aArray.name = 'demo';

aArray.push('demo1')
console.log(aArray);
//output:
// ["a", 123, [object Object] {
//   a: "1",
//   b: "2"
// }, "demo1"]
```

---
# 參考資料
[javascript總for of和for in的區別？](https://segmentfault.com/q/1010000006658882)
[Iterator 和for...of 循環](http://es6.ruanyifeng.com/#docs/iterator#for---of-%E5%BE%AA%E7%8E%AF)
[for循环、for-in、forEach、for-of](https://www.jianshu.com/p/8c77447c5060)