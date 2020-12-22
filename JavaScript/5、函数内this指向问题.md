# 函数内this的指向问题

要更好的了解`this`建议先去看下`作用域`

## 一、介绍

* `this`被定义在`所有函数`的作用域中

* 函数的this指向当前函数的执行上下文对象

* 要找到函数的this指向哪里，需要找到该函数被调用的位置

* 该函数被谁调用，就指向谁

## 二、绑定规则

按优先级，`new（new绑定） > call、apply（显式绑定） > 上下文对象调用（隐式绑定）> 默认绑定   `，存在多个绑定，以优先级高的为主

### 1、new绑定

> `new`调用的函数，this指向的是新创建的对象

JavaScript中，包括内置对象在内的所有函数都可以用`new`调用，这种函数调用成为`构造函数调用`，不存在`构造函数`，只有对函数的`构造调用`

```javascript
function Fun(name) {
    this.name = name
}
var f1 = new Fun('Jack') // 
console.log(f1) // Jack
```

### 2、显式绑定

> `call、apply`显式绑定的，this指向指定的对象

```javascript
function fun() {
    return this.name
}
obj1 = {
    name: 'obj1'
}
var f2 = fun.call(obj1) // 指向obj1,f2=obj1
console.log(f2) // obj1
```

通过`call`、`apply`、`bind`方法，强制将函数的this指向其他对象

三者用法：

+ call() 立即执行函数，第一个参数是函数内this要指向的对象，第二个开始是给函数传递的参数，以`,`分隔
+ apply() 立即执行函数，第一个参数是函数内this要指向的对象，第二个参数是一个数组，通过数组往函数传参
+ bind() 不执行原函数，返回一个改变this后的新函数，第一个参数是函数内this要指向的对象

```javascript
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

### 3、隐式绑定

>  在某个上下文对象中调用（隐式绑定），this指向该上下文对象

```javascript
var name = 'window'
obj2 = {
    name: 'obj2',
    f3: fun
}
var a = obj2.f3()
console.log(a) // obj2

obj3 = {
    name: 'obj3',
    f4: fun()
}
var b = obj3.f4
console.log(b) // window, obj3对象内执行了fun函数，该函数没有被1、2、3种方式绑定，所以轮到了默认绑定，默认绑定绑定到window

```

对象属性引用链中只有最后一层会影响调用位置，也就是如果有多个对象，则最靠近函数的那个对象才会影响函数的调用位置

```javascript
var name = 'window'
var obj1 = {
    name:'obj1',
    f: fn
}
var obj2 = {
    name: 'obj2',
    obj1: obj1
}
function fn() {
	console.log(this.name)
}
obj1.f() // obj1
obj2.obj1.f() // obj1
```

**常见问题：隐式绑定的函数会丢失绑定对象，丢失后会应用默认绑定**

```javascript
var name = 'window'
var obj = {
    name: 'obj1',
    fn: function() {
        console.log(this.name)
    }
}

var f1 = obj.fn
f1() // window, fn存储的是一个函数地址，所以f1得到的也是一个函数地址，相当于在全局环境执行了一个函数，也叫丢失绑定
obj.fn() // obj1
```



### 4、默认绑定

>  默认绑定，**函数体**（非调用位置）处于非严格模式绑定到`window`，处于严格模式绑定到`undefined`

``` javascript
    var name = 'window'
    var name1 = fun()
    console.log(name1) // window
```

补充一个知识点，这里一直用`var`定义而不是用`let`定义，是由于`var`定义变量既是全局变量又是`window`的属性，而`let`定义的只是全局变量

详见文章《顶层对象和全局变量》

### 5、箭头函数

> 箭头函数的this会继承外层函数调用的this绑定（无则继续往外层找this直到window），且无法更改

``` javascript
var name = 'window'
var fn = function() {
    return (()=>{
        console.log(this.name)
    })
}
var obj1 = {
    name: 'obj1',
    f: fn
}

fn()() // window
obj1.f()() // obj1, obj1.f()即箭头函数：()=>{console.log(this.name)
obj1.f().call(window) // obj1, 无法更改箭头函数的this指向
```

### 6、setTimeout()和setInterval()中的this

window.setTimeout()和window.setInterval()的函数中的this有些特殊，里面的this默认是window对象

由`setTimeout()`调用的代码运行在与所在函数完全分离的执行环境上。这会导致，这些代码中包含的 `this` 关键字在非严格模式会指向 `window` (或全局)对象，严格模式下为 undefined，这和所期望的`this`的值是不一样的

```javascript
var name = 'window'
setTimeout(function(){console.log(this.name)}, 300)
```

可参考[MDN中setTimeout](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout)

## 三、总结

this是在函数内的，绑定的是对象

按以下1、2、3、4优先级判断，`new`优先级最高

1、由`new`调用，绑定到新创建的对象；

2、由`call`、`apply`、`bind`调用，绑定到指定对象；

3、由上下文对象调用，绑定到该上下文对象；

4、默认，**函数体**（非调用位置）处于非严格模式绑定到`window`，处于严格模式绑定到`undefined`

5、**箭头函数**的this会继承**外层函数调用的this绑定**，且无法更改

## 四、练习

### 习题1

``` javascript
var num = 1;
var myObject = {
    num: 2,
    add: function() {
        this.num = 3;
        (function() {
            console.log(this.num); 
            this.num = 4; 
        })();
        console.log(this.num); 
    },
    sub: function() {
        console.log(this.num) 
    }
}
myObject.add(); 
console.log(myObject.num);
console.log(num); 
var sub = myObject.sub;
sub();
```

**答案与解析：**





```javascript
myObject.add() //1 3  
// 原因
(function() {
    console.log(this.num); 
    this.num = 4; 
})()
// 为一个立即执行函数，根据我们的四个绑定规则，无new、无call、apply、bind、无上下文对象，所以是默认绑定，指向window
// 所以打印出1，并且把window.num改成4
// 3 则是由于add()方法由myObject调用，隐式绑定，this指向myObject，this.num = 3将该对象的num改成了3
console.log(myObject.num) // 3
console.log(num) // 4

var sub = myObject.sub;
sub()4 // 丢失了myObject隐式绑定，myObject.sub将sub方法的地址赋值给了变量sub，sub()是在全局环境执行的，为默认绑定
```



### 习题2

```javascript
var name = 'window'

function Person (name) {
  this.name = name;
  this.show1 = function () {
    console.log(this.name)
  }
  this.show2 = () => console.log(this.name)
  this.show3 = function () {
    return function () {
      console.log(this.name)
    }
  }
  this.show4 = function () {
    return () => console.log(this.name)
  }
}

var personA = new Person('personA')
var personB = new Person('personB')


personA.show1()
personA.show1.call(personB) 

personA.show2() 
personA.show2.call(personB) 

personA.show3()() 
personA.show3().call(personB) 
personA.show3.call(personB)()

personA.show4()() 
personA.show4().call(personB) 
personA.show4.call(personB)() 
```

**答案与解析：**





```javascript
personA.show1()  // personA，new绑定，this绑定到了personA对象上
personA.show1.call(personB) // personB，显式绑定，this绑定到了personB

personA.show2() // personA，箭头函数的this继承了Person()函数的this指向，即personA
personA.show2.call(personB) // personA，箭头函数的this指向无法改变

personA.show3()() // window，隐式绑定丢失，personA.show3() === function() {console.log(this.name)}，该函数在全局环境执行，默认绑定，指向window
personA.show3().call(personB) // personB，隐式绑定丢失后，通过显式绑定到了personB 
personA.show3.call(personB)() // window，将show3方法显式绑定到personB，但是还是绑定丢失了

personA.show4()() // personA，箭头函数this继承外层函数的this绑定，即指向personA
personA.show4().call(personB) // personA，箭头函数的this无法改变
personA.show4.call(personB)() // personB，箭头函数的外层函数this指向了personB
```



### 习题3

```javascript
var name = 'window'
var obj1 = {
  name: 'obj1',
  foo1: function () {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  },
  foo2: () => {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
}
var obj2 = {
  name: 'obj2'
}

obj1.foo1.call(obj2)()
obj1.foo1().call(obj2)
obj1.foo2.call(obj2)()
obj1.foo2().call(obj2)
```

**答案与解析：**





```javascript
obj1.foo1.call(obj2)() // obj2 obj2，显式绑定到obj2；箭头函数继承外层函数的this绑定
obj1.foo1().call(obj2) // obj1 obj1，隐式绑定，obj1调用foo1方法；箭头函数的this无法改变
obj1.foo2.call(obj2)() // window window，箭头函数this无法改变，它的外层函数作用域为window；绑定丢失，变成默认绑定，指向window
obj1.foo2().call(obj2) // window obj2，箭头函数this无法改变，它的外层函数作用域为window；将返回的函数显式绑定到obj2
```



### 习题4

``` javascript
var name = 'window'
function Person (name) {
  this.name = name
  this.foo1 = function () {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
  this.foo2 = function () {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
  this.foo3 = () => {
    console.log(this.name)
    return function () {
      console.log(this.name)
    }
  }
  this.foo4 = () => {
    console.log(this.name)
    return () => {
      console.log(this.name)
    }
  }
}
var person1 = new Person('person1')

  person1.foo1()()
  person1.foo2()()
  person1.foo3()()
  person1.foo4()()
```

**答案与解析：**





 ```javascript
  person1.foo1()() // person1 window // new绑定，this指向person1;默认绑定，没有new、显式和隐式绑定，指向window
  person1.foo2()() // person1 person1 // new绑定；箭头函数继承foo2的this
  person1.foo3()() // person1 window // 箭头函数继承Person的this；返回的函数为默认绑定，指向window
  person1.foo4()() // person1 person1 // 箭头函数继承Person的this；返回的箭头函数也是
 ```



