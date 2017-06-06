## webpack

webpack 是什么？

### entry

entry表示webpack编译的入口文件，通常由html通过script标签引入。

### loader

loader 用于对模块的源代码进行转换。其类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL，或者将CSS文件、markdown等非js文件转换为js文件并require进来！

#### loader 特性
- 支持链式传递
- 可以是同步或异步函数
- 运行在Node.js中
- 可接收查询参数
- 可以使用options配置
- 可产生额外的任意文

#### loader使用方法
```
// webpack.config.js
module.exports = {
  module: {
    rules: [
      {test: /\.css$/, use: 'css-loader'},
      {test: /\.ts$/, use: 'ts-loader'}
    ]
  }
};

// require 语句中使用
require('style-loader!css-loader?modules!./styles.css');

```

### module
Module是webpack的中的核心实体，要加载的一切和所有的依赖都是Module，总之一切都是Module。

> webpack支持的模块类型：
  - ES2015模块，使用import来加载
  - CommonJS模块，使用 require()加载。例如：Node.js模块
  - AMD模块，使用require 加载。例如：require.js支持的异步加载模块
  - css/sass/less 模块文件
  - image等其它非js模块文件

webpack 通过 loader 可以支持各种语言和预处理器编写模块。loader 描述了webpack 如何处理非js模块，可将这些非js模块进行转换，然后可以通过普通的js模块加载方式来使用。


### webpack 整体运行流程图
![](http://7xt6po.com1.z0.glb.clouddn.com/webpack%E6%95%B4%E4%BD%93%E6%B5%81%E7%A8%8B.png)
