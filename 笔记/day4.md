## 1. 复习一下
三级联动业务:
- 前面基础课程当中v-for很少使用index，以后在写项目的时候，index索引值切记加上
- 防抖与节流【面试经常出现】
- vuex可以模块式开发
vuex经常用的套路是state、mutations、actions、getters、modules



## 2. 注册全局组件
- 在home模块当中，使用了一个功能三级联动功能---->[typeNav]
- 在search模块当中，也使用三级联动的功能------->[typeNav]
- 注意的事项
	- 注意1：以后在开发项目的时候，如果发现某一个组件在项目当中多个地方出现频繁的使用
	咱们经常把这类的组件注册为全局组件。
	注册全局组件的好处是什么：只需要注册一次，可以在程序任意地方使用
	- 注意2:咱们经常把项目中共用的全局组件放置于components里面，以后需要注意，项目当中全局组件（共用的组件）一般放置于components文件夹中
	- ：全局组件只需要注册一次，就可以在项目当中任意的地方使用，注册全局组件一般是在入口文件注册。

## 3. 过渡动画
- 最早接触的时候:CSS3
- Vue当中也有过渡动画效果---transition内置组件完成
**注意1,在Vue当中，你可以给 （某一个节点）|（某一个组件）添加过渡动画效果
但是需要注意，节点|组件务必出现v-if|v-show指令才可以使用。**
```html
<transition name = "sort">
	<div class = "sort" v-show = "true"></div>
</transition>
```
```css
//过渡动画样式(根据transition标签的类名)
//过渡动画的开始状态
.sort-enter {

}
//过渡动画结束状态
.sort-enter-to {

}
//定义动画时间、速率
.sort-enter-active {
    transition: all .5s linear
}
```



## 4. TypeNav三级联动性能优化
- 项目：home切换到search或者search切换到home，你会发现一件事情，组件在频繁的向服务器发请求，获取三级联动的数据进行展示。
项目中如果频繁的向服务器发请求，影响性能，因此咱们需要进行优化。
- 为什么会频繁的向服务器发请求获取三级联动的数据?
因为路由跳转的时候，组件会进行销毁的【home组件的created：在向vuex派发action，因此频繁的获取三级联动的数据】只需要发一次请求，获取到三级联动的数据即可，不需要多次。
最终解决方案：在App.APP组件中的`mounted()`只会执行一次
```js
mounted(){
	//派发一个action||获取商品分类的三级列表的数据
	this.$store.dispatch('getCategoryList')
}
```
- 项目性能优化手段有哪些？
v-if|v-show选择
按需加载          lodash、ant
防抖与节流
请求次数优化

## 5. query参数和params参数合并
- 点击搜索按钮，要判断路径中是否有query参数，有的话要合并query参数，否则query参数要被params参数覆盖。
```js
 if (this.$route.query) {
	//本身点击搜索按钮，当年只是携带params，如果路径当中存在query参数，是需要把query参数页携带给search
	let location = {
		name: "search",
		params: { keyword: this.keyword || undefined },
	};
	location.query = this.$route.query;
}
```
## 6. Mock数据
- 开发listContainer|Floor组件业务？
- 接下来需要开发listContainer与floor组件
- 场景:开发项目，产品经理画出原型，前端与后端人员需要介入（开发项目），leader（老大）刚开完会，前端与后端负责哪些模块，后端人员（....开发服务器），前端人员【项目起步、开发静态页面、查分静态组件】，回首一看后台，接口没有写好，像这种情况，前端人员可以mock一些数据【前端程序员自己模拟的一些假的接口】，当中工作中项目上线，需要把mock数据变为后台给的接口数据替换。
#### mock数据
- 注意：因为后台老师没有给我们写好其他接口【老师们特意的：因为就是想练习mock数据】但是项目中mock数据，你就把他当中真实接口数据用就行。
- 注意：mock（模拟数据）数据需要使用到mockjs模块，可以帮助我们模拟数据。
- 注意：mockjs【并非mock.js mock-js】
[mock官网](http://mockjs.com/  )
- mock官网一句话：生成随机数据，拦截 Ajax 请求
1. 第一步:安装依赖包mockjs
2. 第二步：在src文件夹下创建一个文件夹，文件夹mock文件夹。
3. 第三步:准备模拟的数据
把mock数据需要的图片放置于public文件夹中！
比如:listContainer中的轮播图的数据
```js
[
   {id:1,imgUrl:'xxxxxxxxx'}, 
   {id:2,imgUrl:'xxxxxxxxx'}, 
   {id:3,imgUrl:'xxxxxxxxx'}, 
]
```
4. 第四步：在mock文件夹中创建一个server.js文件
    注意：在server.js文件当中对于banner.json||floor.json的数据没有暴露，但是可以在server模块中使用。
    对于webpack当中一些模块：图片、json，不需要对外暴露，因为**默认就是对外暴露。**
5. 第五步:通过Mock.mock方法进行模拟数据
```js
//mock数据：第一个参数为请求地址，第二个参数为请求数据
Mock.mock(url,{code:200,data:banner})
```
7. 第六步:回到入口文件，引入mockServer.js`import "@/mock/serve";`
    mock需要的数据|相关mock代码页书写完毕，关于mock当中serve.js需要执行一次，如果不执行，和你没有书写一样的。
8. 第七步:在API文件夹中创建mockRequest【axios实例：baseURL:'/mock'】
    专门获取模拟数据用的axios实例。
    在开发项目的时候：切记，单元测试，某一个功能完毕，一定要测试是否OK
## 7. swiper基本的使用
- swiper可以在移动端使用？还是PC端使用？
答：swiper移动端可以使用，pc端也可以使用。
- swiper常用于哪些场景？
常用的场景即为轮播图----【carousel:轮播图】
swiper最新版本为7版本的，项目当中使用的是5版本
[swiper官网](https://www.swiper.com.cn/)





























































