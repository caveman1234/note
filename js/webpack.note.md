* 外部模块不通过webpack编译script引入，可以用cmd,amd,commonjs引入
```js
module.exports = {
  externals: {
    jquery: '$'//$ 被视为一个全局变量，可以通过模块在webpack中流动,jquery 被视为模块的名字 
  }
}
```
* 内部实现
```js
(function(modules){
  var installedModules = {};
 	function __webpack_require__(moduleId) {
 		if(installedModules[moduleId]) {
 			return installedModules[moduleId].exports;
 		}
 		var module = installedModules[moduleId] = {
 			i: moduleId,
 			l: false,
 			exports: {}
 		};
     //modules["jquery"]
 		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
 		module.l = true;
 		return module.exports;
 	}
})(
  {
    "jquery" : function(module, exports) {
      eval("module.exports = $;")
    }
  }
)
```
* 2 将全局jQuery 变量 下的 map 函数 在webpack中当作模块流动,moduleId为subtract
```js
externals: {
    subtract: ['jQuery', 'map']
  }

"subtract":eval("module.exports = jQuery[\"map\"];"）
```
