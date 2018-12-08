```js
(function(root,factory){

  if(typeof exports === "object" && typeof module === "object"){
    //node环境
    module.exports = factory();
  }else if(typeof define === "function" && define.amd){
    //require环境
    define("pluginName",factory);
  }else if(typeof exports === "object"){
    //es6环境
    exports.pluginName = factory();
  }else {
    //this环境
    root.pluginName = factory();
  }
})(this,function(){
    return (function(modules){
      //数组的下表数字对应对象的key(moduleId) 0 -> "0"
      //缓存所有的模块
      var installedModules = {};
      function __webpack_require__(moduleId){
        if(installedModules[moduleId]){
          //被require的时候如果已经被安装过就返回这个模块对外大exports
          return installedModules[moduleId].exports;
        }
        //如果没被安装过生成初始安装
        var module = installedModules[moduleId] = {
          id:moduleId,
          loaded:false,
          exports:{}
        };
        //对应模块函数被执行，对外暴露将挂在出事安装模块的exports上，同时模块具有require的能力
        modules[moduleId].call(module.exports,module,module.exports,__webpack_require__);
        //已加载
        module.loaded = true;
        //返回需要暴露的信息
        return module.exports;
      }
      window.__webpack_require__ = __webpack_require__;
      //缓存模块
      __webpack_require__.m = modules;
      //缓存已安装过的模块
      __webpack_require__.c = installedModules;
      //判断对象是否有属性(不包含原型上)
      __webpack_require__.o = function(object,property){
        return Object.prototype.hasOwnProperty.call(object,property);
      }
      //定义模块只能get不能修改模块（浅）
      __webpack_require__.d = function(exports,name,getter){
        //exports对象上没有name的key就创建此模块
        if(!__webpack_require__.o(exports,name)){
          Object.defineProperty(exports,name,{
            configurable:false,
            enumerable:true,
            get:getter
          });
        }
      }
      //入口
      return __webpack_require__(0);
    })(
      [
        /* 0 */
        function(module,__webpack_exports__,__webpack_require__){
          __webpack_require__.d(__webpack_exports__,"a",function(){return {a:1}});
        },
        /* 1 */
        function(module,__webpack_exports__,__webpack_require__){
          var temp = __webpack_require__("0");
          temp.b = 2;
          __webpack_exports__["b"] = temp;
        },
      ]
    )
  }
);
```