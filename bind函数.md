###实现bind
代码如下：  
<pre>
Function.prototype.bind = Function.prototype.bind || function () {
  var that = this;

  //除去上下文变量
  var outArg = Array.prototype.slice.call(arguments);
  var context = outArg.shift();

  return function() {
    var innerArg = Array.prototype.slice.call(arguments);
    return that.apply(context, outArg.concat(innerArg));
  };
};
</pre>
如果浏览器不支持bind函数，则使用后面的兼容代码。
