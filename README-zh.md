# AngularJS-Gulp-Browserify 应用启动器

本应用启动器，以最佳实践打造。  
特别地，我们的目录结构与官方标准不同，而是遵循了以下设计规范：  
https://docs.google.com/document/d/1XXMvReO8-Awi1EZXAXS4PzDzdNvV6pGcuaF4Q9821Es/pub

代码文件都是结构化地进行组合。  
应用的各个模块都是自包含的（诸如样式文件、视图、控制器以及指令等），  
而非以功能进行分组（例如：所有视图文件都归类在一个文件夹，所有的样式文件也都归类在同一个文件夹）。

我们的应用目录结构看起来就像这样：

```
/app
--- /assets
------ /images
------ /icons
--- /common
------ /directives
------ /constants
------ /elements (页面公共元素。例如：页头、页脚等)
------ /resources
------ /services
------ /styles
------ common.js (公共模块的require导入。例如：ngRouter等)
------ common.less
--- /modules
------ index.js
------ MainController.js
------ MainController.spec.js (对应的控制器单元测试)
------ modules.less
------ /module1 (例如：这是主页模块，就可以命名为home)
--------- index.js (模块的定义)
--------- home.html (视图文件)
--------- home.less (样式文件)
--------- HomeController.js (控制器继承自 MainController)
--------- HomeController.spec.js
--------- homeDirective.js (视图的定义)
--------- homeRoutes.js (本模块专属的路由定义)
------ /module2
--------- /sub-module1
--------- /sub-module2
--------- index.js (模块的定义 - 子模块都在这里用require导入)
--------- module.html
--------- module.less
--------- ModuleController.js
--------- ModuleController.spec.js
--------- moduleDirective.js
--------- moduleRoutes.js (路由的定义，以及对其子模块的配置)
--- app.js
--- app.less
--- appConfig.js (主配置文件 - 不要在这里定义路由)
--- index.html
/dist (利用Gulp工作流进行打包压缩等操作后的输出)
/libs (其实这个就是bower_components，只是利用根目录下的.bowerrc改了名字而已)
/node_modules
```

每个模块都是自包含的，所有js文件都通过Browserify进行导出、混淆压缩合并。  
所有less文件都被```app.less``` import 引入，
每个less文件都被所属模块的```modules.less``` import 导入,  
然后Gulp就会把这个综合了全部 less文件的 app.less 编译成一个 CSS文件，输出到```dist```文件夹。


### 安装指引
*注意：*默认你已经本地安装了bower和gulp。若还没，请在终端敲 ```npm install -g bower gulp``` 进行安装

1) 为了轻量，Node模块以及Bower模块都不包含在本仓库。把项目clone到本地后，第一步肯定是 ```npm install```。
按理来说，Bower库也应该会跟着自动下载。若没有，请手动敲 ```bower install```。

2) 所有东西都安装完毕后，你只需要 ```gulp build```，应用服务器就会运行在```localhost:5000```(端口可以在 gulpfile 中更改)。

为了加速gulp的处理，```gulp```(默认任务)不包括复制静态文件。

实际上，用默认的```gulp```命令可以应付绝大部分需求。  
不过如果你想重新整体编译到```dist```文件夹，请敲```gulp build```。


### 目录结构开发指南
1) 所有管道任务、自动工作流以及测试的依赖都在 ```node_modules``` 文件夹，而前端第三方库/框架则位于 ```libs``` 文件夹 (实际上就是bower_components，利用.bowerrc定义重命名)。

2) 所有额外的第三方模块以及插件都应该使用 ```npm/bower install module_name --save/--save-dev``` 命令，以便于自动添加到 ```package.json``` 及 ```bower.json``` 中。

3) 所有的开发任务都在 ```app``` 文件夹中完成。生产环境下的代码文件都会通过gulp的自动化任务输出到 ```dist```文件夹 (该文件夹会在第一次gulp任务中自动创建)。

4) ```gulpfile.js``` 包含清晰的注释，定义了各项自动化任务。监听到对应文件的变动，gulp就会自动处理然后输出到 ```dist``` 文件夹。生产环境下，所有的代码文件都会汇聚到这个文件夹。


### 开发，测试，生产，部署
开发 DEV： 在开发过程中，你应该使用默认的```gulp```任务 (除非你需要重新编译，那么你就可以用```gulp build```)。

测试 TEST： 传统的单元测试，把所有的测试都堆到```/test```文件夹。相反地，我们把单元测试文件都包含在各自的模块目录下，后缀名```.spec.js``` (例如：```MainController.spec.js```). 想要运行测试，只需要简单地```gulp test```，就会运行Karma提供的开发服务器，这样子你就可以边写代码边测试了。Karma会自动基于文件变动进行测试。

生产 PROD：如果要步入生产环境，敲```gulp prod```就可以混淆压缩合并文件到```/dist```文件夹。同时，根目录下的```server.js```提供基本的Express生产环境服务器，直接敲```node server```即可启用。


### 路由，控制器以及模板路径TemplateURLs
注意：创建控制器和服务/工厂方法文件时，请遵循恰当的命名规范，即首字母大写。其余文件的命名使用驼峰命名法。

1) 默认的AngularJS应用会倾向于使用```angular-route```插件，即在 index.html 中使用主```ng-view```指令，以及直接使用标准的```href```超链接标签。本应用采用的是具备更佳路由组织以及更强可定制性的```angular-ui-router```插件，它使用主```ui-view```而非```ng-view```，使用```sref```而非```href```。更多详情请参阅其文档：https://github.com/angular-ui/ui-router

2) 由于应用结构是模块化的，因此不使用标准的路由参数。在很多其他的例子中，routes是这样子使用```TemplateURL```和```controller```的：

```
$stateProvider
    .state('home', {
      url: '/',
      templateUrl: './modules/home/home.html',
      controller: './modules/home/HomeController.js'
    })
...
```
在本应用，每个模块都被设计成可注入的指令，对应着各自的控制器。例如，home模块有一个指令```homeView```，可使用```<div home-view></div>```注入到 HTML 中 (以驼峰法命名的指令在 HTML 中变为带横杠的名称)。  

由此，我们的路由配置就可以利用```template```参数而非```templateURL```，看起来就像是这样：  

```
$stateProvider
    .state('home', {
      url: '/',
      template: '<div home-view></div>'
    });
```
可见，这样子更简单明了。只需要引用```<div></div>```标签作为模板，就可以让其余的全包含在本模块中。  
之后如果文件架构上有变动，也无需更改路由。

随着越来越多的配置参数添加到各个路由状态，我们很有必要对$stateProvider函数进行改造。下面是当前的配置：

```
var home = {
    name: 'home',
    url: '/',
    template: '<div home-view></div>'
};
var module2 = {
    name: 'module2',
    url: '/module2',
    template: '<div module2-view></div>'
};

$stateProvider.state(home);
$stateProvider.state(module2);
```

利用这个办法，就非常容易保持每个路由状态都简洁明了。

关于本应用文件目录架构的视图组织及子模块的例子，请参考：
https://github.com/goodbomb/angular-gulp-browserify-starter/blob/master/app/modules/pages/pagesRoutes.js


### 添加子模块
1) 在```app/modules```下新建文件夹，之后在其内创建以下文件

```
index.js
moduleName.html
moduleName.less
moduleNameController.js
moduleNameController.spec.js
moduleNameDirective.js
moduleNameRoutes.js (如果有很多配置项，你可以包含外部的配置文件)
```

2) 根据```app/module/home```例子，更改对应的文件内容。切记遵循命名规范。

3) 在```moduleNameRoutes```中新增路由，例如：

```
var home = {
    name: 'home',
    url: '/',
    template: '<div home-view></div>'
};
var moduleName = {
    name: 'moduleName',
    url: '/moduleName',
    template: '<div moduleName-view></div>'
};

$stateProvider.state(home);
$stateProvider.state(moduleName);
```

4) 打开模块父```index.js```文件 (譬如```modules/index.js```)，为新模块新增依赖requirement。切记是require整个模块目录 (browserify会寻找 index.js 文件作为其他模块依赖的入口)。

```
require('./modules/moduleName').name
```

最终，结果应该是这样子：

```
'use strict';

require('angular');

module.exports = angular.module('modules',
	[
		require('./home').name,
		require('./moduleName').name
	])
	.controller(require('./MainController'));
```

这些步骤完成后，你应该就可以看到在第3步你创建的URL对应的新模块内容了。

注意：对子模块也是同样的操作，只需把模块目录当成是根目录，然后在要定义特定模块路由状态及操作的地方新建一个```moduleRoutes.js```文件，之后就在本模块的```index.js```中 require 子模块。你可以直接就用主 ```module```目录来操作，然后通过它 require 其他所有模块。这样就不需要用到 app.js，且只需要调用```require('./modules').name```而非```require('./modules/moduleName').name```。这些都是取决于你以及你怎么实现模块化。


### 添加第三方库/框架的JS及CSS文件
注意：本项目为v1.2，```vendor.js```文件的作用已经改变。

为不让诸多 script 以及 link 标签铺满我们的 index.html 文件，所有的js/css文件都通过Gulp打包合并成单独的vendor.css/.js。  
若需要添加新的库/框架文件，请在```Gulpfile.js```中的 *"File Paths"* 部分，添加对应的路径。

* CSS 文件, 请在 *VendorCSS* 工作流中添加路径。
* JS 文件, 请在 *VendorJS* 工作流中添加路径。

```vendor.js``` 涵括所有第三方库/框架，有别于仅仅打包了自己代码的```bundle.js``` 。当然，你也可以把一切东西都打包进```bundle.js``` ，只是这样子你会在打包上有速度上的损失。


```bundle.js``` 是不断开发迭代的，而```vendor.js``` 则应为高度静态不变的 (除了库/框架文件的升级以及增减)。由此可见，```bundle.js```  应该尽可能保持轻量，以加快browserify的打包。


### 贡献
本项目开源，如果您有任何意见或建议，欢迎 pull request 到 *develop* 分支，这样子我们可以很方便地进行研讨。

### 学习资源
- https://github.com/curran/screencasts/tree/gh-pages/introToAngular
- https://www.codeschool.com/courses/shaping-up-with-angular-js
- http://egghead.io
- http://thinkster.io
