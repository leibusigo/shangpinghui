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