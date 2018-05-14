---
title: 关于Promise理解，到位了
date: 2018-05-10 14:15:51
tags:
---
之前看过了很多遍关于Promise 的相关知识，但是感觉不踏实，总觉得不得心应手。
最近心血来潮，想回炉再造一遍。网上看了好多篇相关的文章，算是get到了一些点，下面是自己整理的一些不清楚或者有遗漏的地方。
* promise.resolve()传递不同参数的含义？
* 当一个promise依赖另一个promise时事件执行顺序?
* 当catch捕获到了错误后，会不会继续执行后面的then方法?
如果你也觉得这些问题，你比较迷茫，可以看看。
# 1.promise语法
**Promise编程的核心思想是如果数据就绪(promised)，那么(then)做点什么。**  
Promise 实例如下：
```
const promise = new Promise(function(resolve, reject) {
  // ... some code

  if (/* 异步操作成功 */){
    resolve(value);
  } else {
    reject(error);
  }
});
```
Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve(成功)和reject(失败)。  

**resolve**函数的作用是，将Promise对象的状态从“未完成”变为“成功”（ 即从 pending 变为 resolved ），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去。  

**reject**函数的作用是，将Promise对象的状态从“未完成”变为“失败”（ 即从 pending 变为 rejected ）， 在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

Promise实例生成以后，可以用then方法分别指定resolved状态和rejected状态的回调函数。
```
promise.then(function(value) {
  // 成功后的回调
}, function(error) {
  // 失败后的回调
});
```
以上，第二个函数是可选的，不一定要提供。这两个函数都接受Promise对象传出的值作为参数。

如果then方法返回的是一个新的Promise实例，那么就可以采用链式写法，即then方法后面再调用另一个then方法。
```
getJSON("/posts.json").then(function(json) {
  return json.post;
}).then(function(post) {
  // 第二个promise请求的操作。
});
```
以上的代码，第一个回调函数完成以后，会将返回结果作为参数，传入第二个回调函数。

**Promise.prototype.catch**方法用于指定发生错误时的回调函数
```
getJSON('/posts.json').then(function(posts) {
  // ...
}).catch(function(error) {
  // 处理 getJSON 和 前一个回调函数运行时发生的错误
  console.log('发生错误！', error);
});

```
如果异步操作抛出错误，状态就会变为rejected，就会调用catch方法指定的回调函数，处理这个错误。另外，then方法指定的回调函数，如果运行中抛出错误，也会被catch方法捕获。

建议Promise 对象后面要跟catch方法，这样可以处理 Promise 内部发生的错误。catch方法返回的还是一个 Promise 对象，因此后面还可以接着调用then方法。

**注意**  
1. 如果resolve函数和reject函数调用时带有参数，那么它们的参数会被传递给回调函数。resolve函数的参数除了正常的值以外，还可能是另一个 Promise 实例

```
const p1 = new Promise(function (resolve, reject) {
  // ...
});

const p2 = new Promise(function (resolve, reject) {
  // ...
  resolve(p1);
})

//上面代码中，p1和p2都是 Promise 的实例，但是p2的resolve方法将p1作为参数，即一个异步操作的结果是返回另一个异步操作。
```
这时p1的状态就会传递给p2。也就是说，p1的状态决定了p2的状态。如果p1的状态是pending，那么p2的回调函数就会等待p1的状态改变；如果p1的状态已经是resolved或者rejected，那么p2的回调函数将会立刻执行。

2. 调用resolve或reject并不会终结 Promise 的参数函数的执行。

3. then方法是定义在原型对象Promise.prototype上的。

4. 如果没有使用catch方法指定错误处理的回调函数，Promise 对象抛出的错误不会传递到外层代码，即不会有任何反应。

5. Promise 在resolve语句后面，再抛出错误，不会被捕获，等于没有抛出。因为 Promise的状态一旦改变，就永久保持该状态，不会再变了。

# 2. Promise.resolve()
有时需要将现有对象转为 Promise 对象，Promise.resolve方法就起到这个作用（了解一下）。

```
Promise.resolve('foo')
// 等价于
new Promise(resolve => resolve('foo'))
```

Promise.resolve方法的参数分成四种情况。

1）参数是一个 Promise 实例

如果参数是 Promise 实例，那么Promise.resolve将不做任何修改、原封不动地返回这个实例。

2）参数是一个thenable对象

thenable对象指的是具有then方法的对象，比如下面这个对象。
```
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};
```

Promise.resolve方法会将这个对象转为 Promise 对象，然后就立即执行thenable对象的then方法。
```
let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

let p1 = Promise.resolve(thenable);
p1.then(function(value) {
  console.log(value);  // 42
});

//上面代码中，thenable对象的then方法执行后，
//对象p1的状态就变为resolved，从而立即执行最后那个then方法指定的回调函数，输出 42。
```
3）参数不是具有then方法的对象，或根本就不是对象  

如果参数是一个原始值，或者是一个不具有then方法的对象，则Promise.resolve方法返回一个新的 Promise 对象，状态为resolved。

```
const p = Promise.resolve('Hello');

p.then(function (s){
  console.log(s)
});
// Hello


//上面代码生成一个新的 Promise 对象的实例p。
//由于字符串Hello不属于异步操作（判断方法是字符串对象不具有 then 方法），返回 Promise 实例的状态从一生成就是resolved，所以回调函数会立即执行。
//Promise.resolve方法的参数，会同时传给回调函数。
```

4）不带有任何参数

Promise.resolve方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。

所以，如果希望得到一个 Promise 对象，比较方便的方法就是直接调用Promise.resolve方法。
```
const p = Promise.resolve();

p.then(function () {
  // ...
});
```
**注意！立即resolve的 Promise 对象，是在本轮“事件循环”（event loop）的结束时，而不是在下一轮“事件循环”的开始时**

```
setTimeout(function () {
  console.log('three');
}, 0);

Promise.resolve().then(function () {
  console.log('two');
});

console.log('one');

// one
// two
// three


//上面代码中，setTimeout(fn, 0)在下一轮“事件循环”开始时执行。

//Promise. resolve()在本轮“事件循环”结束时执行。

//console.log('one')则是立即执行，因此最先输出。
```
# 3.错误提醒

1）用了 promises 后怎么用 forEach?

forEach()/for/while 并非你寻找的解决方案。你需要的是 Promise.all();  
例子如下：
```
// 我想删除所有的docs

db.allDocs({include_docs: true}).then(function (result) {
  return Promise.all(result.rows.map(function (row) {
    return db.remove(row.doc);
  }));
}).then(function (arrayOfResults) {
  // All docs have really been removed() now!
});
```
上面的代码是，大体来说，Promise.all()会以一个 promises 数组为输入，并且返回一个新的 promise。  
这个新的 promise 会在数组中所有的 promises 都成功返回后才返回。他是异步版的 for 循环。

并且 Promise.all() 会将执行结果组成的数组返回到下一个函数。  
比如当你希望从 PouchDB 中获取多个对象时，会非常有用。  
此外一个更加有用的特效是，一旦数组中的 promise 任意一个返回错误，Promise.all() 也会返回错误。

2）忘记使用catch！
单纯的坚信自己的 promises 会永远不出现异常，很多开发者会忘记在他们的代码中添加一个 .catch()。  
然而不幸的是这也意味着，任何被抛出的异常都会被吃掉，并且你无法在 console 中观察到他们。这类问题 debug 起来会非常痛苦。  
**所以！把catch()加上！**

3) 使用副作用调用而非返回

下面的代码，经常被使用到，但是有什么问题呢？
```
somePromise().then(function () {
  someOtherPromise();
}).then(function () {
  // 我希望someOtherPromise() 状态变成resolved!
  // 但是并没有
});
```
每一个 promise 都会提供给你一个 then() 函数 (或是 catch()，实际上只是 then(null, ...) 的语法糖)。当我们在 then() 函数内部时：
```
somePromise().then(function () {
  // I am inside a then() function!
});
```

我们可以做什么呢？有三种事情：
* return 另一个 promise
* return 一个同步的值 (或者 undefined)
* throw 一个同步异常

就是这样。一旦你理解了这个技巧，你就理解了 promises。因此让我们逐个了解下。

1. 返回另一个 promise。
```
getUserByName('nolan').then(function (user) {
  return getUserAccountById(user.id);
}).then(function (userAccount) {
  // I got a user account!
});
```
注意：我是` return `第二个 promise，这个` return `非常重要。如果我没有写` return`，`getUserAccountById() `就会成为一个副作用，并且下一个函数将会接收到` undefined `而非` userAccount`

2. 返回一个同步值 (或者 undefined)

返回 undefined 通常是错误的，但是返回一个同步值实际上是将同步代码包裹为 promise 风格代码的一种非常赞的手段。  
举例来说，我们对 users 信息有一个内存缓存。我们可以这样做：

```
getUserByName('nolan').then(function (user) {
  if (inMemoryCache[user.id]) {
    return inMemoryCache[user.id];    // returning a synchronous value!
  }
  return getUserAccountById(user.id); // returning a promise!
}).then(function (userAccount) {
  // I got a user account!
});
```

第二个函数不需要关心 userAccount 是从同步方法还是异步方法中获取的，并且第一个函数可以非常自由的返回一个同步或者异步值。

3. 抛出同步异常

比如我们希望在用户已经登出时，抛出一个同步异常。这会非常简单。
```
getUserByName('nolan').then(function (user) {
  if (user.isLoggedOut()) {
    throw new Error('user logged out!'); // throwing a synchronous error!
  }
  if (inMemoryCache[user.id]) {
    return inMemoryCache[user.id];       // returning a synchronous value!
  }
  return getUserAccountById(user.id);    // returning a promise!
}).then(function (userAccount) {
  // I got a user account!
}).catch(function (err) {
  // Boo, I got an error!
});
```
如果用户已经登出，我们的 catch() 会接收到一个同步异常，并且如果 后续的 promise 中出现异步异常，他也会接收到。  
再强调一次，这个函数并不需要关心这个异常是同步还是异步返回的。