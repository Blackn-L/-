## this的指向问题

* 全局作用域的this永远是window
* 函数内的this，只与函数怎么调用有关
1. 函数名()  => this指向window
2. 对象.函数名() => this指向对象
3. 事件处理函数(onclick) => this指向事件源（谁的事件,div,button) 

## 改变this指向的三个方法call()、apply()、bind()
+ call() 立即执行函数，第一个参数是函数内this要指向的对象，第二个开始是给函数传递的参数，以`,`分隔
+ apply() 立即执行函数，第一个参数是函数内this要指向的对象，第二个参数是一个数组，通过数组往函数传参
+ bind() 不执行原函数，返回一个改变this后的新函数，第一个参数是函数内this要指向的对象
```JavaScript
let obj = {
    name: 'Jack',
    age: 22,
    
}
window.name = 'Lisa'
let fun =  function() {
        console.log(this.name)
    }
fun() // Lisa

// call()
fun.call(obj,'22','33') // Jack

//  apply
fun.apply(obj,['22','33']) // Jack

// bind()
let newFun = fun.bind(obj)
newFun() // Jack
```