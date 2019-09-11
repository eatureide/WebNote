## 原始类型有哪些？

boolean 
null 
undefined 
number 
symbol 
string

原始类型不能调用方法，但如果这个原型类型本身就是string的话例外：
如'1'.toString(),这时候'1'就已经被强制转换成了string类型，也就是对象类型，所以可以调用toString函数，string类型是不可改变的，无论你在sting类型上调用任何方法，都不会对值有改变

## 对象类型和原始类型的不同之处？

原始类型存储的是值，对象类型存储的是地址（指针）
当创建一个对象类型的时候，计算机会在内存中帮我们开辟一个空间来存放值，但需要找到这个空间，这个空间就拥有一个地址（指针）

## typeof是否能正确判断类型？instanceof能正确判断对象的原理是什么？

typeof除了原始类型来说，除了null都可以显示正确的类型，所以typeof并不能准确判断变量到底是什么类型

typeof可以正确判断除了null以外的原始类型，因为bug的缘故，null变成了object

typeof [] // 'object'
typeof {} // 'object'
typeof console.log // 'function'

instanceof的内部机制是通过原型链判断的

## 对象转原始类型

对象在类型转换的时候，会调用内置的[[ToPrimitive]]函数，对于该函数来说，转换逻辑一般来说如下：

如果已经是原始类型了，就无需再转换
如果需要转字符串类型就调用x.toString(),转换为基础类型的话就返回转换的值。不是字符串类型的话就先调用valueOf，结果不是基础类型的话再调用toString
调用x.valueOf(),如果转换为基础类型，就返回转换的值
如果都没有返回原始类型，就会报错

## 四则运算符
加法运算符不同于其它几个运算符，它有以下几个特点：
运算中其中一方为字符串，那么他就会把另一方叶转换为字符串
如果一方不是字符串或者数字，那么会将他转换为数字或字符串

## 比较运算符
如果是对象，就通过toPrimitive转换对象
如果是字符串，就通过unicode字符索引比较

let a = {
  valueOf() {
    return 0
  },
  toString() {
    return '1'
  }
}

a > -1

以上代码中，因为a是对象，所以会通过valueOf转换为原始类型再比较

==和===有什么区别？
对于==来说，如果对比双方的类型不一样的话，就会进行类型转换

假设我们需要对比X和Y是否相同，就会进行如下判断流程：
1、首先会判断两者类型是否相同，相同的话就是比大小了
2、类型不相同的话，就会进行类型转换
3、会先判断是否存在对比null和undefined，是的话返回true
4、判断两者类型是否为string和number，是的话就会将字符串转为number

  1 == '1'
  1 == 1
5、判断其中一方是否为boolean，是的话就会把boolean转为number再进行判断

  '1' == true
  '1' == 1
    1 == 1

6、判断其中一方是否为object且另一方为string、number或者symbol，是的话就会把object转换为原始类型再进行判断

  '1' == { name: 'aaa' }
  '1' == '[object, object]'


## 什么是闭包？
闭包的定义其实很简单： 函数A内部有一个函数B，函数B可以访问到A中的变量，那么函数B就是闭包

在JS中，闭包的意义就是让我们可以间接访问函数内部的变量

循环中使用闭包解决var定义函数的问题
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}

首先因为 setTimeout 是个异步函数，所以会先把循环全部执行完毕，这时候 i 就是 6 了，所以会输出一堆 6。

解决办法有三种，第一种是使用闭包的方式
for (var i = 1; i <= 5; i++) {
  ;(function(j) {
    setTimeout(function timer() {
      console.log(j)
    }, j * 1000)
  })(i)
}

在上述代码中，我们首先使用了立即执行函数将 i 传入函数内部，这个时候值就被固定在了参数 j 上面不会改变，当下次执行 timer 这个闭包的时候，就可以使用外部函数的变量 j，从而达到目的。

第二种就是使用 setTimeout 的第三个参数，这个参数会被当成 timer 函数的参数传入。

for (var i = 1; i <= 5; i++) {
  setTimeout(
    function timer(j) {
      console.log(j)
    },
    i * 1000,
    i
  )
}

第三种就是使用 let 定义 i 了来解决问题了，这个也是最为推荐的方式
for (let i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}

## 浅拷贝

首先可以通过Object.assign来解决这个问题，很多人认为这个函数是用来深拷贝的，
其实不是，Object.assign只会拷贝所有的属性的值到新对象中，如果属性值是对象的话，拷贝的是地址，所以并不是深拷贝

let a = {
  age: 1
}
let b = Object.assign({}, a)
a.age = 2
console.log(b.age) // 1

另外我们还可以通过展开运算符 ... 来实现浅拷贝

let a = {
  age: 1
}
let b = { ...a }
a.age = 2
console.log(b.age) // 1

通常浅拷贝就能解决大部分问题了，但是当我们遇到如下情况就可能需要使用到深拷贝了
let a = {
  age: 1,
  jobs: {
    first: 'FE'
  }
}
let b = { ...a }
a.jobs.first = 'native'
console.log(b.jobs.first) // native

浅拷贝只解决了第一层的问题，如果接下去的值中还有对象的话，那么就又回到最开始的话题了，两者享有相同的地址。要解决这个问题，我们就得使用深拷贝了。

## 深拷贝

这个问题通常可以通过 JSON.parse(JSON.stringify(object)) 来解决。

let a = {
  age: 1,
  jobs: {
    first: 'FE'
  }
}
let b = JSON.parse(JSON.stringify(a))
a.jobs.first = 'native'
console.log(b.jobs.first) // FE

但是该方法也是有局限性的：

会忽略 undefined
会忽略 symbol
不能序列化函数
不能解决循环引用的对象 

## 原型
比如输出一个arr，点开他，它的__proto__指向了Array.prototype


## 原型链
Object是所有对象的爸爸，所有的对象都可以通过__proto__找到它
Function是所有函数的爸爸，所有函数都可以通过__proto__找到他

函数的prototype是一个对象
对象的__proto__属性指向原型，__proto__将对象和原型连接起来组成了原型链

## 原型如何实现继承

组合继承是最常用的继承方式

function Parent(value) {
  this.val = value
}

Parent.prototype.getValue = function () {
  console.log(this.val)
}

function Child(value) {
  Parent.call(this, value)
}

Child.prototype = new Parent()

var child = new Child(1)
child.getValue()

以上继承方式核心是再于子类的构造函数通过Parent.call(this)继承父类的属性，然后改变字类的原型为 new Parent()来继承父类的函数

## 寄生组合继承

这种继承方式对组合继承进行了优化，组合继承的缺点再于继承父类函数时调用了构造函数，只需要优化掉这点就行了

function Parent(value) {
  this.val = value
}
Parent.prototype.getValue = function() {
  console.log(this.val)
}

function Child(value) {
  Parent.call(this, value)
}
Child.prototype = Object.create(Parent.prototype, {
  constructor: {
    value: Child
  }
})

const child = new Child(1)

child.getValue() // 1
child instanceof Parent // true

以上继承实现的核心就是将父类的原型赋值给了字类，并将构造函数设置为字类，这样及解决了无用的父类属性问题，还能正确的找到字类的构造函数

## Class继承

class Parent {
  constructor(value) {
    this.val = value
  }
  getValue() {
    console.log(this.val)
  }
}

class Child extends Parent {
  constructor(value){
    super(value)
  }
}

const child = new Child(1)
child.getValue()

child instanceof Parent // true

class 实现继承的核心在于使用 extends 表明继承自哪个父类，并且在子类构造函数中必须调用 super，因为这段代码可以看成 Parent.call(this, value)。

 JS 中并不存在类，class 的本质就是函数。


## 模块化
为什么要使用模块化？

模块化可以给我带来以下好处
1、解决命名冲突
2、提高复用性
3、提高代码可维护性

立即执行函数

在早期，使用立即执行函数实现模块化是常见的手段，通过函数作用域解决了命名冲突、污染全局作用域的问题

(function() {
  var obj = {}
  console.log(obj)
})()

// console.log(obj)

## proxy可以实现什么功能
proxy是es6新增的功能，它可以用来自定义对象中的操作

let onWatch = (obj, setBind, getLogger) => {
  let handler = {
    get(target, property, receiver) {
      getLogger(target, property)
      return Reflect.get(target, property, receiver)
    },
    set(target, property, value, receiver) {
      setBind(value, property)
      return Reflect.set(target, property, value)
    }
  }
  return new Proxy(obj, handler)
}

let obj = { a: 1 }
let p = onWatch(
  obj,
  (v, property) => {
    console.log(`监听到属性${property}改变为${v}`)
  },
  (target, property) => {
    console.log(`'${property}' = ${target[property]}`)
  }
)
p.a = 2 // 监听到属性a改变
p.a // 'a' = 2


# map，filter，reduce各自有什么作用？
map作用是生成一个新数组，遍历原数组，讲每个元素拿出来做一些改变后放到新数组中,并且不影响原数组

map回调函数接受三个参数，分别是索引元素，索引，原数组

filter的作用也是生成一个新数组，在遍历原数组的时候返回值为true的元素放入新数组，可以利用这个函数删除一些不需要的元素

let array = [1,2,3,4,6]
let newArray = array.filter((item) => item !== 6)
console.log(newArray)

和map一样，filter的回调函数接受三个参数，用处也相同


## reduce

const arr = [1, 2, 3]
const sum = arr.reduce((acc, current) => acc + current, 0)
console.log(sum)

首先初始值为 0，该值会在执行第一次回调函数时作为第一个参数传入
回调函数接受四个参数，分别为累计值、当前元素、当前索引、原数组，后三者想必大家都可以明白作用，这里着重分析第一个参数
在一次执行回调函数时，当前值和初始值相加得出结果 1，该结果会在第二次执行回调函数时当做第一个参数传入
所以在第二次执行回调函数时，相加的值就分别是 1 和 2，以此类推，循环结束后得到结果 6

如果传2，等于8，这样就好理解了

## 并发于并行的区别？
并发是宏观概念，我分别有任务A和任务B，在一段时间内通过切换完成了这两个任务，这种情况就可以称之为并发

并行是微观概念，假设CPU中存在两个核心，那我我就可以同时完成A、B。同时完成多个任务的情况就可以称之为并行

## 什么是回调函数？

ajax(url,() => {}) // 回调函数的例子

回调函数有一个致命的弱点，就是容易写出callback hell，假设多个请求存在依赖性，你可能会写出以下代码：

ajax(url, () => {
    // 处理逻辑
    ajax(url1, () => {
        // 处理逻辑
        ajax(url2, () => {
            // 处理逻辑
        })
    })
})

以上代码看起来不利于阅读和维护，当然，你可能会想说解决这个问题还不简单，把函数分开来写不就得了

function firstAjax() {
  ajax(url1, () => {
    // 处理逻辑
    secondAjax()
  })
}
function secondAjax() {
  ajax(url2, () => {
    // 处理逻辑
  })
}
ajax(url, () => {
  // 处理逻辑
  firstAjax()
})

以上的代码虽然看上去利于阅读了，但是还是没有解决根本问题。

回调地狱的根本问题就是：

嵌套函数存在耦合性，一旦有所改动，就会牵一发而动全身
嵌套函数一多，就很难处理错误


Generator 算是 ES6 中难理解的概念之一了，Generator 最大的特点就是可以控制函数的执行。在这一小节中我们不会去讲什么是 Generator，而是把重点放在 Generator 的一些容易困惑的地方。

function *foo(x) {
  let y = 2 * (yield (x + 1))
  let z = yield (y / 3)
  return (x + y + z)
}
let it = foo(5)
console.log(it.next())   // => {value: 6, done: false}
console.log(it.next(12)) // => {value: 8, done: false}
console.log(it.next(13)) // => {value: 42, done: true}

你也许会疑惑为什么会产生与你预想不同的值，接下来就让我为你逐行代码分析原因

首先 Generator 函数调用和普通函数不同，它会返回一个迭代器
当执行第一次 next 时，传参会被忽略，并且函数暂停在 yield (x + 1) 处，所以返回 5 + 1 = 6
当执行第二次 next 时，传入的参数等于上一个 yield 的返回值，如果你不传参，yield 永远返回 undefined。此时 let y = 2 * 12，所以第二个 yield 等于 2 * 12 / 3 = 8
当执行第三次 next 时，传入的参数会传递给 z，所以 z = 13, x = 5, y = 24，相加等于 42

Generator 函数一般见到的不多，其实也于他有点绕有关系，并且一般会配合 co 库去使用。当然，我们可以通过 Generator 函数解决回调地狱的问题，可以把之前的回调地狱例子改写为如下代码：

function *fetch() {
    yield ajax(url, () => {})
    yield ajax(url1, () => {})
    yield ajax(url2, () => {})
}
let it = fetch()
let result1 = it.next()
let result2 = it.next()
let result3 = it.next()

# promise的特点是什么？
promise翻译过来就是承诺的意思，这个承诺在未来有一个确切的答复，并且该承诺有三种状态，分别是

1、等待中（pending）
2、完成了（resolved）
3、拒绝了（reject）

这个承诺一旦从等待便成为其它状态就永远不能更改状态了，也就是说一旦状态改变为resolved后，就不能再次改变

new Promise((resolve, reject) => {
  resolve('success')
  //无效
  reject('reject')
}).then((e) => {
  console.log(e)
})

在构造promise的时候，构造函数内部的代码是立即执行的
new Promise((resolve, reject) => {
  console.log('new promise')
  resolve('success)
})
console.log('finish')

// new Promise -> finifsh

promise实现了链式调用，也就是说每次调用then之后返回的都是一个promise，并且是一个全新的promise，原因也是因为状态不可改变。如果你在then中使用了return，那么return的值会被promise.resolve()包装

Promise.resolve(1)
  .then(res => {
    console.log(res) // => 1
    return 2 // 包装成 Promise.resolve(2)
  })
  .then(res => {
    console.log(res) // => 2
  })

  promise也很好地解决了回调地狱地问题，可以把之前地回调地狱列子改写问如下代码：
  ajax(url)
    .then((res) => {
      console.log(res)
      return ajax(url1)
    }).then((res) => {
      console.log(res)
      return ajax(url2)
    }).then((res) => console.log(res))

## promise的缺点
无法取消promise，错误需要通过回调函数捕获


## async及await的特点，他们的优缺点分别是什么，await原理是什么？
一个函数如果加上async，那么该函数就会返回一个promise

async function test() {
  return '1'
}
console.log(test())

async就是讲函数返回值用promise.resolve()包裹了下，和then中返回处理值一样，并且await只能配套async使用

async function test() {
  let value = await sleep()
}

async和await可以说是异步终极解决方案了，相比直接使用promise来说，优势再于处理then的调用链，能够更清晰准确的写出代码，毕竟写一大堆then也很恶心，并且也能优雅地解决回调地狱问题。
当然也存在一些缺点，因为await将异步代码改造成了同步代码，如果多个异步代码没有依赖性却使用了await会造成性能上的降低

async function test() {
  // 以下代码没有依赖性的话，完全可以使用 Promise.all 的方式
  // 如果有依赖性的话，其实就是解决回调地狱的例子了
  await fetch(url)
  await fetch(url1)
  await fetch(url2)
}

下面来看一个使用await的例子
首先函数b先执行，在执行到await 10之前变量还是0，因为await内部实现了generator，generator会保留栈中的东西，所以这时候a=0被保存了下来
因为await是异步操作，后来的表达式不返回promise的话，就会被包装成promise.resolve(返回值),然后回去执行函数外的同步代码
同步代码执行完后开始执行异步代码，将保存下来的值拿出来用，这时候a=0+10

其实await就是generator加上promise的语法糖，且内部实现了自动执行generator

## setInterval、setTimeout、requestAnimationFrame各有什么特点

很多人认为setTimeout是延时多久，那就应该是多久后执行
其实这个观点是错误的，因为js是单线程执行的，如果前面的代码影响了性能，就会导致setTimeout不会按时执行

setInterval的作用和setTimeout基本一致，只是该函数是每隔一段时间执行一次回调函数

通常来说不建议使用setInterval，第一，它和setTime一样，不能保证在预期的时间执行任务，第二，它存在执行积累的问题
function demo() {
  setInterval(function(){
    console.log(2)
  },1000)
  sleep(2000)
}
demo()

以上代码在浏览器环境中，如果定时器执行过程中出现了耗时操作，多个回调函数会在耗时操作结束以后同时执行，这样可能就会带来性能上的问题。

requestAnimationFrame 自带函数节流功能，基本可以保证在 16.6 毫秒内只执行一次（不掉帧的情况下），并且该函数的延时效果是精确的，没有其他定时器时间不准的问题，当然你也可以通过该函数来实现 setTimeout

