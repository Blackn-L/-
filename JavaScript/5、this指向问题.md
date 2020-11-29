## this的指向问题

* 全局作用域的this永远是window
* 函数内的this，只与函数怎么调用有关
1. 函数名()  => this指向window
2. 对象.函数名() => this指向对象
3. 事件处理函数(onclick) => this指向事件源（谁的事件,div,button) 