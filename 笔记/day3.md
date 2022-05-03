## 1. 一级分类背景颜色添加 
- 1. CSS，伪类，改变`backgroundcolor`
- 2. JS套路 v-for的索引值index

## 2.函数的防抖和节流
- 正常：事件触发非常频繁，而且每一次的触发，回调函数都要去执行（如果时间很短，而回调函数内部有计算，那么很可能出现浏览器卡顿）
- 节流：在规定的间隔事件范围内不会重复触发回调，只有大于这个时间间隔才会触发回调，把频繁触发变为少量触发。
- 防抖：前面所有的触发都被取消，最后一次执行在规定的时间之后才会触发，也就是连续快速的触发，只会执行一次。
```js
//！！！debounce函数的返回值应该是个函数，inp元素的输入响应事件
inp.oninput = debounce(function(){
    console.log(this);
},500)
//闭包实现防抖
//两个参数：需要执行的逻辑fn，延时delay
function debounce(fn,delay){
	//初始化延时
	let t = null;
	//利用闭包：能够读取其它函数内部变量(t)的函数 1.内部函数使用了外部函数的变量t 2.外部函数的返回值是内部函数
	return function(){
		//当t不为0时，清除延时器
		if(t !== null){
			clearTimeout(t)
		}
		//t=0时，经过设定的延时，执行内部函数
        //注意!!!这个时候fn()在延时器的箭头函数中执行，this指向window。要改变this指向
		t = setTimeout(()=>{
            //用call()改变this指向，window调用的fn()。延时器里的this指向inp元素
			fn.call(this);
		},delay)
	}

}

//手写节流
//每隔一段事件触发一次
function throttle(fn,delay){
    let flag = true
    return function(){
        if(flag){
            setTimeout(() => {
                fn.call(this)
                flag =true
            },delay)
        }
    }
}
```
## 3.三级联动组件的路由跳转与传递参数
#### 路由跳转
- 声明式导航：router-link
- 编程式导航：push|replace

#### 三级联动
- 如果使用声明式导航，可以实现路由的跳转与传递参数，但会出现卡顿现象。
router-link：会生成一个组件，当服务器返回数据后，会循环出很多router-link组件。从而出现卡顿现象。
- 最好的方式：事件委派 + 编程式导航
	- 问题1：事件委派将全部子节点的事件委派给父亲节点，无法确定点击的一定是a标签
	- 问题2：确定了a标签，无法区分一级、二级、三级分类的标签
- 第一个问题：把子节点中的a标签，加上自定义属性`data-catagoryName`。通过`let elememt = event.target`方法获取触发事件的节点，找到带有`data-catagoryName`的节点。
节点有一个dataset属性，可以获取节点的自定义属性与属性值。
通过解构赋值将想要的属性解构出来`let {categoryname} = element.dataset`
- 第二个问题：给三级分类的标签分别加上自定义属性`data-catagory1ID`等。通过识别标签中是否有这些属性来区分。