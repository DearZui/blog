## 一些声明
通过这些问题就来判断一个人的Node.js水平是不太严谨的，但是**它能让你对面试者在Node.js上的经验如何有个大概的了解。**

但是显然，这些问题并不会告诉你面试者思考问题的方式。

**Show me the code. 结合一些编程题来考察面试者吧**

大家都是人，不要做一个死板不近人情的面试官哦。

---
#### 什么是error-first的回调方式

Error-first回调方式用来同时传递error和data。将错误作为第一个参数，它就必须先检查看看有没有错误先。另外的参数就用来传递data了。

```
fs.readFile(filePath, function(err, data) {
  if(err) {
    //处理错误，这里的return很重要，如果发生错误，在此处就会停止了。
    return console.log(err);
  }
  //传递data
  console.log(data);
})
```

---
#### 你是如何避免回调地狱的?

- **模块化** 把回调函数分割成独立的函数
- 使用**控制流的库**，比如[async](https://www.npmjs.com/package/async)
- **generators结合Promise**
- **async/await**

---
#### Promise是什么?

概念不多说了，简单来说就是帮助你更好地处理异步操作的东西。

```
new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('result');
  }, 100)
})
  .then(console.log)
  .catch(console.error)
```

---
#### stub是什么? 举个例子?

stub是用来模拟组件/模块行为的东西，它在测试阶段为函数调用提供内部响应。

例子是写文件，但实际上并没有这么做

```
var fs = require('fs');

var writeFileStub = sinon.stub(fs, 'writeFile', function(path, data, cb) {
  return cb(null)
})

expect(writeFileStub).to.be.called
writeFileStub.restore();
```

---
#### 如何保证你的HTTP cookies安全不受XSS攻击

在`set-cookie`HTTP头部加上这几个信息：

- HttpOnly-这个属性用来防止跨站脚本攻击，它不允许cookie被JavaScript代码获取。
- secure-这个属性告诉浏览器只有在HTTPS连接时才发送cookie

像这样：`Set-Cookit: sid=<cookit-value>; HttpOnly`

---
#### 下面这段代码有什么问题
```
new Promise((resolve, reject) => {
  throw new Error('error')
}).then(console.log)
```

`then`后面没有跟上`catch`，这样的话如果出错的这段代码还是默默地运行，并不会告诉你哪里出错了。

修改后：

```
new Promise((resolve, reject) => {
  throw new Error('error')
}).then(console.log).catch(console.error)
```

如果你正在调试一个大型项目，你不知道哪个Promise可能会有问题，可以使用`unhandledRejection`。它会打印出所有未经处理的Promise异常

```
process.on('unhandledRejection', (err) => {
  console.log(err)
})
```

---
#### 下面的代码有什么问题?

```
function checkApiKey(apiKeyFromDb, apiKeyReceived) {
  if (apiKeyFromDb === apiKeyReceived) {
    return true
  }
  return false
}
```

说实话我刚看到的时候也是一脸懵逼，这有啥问题？不是很正常的一个if else代码吗。

不过这不是普通的if else代码，这是用来比较安全证书的代码，这个时候你不能泄露一丁点的信息，所以确保他们在一定的时间内进行比较。否则的你的应用就可能受到时序攻击了。

什么是时序攻击([timing attacks](https://en.wikipedia.org/wiki/Timing_attack))？Node.js使用的V8引擎试图从表示层面上优化代码。它一个字符一个字符地比较，一旦找到不符合它就停止比较。

你可以使用[cryptiles](https://www.npmjs.com/package/cryptiles)这个npm模块来解决这个问题

```
function checkApiKey(apiKeyFromDb, apiKeyReceived) {
  return cryptiles.fixedTimeCimparison(apiKeyFromDb, apiKeyReceived)
}
```

某乎上有个解释地挺好的[如何通俗地解释时序攻击(timing attack)?](https://www.zhihu.com/question/20156213/answer/43377769)

---
#### 下面的代码会输出什么

```
Promise.reso(1)
  .then((x) => x + 1)
  .then((x) => {throw new Error('My Error')})
  .catch(() => 1)
  .then((x) => x + 1)
  .then((x) => console.log(x))
  .catch(console.error) 
```

1. 一个新的Promise被创造出来，它会解析参数1
2. 解析后的值会被加上1(现在是2)，并立即返回了这个2
3. 解析的值被丢弃，抛出一个异常
4. 异常被丢弃，新的值1被返回
5. catch后运行不会停止，在异常处理之前，它继续运行，一个新的，增加了1后的值2被返回
6. 返回值被打印出来
7. 这一行不会运行，因为没有异常