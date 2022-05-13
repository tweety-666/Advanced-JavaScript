# What is THIS in JavaScript
###### tags: `javascript`、`this`

## 為什麼會有this
物件導向的語言中，this指的是實例。
JS的this之所以困難，是因為跟其他物件導向的語言比起來有著惱人的例外。JS其實不是物件導向的語言，只是有著原型物件導向語法的語法糖(很像物件導向但其實只是模擬物件導向)，其實還是原型，並不是真正的以類型為基礎的物件導向設計。

 ## 基本情形
1. this的值在瀏覽器底下就會是window
2. 在node.js底下會是global，
3. 在嚴格模式下，this的值就會是undefined

### 基本情形: 物件的this
```javascript=
// normal function is dynamically scoped
const obj = {
  name: 'OWO',
  func: function() {
    console.log(this) // obj
  }
}

obj.func()

```

## 令人意外的情形
### 1. 物件function中呼叫其他function
Normal function is dynamically scoped.
```javascript=
// normal function is dynamically scoped
const obj = {
  name: 'OWO',
  func: function() {
    console.log(this) // obj
    var anotherFunc = function() {
      console.log(this) // global
    }
     anotherFunc() // 又到這裡被呼叫
  }
}

obj.func() // 在global這裡被呼叫

```
## 令人意外的情形
### 2. 物件function被賦值給其他地方呼叫
```javascript=
// normal function is dynamically scoped
const obj = {
  name: 'OWO',
  func: function() {
    console.log(1,this) // global(window)
    var anotherFunc = function() {
      console.log(2,this) // global(window)
    }
    anotherFunc()
  }
}

var func2 = obj.func // obj.func被給予到global那邊
func2() // global(window)
```

> **JavaScript的this，取決於函式如何呼叫**

### Dynamic scope VS lexical scope
* lexical scope:
    程式碼中，變數、數據、function被定義的地方。
* Dynamic scope:
    function被呼叫的地方。 
    
## 避免意外
    
### 1. 箭頭函式
Arrow function is lexically bound.
```javascript=
// arrow function is lexically bound
const obj = {
  name: 'OWO',
  func: function() {
    console.log(this) // obj
    var anotherFunc = () => {
      console.log(this) // obj
    }
     anotherFunc()
  }
}

obj.func()

```
### 2. call()、apply()、bind()
強制綁定你要的this進去。
```javascript=
const obj = {
  name: 'OWO',
  func: function() {
    console.log(1,this) // obj
    var anotherFunc = function(a,b) {
      console.log(2,this,a,b) // obj
    }
     anotherFunc.bind(this)(1,2) // 先綁定 後呼叫
  }
}

obj.func()
```
```javascript=
const obj = {
  name: 'OWO',
  func: function() {
    console.log(1,this) // obj
    var anotherFunc = function(a,b) {
      console.log(2,this,a,b) // obj
    }
     anotherFunc.call(this,1,2)
  }
}

obj.func()
```
```javascript=
const obj = {
  name: 'OWO',
  func: function() {
    console.log(1,this) // obj
    var anotherFunc = function(a,b) {
      console.log(2,this,a,b) // obj
    }
     anotherFunc.apply(this,[1,2])
  }
}

obj.func()
```

### 3. 事先跟function說好誰是this
```javascript=
const obj = {
  name: 'OWO',
  func: function() {
    console.log(this) // obj
    var self = this
    var anotherFunc = function(){
      console.log(self) // obj
    }
     anotherFunc()
  }
}

obj.func()

```