##angularJS中的promise和$q
在angular中对promise已经了封装，即$q，它是对promise的一种实现，可以在依赖中直接注入$q这个服务。它和ES6中的promise在语法上有一点区别，但都是为了让代码跳出异步的回调地狱。
###ES6中的promise
在ES6的语法中，将promise写为了标准，并规定了相应的语法结构，下面进行简单的介绍，主要参考了阮一峰老是的ES6入门这本书。  
因为在JS中存在着大量的异步请求，造成了会有很多嵌套的回调函数，使得代码写完之后难以维护并且代码结构不够美观。promise在社区出现之后，使得JS能够用同步的写法来处理异步请求。在ES6中，将promise写进了标准，提供原生的promise对象。  
####promise的三种状态
promise有三种状态，用来表示异步操作的完成进度。初始状态是Pending，即进行中；Resolved，即Fulfilled，完成状态，如果是这个状态，就会调用resolve方法，并进行数据的传递；第三种是Reject，即拒绝状态，会调用reject方法，并将传递错误的具体信息。当pending状态改变之后，就是一个不可逆的过程。
###promise的基本用法
通过new一个Promise对象来创建原始的对象：  
<pre>
	var p = new Promise(function(resolve,reject){
		// ... some code
		if(/* 操作成功 */){
			resolve(value);
		}else{
			reject(errorValue);
		}
	});
</pre>  
在上面的代码中，resolve和reject都是已经提供好的函数，不用我们自己实现。当异步操作成功后，通过resolve函数来传递数据；当操作失败后，用reject函数来传递错误的信息。  
当promise对象生成之后，可以通过then方法来对后续的数据进行操作。then接受两个函数作为参数，第一个函数在pending变成Fulfilled时调用的函数，函数中的数据就是resolve中传递过来的数据；第二个函数在pending变成reject时调用，函数重点数据是reject中传递过来的数据。  
<pre>
	promise.then(function(data){
		//success,其中的data为resolve(value)中的value
	},function(error){
		//fail,其中error为reject(errorValue)中的errorValue
	});
</pre>  
then()方法还支持链式调用，只要在return一个数据或者promise即可。  
ES6中的promise还有一些其他的方法，在这就不一一介绍了。  
###angularJS中的$q和promise
在angular中，通过$q这个服务可以产生一个promise对象，这个对象也可以叫延迟对象。通过$q的defer()方法可以创建一个延迟对象deferred，通过这个对象可以实现promise异步处理。
####deferred对象
deferred对象有一个属性和三个方法：  
 
* promise属性，这个属性用来创建一个promise对象。  
* resolve(value)，这个方法用来执行promise，value可以是字符串或者对象  
* reject(value)，这个方法用来拒绝promise  
* notify(value)，这个方法用来获取promise的执行状态，并进行传递。
  
####promise对象也有几个方法：  
* then(resolveF,rejectF,notifyF)，这三个函数分别对应deferred对象的三个方法，对应的数据可以在他们之间进行传递。  
* catch(errorF)，用来在promise对象的最后捕获错误。
* finally(callback)。  
promise对象也支持链式的then操作，在回调函数中使用return来传递数据就可以实现链式调用。  
下面是一个简单的例子：  
<pre>
	angular
	.module('app',[])
	.controller('c2',['$scope','$q',function($scope,$q){
		
		$scope.message = '';

		$scope.getNum = function(){
			var num = parseInt(Math.random()*10,10);

			var deferred = $q.defer();
			var promise = deferred.promise;

			promise.then(function(data){
				$scope.message = data;
			},function(error){
				$scope.message = error;
			});

			if(num > 5){
				$scope.message = "产生的随机数" + num + "大于5";
			}else{
				$scope.message = "产生的随机数" + num + "小于5";
			}
		};
	}])
</pre>

** 更多的例子在angular-test中$q文件夹中，其中包括链式操作和Ajax异步请求 **