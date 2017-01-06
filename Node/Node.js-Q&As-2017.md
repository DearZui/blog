## 一些声明
通过这些问题就来判断一个人的Node.js水平是不太严谨的，但是**它能让你对面试者在Node.js上的经验如何有个大概的了解。**

但是显然，这些问题并不会告诉你面试者思考问题的方式。

**Show me the code. 结合一些编程题来考察面试者吧**

大家都是人，不要做一个死板不近人情的面试官哦。

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

#### 你是如何避免回调地狱的?

- **模块化** 把回调函数分割成独立的函数
- 使用**控制流的库**，比如[async](https://www.npmjs.com/package/async)
- **generators结合Promise**
- **async/await**

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