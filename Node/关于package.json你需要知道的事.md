## NAME 属性
 `name`属性作为`package.json`的基础组成部分，顾名思义，描述了这个模块的名字。
 在`package.json`内，`name`属性通常会长这样：
 > "name": "metaverse"

 尽管`name`属性只有很少的限制(最多214字符，不能以`.`或者`<i>`开始，不能有大写字母，URL里面不能出现的字符也不要)，有意思的是，现在很多的生态系统已经定义了自己的命名标准，只需要`name`这个属性就可以准确找到你需要的东西。(我的理解，比如现在npm你知道包的名字直接npm install **就行了嘛)

 ## VERSION属性
 `version`属性描述了这个模块目前的版本。

 尽管`version`属性并不强制使用[semver](https://nodesource.com/blog/semver-a-primer/),semver仍是目前Node.js生态系统中绝大多数模块和项目使用的标准。

 服从semver规则的`version`属性大概长这样：

 >"version": 5.12.4"

 ## LICENSE属性

