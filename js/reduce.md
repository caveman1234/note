```js
var fn1 = (a) => a + 1;
var fn2 = (a) => a + 1;
var fn3 = (a) => a + 1;
var fn4 = (a) => a + 1;
var arr = [fn1,fn2,fn3,fn4];
var f = arr.reduce((acc,fn) => (...args) => acc(fn(...args)));
//f = (...args) =>  fn1(fn2(fn3(fn4(...args))))
```