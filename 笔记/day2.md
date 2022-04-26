## 1. 犯的错误:
1. 项目阶段，左侧菜单目录，只能有项目文件夹
2. 联想电脑安装node_modules依赖包的时候，经常丢包。npm install --save axios --force
3. 单词错误
4. 路由理解
KV：K--->URL  V---->相应的组件
```js
配置路由：
     ------路由组件
     -----router--->index.js
                  import Vue  from 'vue';
                  import VueRouter from 'vue-router';
                  //使用插件
                  Vue.use(VueRouter);
                  //对外暴露VueRouter类的实例
                  export default new VueRouter({
                       routes:[
                            {
                                 path:'/home',
                                 component:Home
                            }
                       ]
                  })
    ------main.js   配置项不能瞎写

$router:进行编程式导航的路由跳转
this.$router.push|this.$router.replace
$route:可以获取路由的信息|参数
this.$route.path
this.$route.params|query
this.$route.meta
```

## 2. 编程式导航路由跳转到当前路由(参数不变), 多次执行会抛出NavigationDuplicated的警告错误?
注意:编程式导航（push|replace）才会有这种情况的异常，声明式导航是没有这种问题，因为声明式导航内部已经解决这种问题。
这种异常，对于程序没有任何影响的。
为什么会出现这种现象:
由于vue-router最新版本3.5.2，引入了promise，当传递参数多次且重复，会抛出异常，因此出现上面现象,
第一种解决方案：是给push函数，传入相应的成功的回调与失败的回调
`this.$router.push({name:"search",params:{keyword:this.keyword},query:{k:this.keyword.toUpperCase()}},()=>{},()=>{})`
第一种解决方案可以暂时解决当前问题，但是以后再用push|replace还是会出现类似现象，因此我们需要从‘根’治病；
```js
//先把VueRouter原型对象的push保存一份
let originPush = VueRouter.prototype.push;
let originReplace = VueRouter.prototype.apply;

//重写push|replace
//第一个参数：告诉原来的push方法，往哪里跳转（传递哪些参数）
VueRouter.prototype.push = function (location,resolve,reject){
	if(resolve && reject){
		//call与apply区别
		//相同点：都可以调用函数一次，都可以篡改函数的上下文一次
		//不同点：call与apply传递参数：call传递参数用逗号隔开，apply方法执行，传递数组
		originPush.call(this,location,resolve,reject);
	}else{
		originPush.call(this,location,() => {},() => {});
	}
}
VueRouter.prototype.replace = function (location,resolve,reject){
	if(resolve && reject){
		originReplace.call(this,location,resolve,reject);
	}else{
		ortginReplace.call(this,location,()=>{},()=>{})
	}
}
```



## 3. 将Home组件的静态组件拆分
2.1静态页面（样式）
2.2拆分静态组件
2.3发请求获取服务器数据进行展示
2.4开发动态业务
拆分组件：结构+样式+图片资源
一共要拆分为七个组件
2.5三级联动组件完成

- 由于三级联动，在Home、Search、Detail都有使用，把三级联动注册为全局组件。
- 好处：只需要注册一次，就可以在项目任意地方使用
```js
//全局组件注册方式，在main.js入口文件中
import TypeNav from '@/components/TyoeNav'
//第一个参数：组件名；第二个参数：哪一个组件
Vue.component(TypeNav.name, TypeNav)
```

## 4. Postman测试接口
- 如果服务器返回的数据code字段为200，代表服务器返回数据成功
- 整个项目，接口前缀有/api字样

## 5. axios二次封装
```js
//安装axios
npm install axios
```
1. AJAX:客户端可以'敲敲的'向服务器端发请求，在页面没有刷新的情况下，实现页面的局部更新。
2. 进行ajax请求的方式：XMLHttpRequest、$、fetch、axios
3. 跨域:如果多次请求协议、域名、端口号有不同的地方，称之为跨域
- 解决方法JSONP、CROS、代理
4. 为什么需要进行二次封装axios？
- 请求拦截器、响应拦截器：请求拦截器可以在发送请求之前处理一些业务；响应拦截器：当服务器数据返回以后，可以处理一些事情。
5. 工作的时候src目录下的API文件夹，一般关于axios二次封装的文件
```js
//在api目录下的request.js文件中，对axios进行二次封装
import axios from "axios"

//1.利用axios对象的方法create，去创建一个axios实例
//2.request就是axios，只不过稍微配置一下
const request = axios.create({
	//配置对象
	//基础路径 ，发请求的时候，路径中会出现api。因为项目请求路径中都有/api,以后请求只用写api之后的路径即可
	baseURL:'/api',
	//设置请求超时时间
	timeout:5000,
})
//请求拦截器：在发送请求前，请求拦截器可以检测到，可以在请求发出去之前做些事情
requests.interceptors.request.use((config)=>{
	//config，配置对象，对象里有一个很重要的属性，headers请求头
	return config
})
//响应拦截器
requests.interceptors.response.use((res)=>{
	//成功的回调函数：服务器响应数据回来以后，响应拦截器可以检测到，可以做一些事情
	return res.data;
},(error) => {
	//失败的回调
	return Promise.reject(new Error('fail'))
})


//对外暴露
export default axios
```
6. 接口的统一管理
- 项目很小：可以在组件的生命周期函数中发请求
- 项目很大：`axios.get('xxx')`
```js
//在api目录下index.js文件
//例如三级联动接口
//发请求：axios发请求返回Promise对象
export const reqCategoryList = () => requests({url:'/product/getBaseCategoryList',method:'get'})
```
**只配置这些有跨域问题，接口是后端服务器提供，ip地址与本地服务器不同,还要配置代理跨域**

```json
	 //在vue.config.js中配置代理跨域，也就是配置webpack
     devServer: {
          proxy: {
               '/api': {
                    target: 'http://39.98.123.211',
               },
          },
     },
```
7. nprogress进度条的使用
```js
//安装
npm install nprogress
```
在request.js文件中使用
nprogress方法的属性: start：代表进度条考试  done：代表进度条结束

## 6. vuex:今晚务必vuex复习一下
vuex:Vue官方提供的一个插件，插件可以管理项目共用数据。
vuex：书写任何项目都需要vuex？项目大的时候才需要vuex
核心概念：state,mutations,actons,getters,moudles
项目大的时候，需要有一个地方‘统一管理数据’即为仓库store
Vuex基本使用: 
```js
//创建store文件夹，下创建index.js文件
import Vue from 'Vue'
import Vuex from 'Vuex'
//需要使用插件一次
Vue.use(Vuex)
//state:仓库存储数据的地方
const state = {};
//mutations:修改state的唯一手段
const mutations = {};
//action:处理action，可以书写自己的业务逻辑，也可以处理异步
const actions = {}
//getters:理解为计算属性，用于简化仓库数据，让组件获取仓库数据更加方便
const getters = {}
//对外暴露Store类的一个实例
export default new Vuex.Store({
	//注册
	state,
	mutations,
	actions,
	getters
})
```



















