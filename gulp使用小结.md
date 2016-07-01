#gulp使用小结
进入公司后，接触了一些gulp自动化处理方面的东西，现在将一些常用的记录下来，方便查看。

###1. gulp的安装
在安装node.js的前提下，首先在全局环境下安装gulp：  
&nbsp;&nbsp;`npm install -g gulp`  
全局安装后，如果想在开发环境中使用，在命令行中进入项目的目录，执行下面的命令，将gulp加入到package.json中  
&nbsp;&nbsp;`npm install -save-dev gulp`  
**如果使用npm速度太慢，可以安装淘宝的镜像cnpm，安装之后，用cnpm来替换npm**  

###2. gulp主要的API
####(1) gulp.src()
用来读取文件，这个方法接受一个参数，可以是一个表示匹配文件的字符串，也可以是一个包含多个匹配正则表达式的数组。  
`gulp.src("./js/*.js")`   
`gulp.src(["./css/*.css","./js/*.js","*.html"])`  
####(2) gulp.dest()
用来写入文件，方法接受一个参数，即一个写入文件的路径，也可以是一个数组，但是一般不会使用数组。  
`gulp.dest(./dist/js")`
####(3) gulp.pipe()
这是gulp的管道函数，用来实现文件的流传输，是基于node的管道处理实现的。文件从输入到输出，中间对文件的一系列处理就是在pipe中进行的。
####(4) gulp.task()
这个函数用来定义gulp可以运行的任务，接受三个参数。第一个为name，表示任务的名字，用来gulp识别任务。第二个参数为当前任务需要依赖的任务列表，用一个数组表示，如果没有依赖那么可以省略。第三个参数是一个函数，把任务要执行的代码都放在里面，这个参数也可以省略。  
`gulp.task("task1",["rename","jsmin"],function(){})`  
`gulp.task("task2",function(){})`
####(5) gulp.watch()
这个函数用来监听文件的变化。第一个参数和gulp.src的参数一样，表示要监听文件的路径，第二个参数是一个数组，当监听的文件改变时，会依次执行数组中的task。
###3 使用gulp
要使用gulp，要在项目的根目录新建一个gulpfile.js的文件，在这个文件中定义task，然后用命令行进入到这个项目，输入  
`gulp taskname`  
这个taskname就是gulpfile中定义的task。  
在gulpfile文件中，要引入即将使用的插件，格式如下：
  
	var gulp =  require('gulp');
	var browserSync = require('browser-sync');  
	var uglify = require('gulp-uglify');  
	var minifyCss = require('gulp-minify-css'); 
	var concat = require('gulp-concat');  
	var sass = require('gulp-sass'); 

###4 常用的gulp插件
常用的gulp插件有如下几种：   

* gulp-uglify: 用来压缩js文件  
* gulp-rename: 用来重命名文件
* gulp-minify-css: 用来压缩css文件
* gulp-minify-html: 用来压缩HTML文件
* gulp-concat: 用来合并文件
* gulp-less: 用来将less文件转换为css文件
* gulp-sass: 用来将scss文件转换为css文件
* gulp-imagemin: 用来压缩JPG、png、GIF等图片
* gulp-ng-annotate: 将angular代码中的依赖自动注入  

插件的安装方式为：  
`npm install --save-dev gulpname`  
其中的gulpname就是插件的名称。
###5 常用的代码

压缩js文件：  
	gulp.task('jsmin',function(){
		 gulp.src('./js/*.js')
	         .pipe(uglify())
	         .pipe(gulp,dest('dist/js'));
	});
压缩css文件:  

	gulp.task('cssmin',function(){
		 gulp.src('./css/*.css')
	      	 .pipe(minifyCss())
	         .pipe(gulp.dest('./dist/css'));
	});  

合并js文件：  

	gulp.task('concat',function(){
		gulp.src('./js/*.js')
	        .pipe(concat())
	        .pipe(gulp.dest('dist/js'));
	});  
 
压缩任务集合：  

	gulp.task('final',['jsmin','cssmin','minify-html','concat']);


###6 实现浏览器的自动刷新
在gulp中使用browser-sync，实现文件的监听和浏览器的自动刷新。  
在项目中，因为用到了sass和compass，在使用compass watch监听scss文件的时候会打开另一个终端页面，调试的时候不太方便。于是弃用了compass这个框架，而选用了sassCore这个这个sass库，它参考了compass和其他一些优秀的sass库，并且提供了友好的文档。这样就将scss文件的编译整合到gulp中了，代码如下：  

	var gulp = require('gulp');
	var browserSync = require('browser-sync');
	var sass = require('sass');
	var uglify = require('gulp-uglify');
	var minifyCss = require('gulp-minify-css');

	//压缩JS文件
	gulp.task('minify-js',function(){
	  gulp.src('./js/*.js')
	      .pipe(uglify())
	      .pipe(gulp,dest('dist/js'));
	});

	//压缩CSS文件
	gulp.task('minify-css',function(){
	  gulp.src('./css/*.css')
	      .pipe(minifyCss())
	      .pipe(gulp.dest('./dist/css'));
	});
	
	gulp.task('server',function(){
		browserSync.init({
			server: {
				baseDir: './'
			}
		});
		
		gulp.watch('./sass/*.scss',['sass'],browserSync);
		gulp.watch('./js/*.js',browserSync.reload);
	    gulp.watch('./css/*.css',browserSync.reload);
	    gulp.watch("./*.html",browserSync.reload);
	});

