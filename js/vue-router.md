```js
//VUE动态路由
var exportRouter = new Router({
  routes: []
});
export default exportRouter;
Vue.use(Router)
setTimeout(()=>{
  exportRouter.addRoutes(routes);
},1000);
```