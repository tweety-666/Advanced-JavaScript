# 深入學習javascript的ES6 - Set
###### tags: `javascript`、`ES6`、`Set`
## Set的誕生
Set跟Array蠻像的。但javascript已經有Array，為什麼ES6還創造了Set？
雖然有了Array，但Set內的項目都是不重複的，而且可以接受不同型別做為Set內的項目。

## Set
集合，是一個源自於數學理論中擁有不同元素的集合，其特性在於它是由一組無序且不重複的項目組成。你也可以想成是一個沒有重複元素和無順序的陣列。

**Set本身是一個構造函數**，用來生成Set數據結構。

## Set的基本方法
基本上這些功能，用array大多也可以做到，但有現成的方法就是方便。
```javascript=
const set = new Set();
set.add(); //添加項目到Set
set.has();//Set中，有沒有某個項目，有的話會回true，反之則false
set.size();//算出Set中有幾個項目
set.delete();//從Set中拿掉項目，刪除成功的話會回true，反之則false
```
Set接受一個可迭代的參數、或是null、或是不給予，且建構式將回傳一個Set物件實例。
另外，Set的值將會是唯一的，但給予的參數不會自轉型別，因此數字5與字串5將被視為不同的兩個值。**Set內部判斷兩個值是否不同，使用的算法叫做“Same-value-zero equality”，它類似於精確相等運算符（===）。**
```javascript=
const SetArr = new Set();
console.log(SetArr);
// [object Set] 

const arr = [1, 2, 3, 4, 5, 5, 5];
const SetArr2 = new Set(arr);
for (let i of SetArr2) { 
   console.log(i);
}
// 1 2 3 4 5
```
```javascript=
let set = new Set();
let a = NaN;
let b = NaN;
set.add(a);
set.add(b);
set // Set {NaN}
```
上面代碼向Set實例添加了兩個NaN，但是只能加入一個。這表明，**在Set內部，兩個NaN是相等。** 另外，**兩個對象總是不相等的。**

```javascript=
let set = new Set();

set.add({});
set.size // 1

set.add({});
set.size // 2
```
上方範例表示，由於**兩個空對像不相等，所以它們被視為兩個值。**

## Set的遍歷方法
* keys()：返回返回Set內每個成員的key
* values()：返回Set內每個成員的value
* entries()：返回Set內的每對key-value
* forEach()：遍歷Set內的每個成員

由於Set結構沒有key，只有value（或者說key和value是同一個值），所以keys方法和values方法的行為完全一致。
因為Set是可遍歷結構，所以也可以用`for...of`。Set也可以用`map()`和`filter()`。
```javascript=
let set = new Set([1, 2, 3]);
set = new Set([...set].map(x => x * 2));
// 返回Set：{2, 4, 6}

let set = new Set([1, 2, 3, 4, 5]);
set = new Set([...set].filter(x => (x % 2) == 0));
// 返回Set：{2, 4}
```
## 聯集、交集、差集
Set原本就是數學裡「集合」的意思。也可以實現聯集（Union）、交集（Intersect）和差集（Difference）。
```javascript=
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);

// 聯集
let union = new Set([...a, ...b]);
// Set {1, 2, 3, 4}

// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
// set {2, 3}

// 差集
let difference = new Set([...a].filter(x => !b.has(x)));
// Set {1}
```


---
# 參考資料
[Set 和Map 數據結構](http://es6.ruanyifeng.com/#docs/set-map)
[用 JavaScript 學習資料結構和演算法：集合（Set）篇](https://blog.techbridge.cc/2017/02/11/javascript-data-structure-algorithm-set/)
[DAY 11. JavaScript Map and Set](https://ithelp.ithome.com.tw/articles/10191607)