# node.js webpack和vite

### webpack与vite比较

1. 底层的语言

`webpack`是基于`nodejs`构建，`js`是以毫秒计数。
`vite`是基于`esbulid`预构建依赖，`esbulid`是采用`go`语言编写的，`go`语言是纳秒级别的。

**总结：因为`js`是毫秒级别，`go`语言是纳秒级别。所以`vite`比`webpack`打包器快10-100倍。**

2. 打包过程

`webpack`：分析各个模块之间的依赖=>然后进行编译打=>打包后的代码在本地服务器渲染。**随着模块增多，打包的体积变大，造成热更新速度变慢。**
`vite`：启动服务器=>请求模块时按需动态编译显示。（`vite`遵循的是`ES Modlues`模块规范来执行代码，不需要打包编译成`es5`模块即可在浏览器运行。）

**总结：`vite`启动的时候不需要分析各个模块之间的依赖关系、不需要打包编译。`vite`可按需动态编译缩减时间。当项目越复杂、模块越多的情况下，`vite`明显优于`webpack`**

3. 热更新

`webpack`：模块以及模块依赖的模块需重新编译
`vite`：浏览器重新请求该模块即可

4. 使用方面

[vite](https://so.csdn.net/so/search?q=vite&spm=1001.2101.3001.7020)开箱即用，更加简单，基于浏览器esm，使得hmr更加优秀，达到极速的效果；webpack更加灵活，api以及插件生态更加丰富。

5.  原理不同

[webpack](https://so.csdn.net/so/search?q=webpack&spm=1001.2101.3001.7020)是bundle，自己实现了一套模块导入导出机制。vite是利用浏览器的esm能力，是bundless。

**vue-cli是基于webpack开发的**