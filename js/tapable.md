* tapable 的用法
```js
var {SyncHook} = require("tapable");
class Car {
	constructor() {
		this.hooks = {
			accelerate: new SyncHook(["newSpeed"])
		};
	}
}

const myCar = new Car();
myCar.hooks.accelerate.tap("WarningLampPlugin", (a) => {
  console.log(a);
});
myCar.hooks.accelerate.tap("WarningLampPlugin", (a) => {
  console.log(a);
});
myCar.hooks.accelerate.call({a:1});
```
```js
/**
function createCompileDelegate(name, type) {
	return function lazyCompileHook(...args) {
		this[name] = this._createCall(type);
		return this[name](...args);
	};
}
**/
//_createCall -> 
(function anonymous(newSpeed) {
  "use strict";
  var _context;
  var _x = this._x;//回调集合
  var _fn0 = _x[0];
  _fn0(newSpeed);
  var _fn1 = _x[1];
  _fn1(newSpeed);
})
```
* AsyncParallelHook
```js
(function anonymous(newSpeed, _callback) {
  "use strict";
  var _context;
  var _x = this._x;
  var _fn0 = _x[0];
  var _hasResult0 = false;
  var _promise0 = _fn0(newSpeed);
  if (!_promise0 || !_promise0.then)
    throw new Error('Tap function (tapPromise) did not return promise (returned ' + _promise0 + ')');
  _promise0.then(_result0 => {
  _hasResult0 = true;
  _callback();
  }, _err0 => {
  if(_hasResult0) throw _err0;
  _callback(_err0);
  });
})
```
* AsyncSeriesHook 按照顺序依次执行 res(11) 的值不会传递下去
* AsyncSeriesWaterfallHook res(11) 的值会传递下去
```js
class Car {
	constructor() {
		this.hooks = {
			accelerate: new AsyncSeriesHook(["newSpeed"])
		};
	}
}

const myCar = new Car();

myCar.hooks.accelerate.tapPromise("dd", s => {
	console.log(s,"one")
	return new Promise((res,rej) => {
		setTimeout(()=>{res("111")},200)
	});
});
myCar.hooks.accelerate.tapPromise("dd", s => {
	console.log(s,"tow")
	return Promise.resolve(s + 1);
});

myCar.hooks.accelerate.callAsync(1,err => {
	console.log(err)
});
```
* intercept
```js
var {SyncHook,AsyncParallelHook,SyncBailHook,AsyncSeriesBailHook,AsyncSeriesHook,AsyncSeriesWaterfallHook} = require("tapable");
class Car {
	constructor() {
		this.hooks = {
			accelerate: new AsyncSeriesBailHook(["newSpeed"])
		};
	}
}

const myCar = new Car();

myCar.hooks.accelerate.tapPromise({name:"dd",context:true}, (context,s) => {
	context.a++;
	return new Promise((res,rej)=>{
		res();
	});
});
myCar.hooks.accelerate.tapPromise({name:"dd",context:true}, (context,s) => {
	context.a++;
	return new Promise((res,rej)=>{
		res(context);
	});
});
myCar.hooks.accelerate.intercept({
	context:true,
	tap(context,tapInfo){
		if(!context.a){
			context.a = 1;
		}
	}
});


myCar.hooks.accelerate.callAsync(1,(err,result) => {
	console.log(err,result,a);
});

```
* interface
```js
interface Hook {
	tap: (name: string | Tap, fn: (context?, ...args) => Result) => void,
	tapAsync: (name: string | Tap, fn: (context?, ...args, callback: (err, result: Result) => void) => void) => void,
	tapPromise: (name: string | Tap, fn: (context?, ...args) => Promise<Result>) => void,
	intercept: (interceptor: HookInterceptor) => void
}

interface HookInterceptor {
	call: (context?, ...args) => void,
	loop: (context?, ...args) => void,
	tap: (context?, tap: Tap) => void,
	register: (tap: Tap) => Tap,
	context: boolean
}

interface HookMap {
	for: (key: any) => Hook,
	tap: (key: any, name: string | Tap, fn: (context?, ...args) => Result) => void,
	tapAsync: (key: any, name: string | Tap, fn: (context?, ...args, callback: (err, result: Result) => void) => void) => void,
	tapPromise: (key: any, name: string | Tap, fn: (context?, ...args) => Promise<Result>) => void,
	intercept: (interceptor: HookMapInterceptor) => void
}

interface HookMapInterceptor {
	factory: (key: any, hook: Hook) => Hook
}

interface Tap {
	name: string,
	type: string
	fn: Function,
	stage: number,
	context: boolean
}
interface Hook {
	isUsed: () => boolean,
	call: (...args) => Result,
	promise: (...args) => Promise<Result>,
	callAsync: (...args, callback: (err, result: Result) => void) => void,
}

interface HookMap {
	get: (key: any) => Hook | undefined,
	for: (key: any) => Hook
}
```