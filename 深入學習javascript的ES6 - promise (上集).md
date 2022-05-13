# 深入學習javascript的ES6 - promise (上集)
###### tags: `javascript`、`ES6`、`promise`

## 同步與異步
* 同步：一次做一件事，A做完換B。
* 異步：同時做不只一件事，一起做A跟B。
~~嗯...，字面上來說，跟我認知的同步不一樣。~~
為什麼有這兩種區分呢？Javascript是單執行緒，一次跑一行，但有時候需要一次做好幾件事情。像是跟後端拿資料，如果沒拿到，後面的程式就不繼續跑了？~~一直停在那邊，母湯啦。~~

## callback
Promise的誕生，一定有要解決的問題。先從callback(回調)講起。
假設，現在要寫個功能，依序抓取客戶的id、名字、信箱。
```javascript=
getUserInfo(function (Users) {
    getID(Users[0], function (User) {
        getName(User, function (User) {
            alert(User.email);
        })
    })
})

function getID(User, callback) {
    $.ajax("http://XXXXXX", {
        user_id: Users[0].id
    }).done(function (result) {
        callback(result);
    })
}

function getName(User, callback) {
    $.ajax("http://XXXXXX", {
        user_name: Users[0].name
    }).done(function (result) {
        callback(result);
    })
}

function getEmail(callback) {
    $.ajax(
        "http://XXXXXX")
        .done(function (result) {
            callback(result);
        });
}
```
分別依序抓取三個東西。才抓取三個東西就寫了三層回調函式，萬一要抓十個資料呢...?
這就是傳說中的callback hell。程式看起來好醜阿，於是乎有了Promise，拯救被callback累壞的工程師。
## Promise
所謂Promise，簡單說就是一個容器，裡面保存著某個未來才會結束的事件（通常是一個異步操作）的結果。
從語法上說，**Promise是一個物件(物件)，從它可以獲取異步操作的消息**。
Promise提供統一的API，各種異步操作都可以用同樣的方法進行處理。

**1. Promise有三種狀態，等待中（pending）、完成（resolve or fulfilled）跟失敗（reject）。**

**2. 物件的狀態不受外界影響。**
Promise物件代表一個異步操作，有三種狀態：pending（進行中）、fulfilled（已成功）和rejected（已失敗）。

**3. 一旦狀態改變，就不會再變，任何時候都可以得到這個結果。**
Promise物件的狀態改變，只有兩種可能：從pending變為fulfilled和從pending變為rejected。

**4. 一旦新建它就會立即執行，無法中途取消。**

**5. 如果不設置callback，Promise內部拋出的錯誤，不會反應到外部。**

**6. 當處於pending狀態時，無法得知目前進展到哪一個階段（剛剛開始還是即將完成）。**

## 用法
```javascript=
const promise = new Promise(function(resolve, reject) {
  // ... some code
  if (/* 異步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```
Promise實例生成以後，可以用then方法分別指定resolved狀態和rejected狀態的callback。
```javascript=
promise.then(function(value) {
  // success
}, function(error) {
  // failure
});
```
使用範例，異步加載圖片：
```javascript=
function loadImageAsync(url) {
  return new Promise(function(resolve, reject) {
    const image = new Image();

    image.onload = function() {
      resolve(image);
    };

    image.onerror = function() {
      reject(new Error('Could not load image at ' + url));
    };

    image.src = url;
  });
}
```
Promise結合Ajax的範例：
```javascript=
const getJSON = function(url) {
  const promise = new Promise(function(resolve, reject){
    const handler = function() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
    const client = new XMLHttpRequest();
    client.open("GET", url);
    client.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();

  });

  return promise;
};

getJSON("/posts.json").then(function(json) {
  console.log('Contents: ' + json);
}, function(error) {
  console.error('wrong!', error);
});
```
## then()跟catch()
以往的callback很多層的話，要除錯很困難。搭配then()跟catch()一起使用。catch()可以拋出所接到的錯誤。
所以說，Promise也可以用then()跟catch()？沒錯。

Promise實例具有then方法，then方法是定義在原型對像Promise.prototype上的。它的作用是為Promise實例，添加狀態改變時的callback。then方法的第一個參數是resolved狀態的callback，第二個參數（可選）是rejected狀態的callback。

**then方法返回的是一個新的Promise實例（注意，不是原來那個Promise實例）。**
因此可以採用**鍊式寫法**，即then方法後面再調用另一個then方法。
```javascript=
getJSON("/post/1.json").then(function(post) {
  return getJSON(post.commentURL);
}).then(function funcA(comments) {
  console.log("resolved: ", comments);
}, function funcB(err){
  console.log("rejected: ", err);
});
```
鍊式寫法，之所以可以then()之後繼續使用then()，因為前面的then()返回的是一個promise，promise的原型鍊有then方法，所以可以一直接續著使用then()。

**Promise物件的錯誤具有"冒泡"性質**，會一直向後傳遞，直到被捕獲為止。也就是說，錯誤總是會被下一個catch語句捕獲。

then()、catch()容易讓人想到同樣拿來拋出error的try()、catch()。下方兩種寫法達成的功能是一樣的。
```javascript=
//寫法1
const promise = new Promise(function(resolve, reject) {
  try {
    throw new Error('test');
  } catch(e) {
    reject(e);
  }
});
promise.catch(function(error) {
  console.log(error);
});

//寫法2
const promise = new Promise(function(resolve, reject) {
  reject(new Error('test'));
});
promise.catch(function(error) {
  console.log(error);
});
```
但跟傳統的try/catch不同的是，如果沒有使用catch方法指定錯誤處理的callback，Promise物件拋出的錯誤不會傳遞到外層代碼，即不會有任何反應。
```javascript=
getJSON('/post/1.json').then(function(post) {
  return getJSON(post.commentURL);
}).then(function(comments) {
  // some code
}).catch(function(error) {
  // 處理前面好幾個promise的錯誤
});
```
數看看，上方案例有幾個promise？getJSON一個，.then()各一個。所以總共有三個。
一般來說，不要在then方法裡面定義Reject狀態的callback（即then的第二個參數），總是使用catch方法。
```javascript=
// bad
promise
  .then(function(data) {
    // success
  }, function(err) {
    // error
  });

// good
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
```


---
## 參考資料
[Promise 物件](http://es6.ruanyifeng.com/#docs/promise)
[[Javascript] Promise, generator, async與ES6](http://huli.logdown.com/posts/292655-javascript-promise-generator-async-es6)
