## 一 Babel

ES6 React等写法在浏览器中并未得到完全的支持，利用 Babel 工具可以将ES6、React等语法编译为浏览器识别的ES5。

使用步骤：
```
1 项目根目录安装：
    旧版：npm i -D babel-cli babel-core babel-preset-env
    新版：npm i -D @babel/cli @babel/core @babel/preset-env

2 项目根目录创建 .babelrc ,内容如下:
    旧版：{"presets":["env"]}
    新版：{"presets":["@babel/env"]}

3 编译src目录下所有文件到dist目录下  
    npx babel src -d dist                           // 若npm<5.2，则可以使用 ./node_modules/.bin/babel
```

babel-cli只是一个执行babel命令行工具，本身不具备编译功能，编译功能是由插件babel-preset-env 提供的。  

带env是指最新的babel编译工具，包含了所有的ES*功能，如果我们不需要这么多的新特性，可以有选择的安装编译插件：
```
# ES2015转码规则	
$ npm install --save-dev babel-preset-es2015
# react转码规则	
$ npm install --save-dev babel-preset-react
# ES7不同阶段语法提案的转码规则（共有4个阶段），选装一个
$ npm install --save-dev babel-preset-stage-0
$ npm install --save-dev babel-preset-stage-1
$ npm install --save-dev babel-preset-stage-2
$ npm install --save-dev babel-preset-stage-3
```

在命令行中敲击大量命令显然不是高效的，可以直接通过配置文件配置babel，新建文件 `.babelrc`即可：
```
  {
    "presets": [
      "es2015",
      "react",
      "stage-2"
    ],
    "plugins": []
  }
```

## 二 工作流

在开发阶段，我们往往使用ES6、less、coffeescript来提升开发效率，但是这些文件不能直接部署在生产环境中，即在开发部署时，需要对css、js等文件执行编译、压缩等等一系列流程任务，我们称之为工作流。  

人工处理这些任务的代价是很高的，利用构建工具可以编写一些任务，按照我们需要的流程来执行。  

常见的处理任务包括：预处理语言的编译、代码压缩混淆、图片体积优化；  

常见的构建工具包括：
- Grunt：已衰落
- Gulp：主流工作流工具，基于NodeJS开发的前端构建工具，国内还有对应产品F.I.S（百度出品）。
- Webpack：也能承担一部分工作流任务，但其核心功能是打包。

## 三 Gulp

### 3.0 Gulp概念

Gulp的作用：
- 对HTML/CSS/JS等文件进行压缩合并
- 语法转换：es6转换为es5，less转换为css
- 公共文件抽取

Gulp也提供了大量的插件来完成更多、更复杂的任务。  

### 3.1 Gulp使用

```
第一步：本地安装gulp。进入项目文件夹后执行下列操作
npm i gulp -D 

第二步：在根目录创建配置文件 gulpfile.js
    const gulp = require('gulp');
    //创建一个默认任务
    gulp.task('default',function () {
        console.log("hello gulp");
    });

第三步：执行命令。这里要注意如果npm版本小于5.2，则不支持npx命令，需要全局安装一次gulp，然后直接运行gulp命令即可
npx gulp default	// 由于default是默认任务，这里可以省略
```

### 3.2 使用Gulp创建任务

示例：
```js
const gulp = reuqire('gulp');

gulp.task('copyJS',function () {        // 定义一个任务，名称为 copyJS
    gulp.src('src/js/**/*.js')          // src()获取资源路径
        .pipe(gulp.dest('dist/'));      // pipe()将资源传输给插件。dest()资源构建完毕后自动创建的路径
});
```

gulp.src()  也可以使用[]参数/正则，!表示不匹配， **代表递归：
```js
gulp.src(['src/js/**/*.*','!src/demo.html']) 
```

执行任务：
```
npx gulp copyJS
```

### 3.3 Gulp常见API

- gulp.src()：获取资源文件
- gulp.dest()：输出资源文件
- gulp.task()：建立gulp任务
- gulp.watch()：监控文件变化
- gulp.pipe()：导出获取到的资源

watch用于监视文件的改变，自动构建：
```js
gulp.task('js',function () {
	//src下文件发生改变，自动执行default任务
    gulp.watch('src/*',['default']); 
});
```

不同任务间存在依赖关系时，可以指定依赖，如下图：
```js
gulp.task('less',['依赖1','依赖2','依赖3'],function(){

});
```

### 3.4 Gulp插件

Gulp本身只有文件复制等基础API，通过不同的插件实现构建任务，Gulp只是按着配置文件调用执行了这些插件。

比如需要编译less，需要先安装编译less的gulp插件：`npm install gulp-less -S`
```js
const gulp = require('gulp');
const less = require('gulp-less');

gulp.task('less',function () {
    gulp.src('src/**/*.less')
        .pipe(less())                   // 调用插件
        .pipe(gulp.dest('dist/'));
});
```

常用gulp插件：
```
gulp-less 		编译LESS文件
gulp-cssmin 		压缩CSS
gulp-rname		重命名
gulp-imagemin 	图片压缩
gulp-uglify 		压缩JS
gulp-concat 		合并
gulp-htmlmin 	压缩HTML
gulp-autoprefixer 添加CSS私有前缀
gulp-rev 		添加版本号
gulp-rev-collector 内容替换
gulp-connect		创建服务器，默认监听8080端口
gulp-useref
gulp-if
```