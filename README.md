# Gulp-study
**gulp 入门到熟练**
# 入门指南
>
1. 全局安装 `gulp`：
`
$ npm install --global gulp
$ npm install gulp   这这是把把gulp安装到项目中
`
2. 作为项目的开发依赖（`devDependencies`）安装：安装前最好先`npm init`，不然会报个错，找不到`package.json`文件  
`
$ npm install --save-dev gulp   在安装的时候把gulp写进项目package.json文件的依赖中
`  
3. 在项目根目录下创建一个名为 `gulpfile.js` 的文件： 
`
var gulp = require('gulp');
gulp.task('default', function() {  // 将你的默认的任务代码放在这});
`  
4. 运行gulp：
`
$ gulp
`
默认的名为 `default `的任务（`task`）将会被运行，在这里，这个任务并未做任何事情。
想要单独执行特定的任务（`task`），请输入 `gulp <task> <othertask>`。


1.1、说明：`src`方法是指定需要处理的源文件的路径，`gulp`借鉴了`Unix操作系统`的管道（`pipe`）思想，前一级的输出，直接变成后一级的输入，`gulp.src`返回当前文件流至可用插件；

1.2、`globs`：  需要处理的源文件匹配符路径。
类型(必填)：`String or StringArray`；

通配符路径匹配示例:

`
“src/a.js”：指定具体文件；

“*”：匹配所有文件    例：src/*.js(包含src下的所有js文件)；

“**”：匹配0个或多个子文件夹    例：src/**/*.js(包含src的0个或多个子文件夹下的js文件)；

“{}”：匹配多个属性    例：src/{a,b}.js(包含a.js和b.js文件)  src/*.{jpg,png,gif}(src下的所有jpg/png/gif文件)；

“!”：排除文件    例：!src/a.js(不包含src下的a.js文件)；
`
```javascript
	var gulp = require('gulp'),
    less = require('gulp-less');
 
gulp.task('testLess', function () {
    //gulp.src('less/test/style.less')
    gulp.src(['less/**/*.less','!less/{extend,page}/*.less'])
        .pipe(less())
        .pipe(gulp.dest('./css'));
});
```
1.3、
`options`：  类型(可选)：`Object`，有3个属性`buffer、read、base`；

`options.buffer`：  类型：`Boolean`  默认：true 设置为false，将返回`file.content`的流并且不缓冲文件，处理大文件时非常有用；

`options.read`：  类型：`Boolean`  默认：true 设置false，将不执行读取文件操作，返回null；

`options.base`：  类型：`String`  设置输出路径以某个路径的某个组成部分为基础向后拼接，具体看下面示例：

```javascript
	gulp.src('client/js/**/*.js') 
  .pipe(minify())
  .pipe(gulp.dest('build'));  // Writes 'build/somedir/somefile.js'
 
gulp.src('client/js/**/*.js', { base: 'client' })
  .pipe(minify())
  .pipe(gulp.dest('build'));  // Writes 'build/js/somedir/somefile.js'
  ```
2、`gulp.dest(path[, options])`
2.1、说明：dest方法是指定处理完后文件输出的路径；
```javascript
	gulp.src('./client/templates/*.jade')
  .pipe(jade())
  .pipe(gulp.dest('./build/templates'))
  .pipe(minify())
  .pipe(gulp.dest('./build/minified_templates'));
  ```
2.2、path：  类型(必填)：String or Function 指定文件输出路径，或者定义函数返回文件输出路径亦可；

2.3、options：  类型(可选)：Object，有2个属性cwd、mode；

options.cwd：  类型：String  默认：process.cwd()：前脚本的工作目录的路径 当文件输出路径为相对路径将会用到；

options.mode：  类型：String  默认：0777 指定被创建文件夹的权限；

3、gulp.task(name[, deps], fn)

3.1、说明：task定义一个gulp任务；

3.2、name：  类型(必填)：String 指定任务的名称（不应该有空格）；

3.3、deps：  类型(可选)：StringArray，该任务依赖的任务（注意：被依赖的任务需要返回当前任务的事件流，请参看下面示例）；

```javascript
	gulp.task('testLess', function () {
    return gulp.src(['less/style.less'])
        .pipe(less())
        .pipe(gulp.dest('./css'));
});
 
gulp.task('minicss', ['testLess'], function () { //执行完testLess任务后再执行minicss任务
    gulp.src(['css/*.css'])
        .pipe(minifyCss())
        .pipe(gulp.dest('./dist/css'));
});
```
3.4、fn：  类型(必填)：Function 该任务调用的插件操作；

4、gulp.watch(glob [, opts], tasks) or gulp.watch(glob [, opts, cb])

4.1、说明：watch方法是用于监听文件变化，文件一修改就会执行指定的任务；

4.2、glob：  需要处理的源文件匹配符路径。类型(必填)：String or StringArray；

4.3、opts：  类型(可选)：Object 具体参看https://github.com/shama/gaze；

4.4、tasks：  类型(必填)：StringArray 需要执行的任务的名称数组；

4.5、cb(event)：  类型(可选)：Function 每个文件变化执行的回调函数；
```javascript
	gulp.task('watch1', function () {
    gulp.watch('less/**/*.less', ['testLess']);
});
 
gulp.task('watch2', function () {
    gulp.watch('js/**/*.js', function (event) {
        console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
    });
});
Gulp 配置 Sass 编译的示例代码
var gulp = require('gulp');
var sass = require('gulp-sass');

gulp.task('sass', function () {
    gulp.src('./scss/*.scss')
        .pipe(sass())
        .pipe(gulp.dest('./css'));
});

gulp.task('watch', function() {
    gulp.watch('scss/*.scss', ['sass']);
});

gulp.task('default', ['sass','watch']);
Grunt 配置 Sass 编译的示例代码
module.exports = function(grunt) {
    grunt.initConfig({
        pkg: grunt.file.readJSON('package.json'),
        sass: {
            dist: {
                files: {
                    'style/style.css' : 'sass/style.scss'
                }
            }
        },
        watch: {
            css: {
                files: '**/*.scss',
                tasks: ['sass']
            }
        }
    });
    grunt.loadNpmTasks('grunt-contrib-sass');
    grunt.loadNpmTasks('grunt-contrib-watch');
    grunt.registerTask('default',['watch']);
}
```
# 常用的gulp插件
## 1 自动加载插件 
使用gulp-load-plugins 
安装：npm install --save-dev gulp-load-plugins 
要使用gulp的插件，首先得用require来把插件加载进来，如果我们要使用的插件非常多，那我们的gulpfile.js文件开头可能就会是这个样子的： 
```
var gulp = require('gulp'),    //一些gulp插件,abcd这些命名只是用来举个例子
    a = require('gulp-a'), 
    b = require('gulp-b'),
    c = require('gulp-c'),
    d = require('gulp-d'),
    e = require('gulp-e'),
    f = require('gulp-f'),
    g = require('gulp-g'),    //更多的插件...
    z = require('gulp-z');  
```
虽然这没什么问题，但会使我们的gulpfile.js文件变得很冗长，看上去不那么舒服。gulp-load-plugins插件正是用来解决这个问题。
gulp-load-plugins这个插件能自动帮你加载package.json文件里的gulp插件。例如假设你的package.json文件里的依赖是这样的:
```
{
  "devDependencies": {
    "gulp": "~3.6.0",
    "gulp-rename": "~1.2.0",
    "gulp-ruby-sass": "~0.4.3",
    "gulp-load-plugins": "~0.5.1"
  }
}
```
然后我们可以在gulpfile.js中使用gulp-load-plugins来帮我们加载插件： 

var gulp = require('gulp');//加载gulp-load-plugins插件，并马上运行它var plugins = require('gulp-load-plugins')(); 

然后我们要使用gulp-rename和gulp-ruby-sass这两个插件的时候，就可以使用plugins.rename和plugins.rubySass来代替了,也就是原始插件名去掉gulp-前缀，之后再转换为驼峰命名。 

实质上gulp-load-plugins是为我们做了如下的转换 

plugins.rename = require('gulp-rename'); 

plugins.rubySass = require('gulp-ruby-sass'); 

gulp-load-plugins并不会一开始就加载所有package.json里的gulp插件，而是在我们需要用到某个插件的时候，才去加载那个插件。

最后要提醒的一点是，因为gulp-load-plugins是通过你的package.json文件来加载插件的，所以必须要保证你需要自动加载的插件已经写入到了package.json文件里，并且这些插件都是已经安装好了的。 
## 2 重命名
使用gulp-rename 

安装：npm install --save-dev gulp-rename 

用来重命名文件流中的文件。用gulp.dest()方法写入文件时，文件名使用的是文件流中的文件名，如果要想改变文件名，那可以在之前用gulp-rename插件来改变文件流中的文件名。
```
var gulp = require('gulp'),    rename = require('gulp-rename'),
    uglify = require("gulp-uglify");
 
gulp.task('rename', function () {
    gulp.src('js/jquery.js')
    .pipe(uglify())  //压缩
    .pipe(rename('jquery.min.js')) //会将jquery.js重命名为jquery.min.js
    .pipe(gulp.dest('js'));    //关于gulp-rename的更多强大的用法请参考https://www.npmjs.com/package/gulp-rename});
    ```
## 3 js文件压缩 
使用gulp-uglify 

安装：npm install --save-dev gulp-uglify 

用来压缩js文件，使用的是uglify引擎 
```
var gulp = require('gulp'),
    uglify = require("gulp-uglify");
 
gulp.task('minify-js', function () {
    gulp.src('js/*.js') // 要压缩的js文件
    .pipe(uglify())  //使用uglify进行压缩,更多配置请参考：
    .pipe(gulp.dest('dist/js')); //压缩后的路径});
    ```
## 4 css文件压缩
使用gulp-minify-css 

安装：npm install --save-dev gulp-minify-css 

要压缩css文件时可以使用该插件 
```
var gulp = require('gulp'),
    minifyCss = require("gulp-minify-css");
 
gulp.task('minify-css', function () {
    gulp.src('css/*.css') // 要压缩的css文件
    .pipe(minifyCss()) //压缩css
    .pipe(gulp.dest('dist/css'));
});
```
## 5 html文件压缩
使用gulp-minify-html 
安装：npm install --save-dev gulp-minify-html 
用来压缩html文件 
```
var gulp = require('gulp'),
    minifyHtml = require("gulp-minify-html");
 
gulp.task('minify-html', function () {
    gulp.src('html/*.html') // 要压缩的html文件
    .pipe(minifyHtml()) //压缩
    .pipe(gulp.dest('dist/html'));
});
```
## 6 js代码检查
使用gulp-jshint 

安装：npm install --save-dev gulp-jshint 

用来检查js代码 
```
var gulp = require('gulp'),
    jshint = require("gulp-jshint");
 
gulp.task('jsLint', function () {
    gulp.src('js/*.js')
    .pipe(jshint())
    .pipe(jshint.reporter()); // 输出检查结果});
```
## 7 文件合并
使用gulp-concat 

安装：npm install --save-dev gulp-concat 

用来把多个文件合并为一个文件,我们可以用它来合并js或css文件等，这样就能减少页面的http请求数了 
```
var gulp = require('gulp'),
    concat = require("gulp-concat");
 
gulp.task('concat', function () {
    gulp.src('js/*.js')  //要合并的文件
    .pipe(concat('all.js'))  // 合并匹配到的js文件并命名为 "all.js"
    .pipe(gulp.dest('dist/js'));
});
```
8 less和sass的编译
```
less使用gulp-less,安装：npm install --save-dev gulp-less
var gulp = require('gulp'),
    less = require("gulp-less");
 
gulp.task('compile-less', function () {
    gulp.src('less/*.less')
    .pipe(less())
    .pipe(gulp.dest('dist/css'));
});
```
```
sass使用gulp-sass,安装：npm install --save-dev gulp-sass
var gulp = require('gulp'),
    sass = require("gulp-sass");
 
gulp.task('compile-sass', function () {
    gulp.src('sass/*.sass')
    .pipe(sass())
    .pipe(gulp.dest('dist/css'));
});
```
## 9 图片压缩
可以使用gulp-imagemin插件来压缩jpg、png、gif等图片。
```javascript
安装：npm install --save-dev gulp-imagemin
var gulp = require('gulp');var imagemin = require('gulp-imagemin');var pngquant = require('imagemin-pngquant'); //png图片压缩插件gulp.task('default', function () {    return gulp.src('src/images/*')
        .pipe(imagemin({
            progressive: true,
            use: [pngquant()] //使用pngquant来压缩png图片
        }))
        .pipe(gulp.dest('dist'));
});
gulp-imagemin的使用比较复杂一点，而且它本身也有很多插件，建议去它的项目主页看看文档
```
## 10 自动刷新
使用gulp-livereload插件，安装:`npm install --save-dev gulp-livereload`。 

当代码变化时，它可以帮我们自动刷新页面 

该插件最好配合谷歌浏览器来使用，且要安装`livereload chrome extensio`n扩展插件,不能下载的请自行FQ。 
```javascript
var gulp = require('gulp'),
    less = require('gulp-less'),
    livereload = require('gulp-livereload');

gulp.task('less', function() {
  gulp.src('less/*.less')
    .pipe(less())
    .pipe(gulp.dest('css'))
    .pipe(livereload());
});

gulp.task('watch', function() {
  livereload.listen(); //要在这里调用listen()方法
  gulp.watch('less/*.less', ['less']);
});
```


