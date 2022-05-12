# 深入學習javascript的ES6 - WeakSet
###### tags: `javascript`、`ES6`、`WeakSet`
## WeakSet
WeakSet 結構與Set 類似，也是不重複的值的集合。但是，它與Set有兩個區別。
**1. WeakSet 的成員只能是可遍歷的結構（陣列、物件之類的）。**
```javascript=
const a = [[1, 2], [3, 4]];//是a數組的成員成為WeakSet的成員，而不是a數組本身。
const ws = new WeakSet(a);
// WeakSet {[1, 2], [3, 4]}

const b = [3, 4];
const ws = new WeakSet(b);
// Uncaught TypeError: Invalid value used in weak set(…)
```
**2. WeakSet 中的物件都是弱引用，即垃圾回收機制不考慮WeakSet對該物件的引用，也就是說，如果其他物件都不再引用該物件，那麼垃圾回收機制會自動回收該物件所佔用的內存，不考慮該物件還存在於WeakSet 之中。**

> 這是因為垃圾回收機制依賴引用計數，如果一個值的引用次數不為0，垃圾回收機制就不會釋放這塊內存。結束使用該值之後，有時會忘記取消引用，導致內存無法釋放，進而可能會引發內存洩漏。

> WeakSet裡面的引用，都不計入垃圾回收機制，所以就不存在這個問題。因此，WeakSet適合臨時存放一組物件，以及存放跟物件綁定的信息。只要這些物件在外部消失，它在WeakSet裡面的引用就會自動消失。

> 由於上面這個特點，WeakSet 的成員是不適合引用的，因為它會隨時消失。另外，由於WeakSet 內部有多少個成員，取決於垃圾回收機制有沒有運行，運行前後很可能成員個數是不一樣的，而垃圾回收機制何時運行是不可預測的，因此ES6 規定WeakSet 不可遍歷。

## 方法
* add(value)：向WeakSet添加一個新成員。
* delete(value)：清除WeakSet內的指定成員。
* has(value)：返回一個boolean，表示某個值是否在WeakSet內。
WeakSet 不可遍歷，所以沒有forEach方法，也沒有size方法。

## 內存洩漏（memory leak）
也叫記憶體洩漏。程式的運行需要記憶體。只要程式提出要求，操作系統或者運行時（runtime）就必須供給記憶體。對於持續運行的服務進程（daemon），必須及時釋放不再用到的記憶體。否則，記憶體佔用越來越高，輕則影響系統性能，重則導致進程崩潰。**沒有及時釋放不再用到的記憶體就是內存洩漏。**

有些語言（比如C 語言）必須手動釋放記憶體，工程師負責記憶體管理。大多數語言提供自動記憶體管理，減輕工程師的負擔，這被稱為"垃圾回收機制"（garbage collector）。

## 垃圾回收機制 （garbage collector）
垃圾回收機制怎麼知道，哪些記憶體不再需要呢？最常使用的方法叫做"引用計數"（reference counting）：語言引擎有一張"引用表"，保存了記憶體裡面所有的資源（通常是各種值）的引用次數。如果一個值的引用次數是0，就表示這個值不再用到了，因此可以將這塊記憶體釋放。

```javascript=
let arr = [1, 2, 3, 4];
console.log('hello world');
arr = null;
```
上面代碼中，arr重置為null，就解除了對[1, 2, 3, 4]的引用，引用次數變成了0，記憶體就可以釋放出來了。



---
# 參考資料
[JavaScript 內存洩漏教程](http://www.ruanyifeng.com/blog/2017/04/memory-leak.html)
[JavaScript 記憶體洩漏（Memory Leak）問題](https://blog.gtwang.org/web-development/javascript-memory-leak-patterns/)
[Set 和Map 數據結構](http://es6.ruanyifeng.com/#docs/set-map)