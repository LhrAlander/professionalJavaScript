# JavaScript 中的 this
> 我终于可以写这章东西了。拖这么久的原因是因为我在写业务代码，加上毕设的代码结构设计搞得我有点懵逼，加上之前确实有点懈怠，然后去写了个简易版的 MVVM 双向绑定和指令渲染的东西，经过那个小项目我发现我对 **内存**、**变量**、**闭包**、**this** 有了更近一步的理解，然后加上看了点书有了点自己的理解，于是今天记录一下。

> 我所写的这些东西只是我自己的理解，不代表就是绝对正确的，是我对 JavaScript 的看法和理解。

> 我要讲的很杂很乱，因为这节本来就很庞大，涉及到了好多东西，我准备从：**变量与内存**、**闭包与 this**、**绑定 this** 这几个方面讲以下我自己的看法。
## 变量、内存
> 这个其实很简单，我们在代码之中写下了一堆又一堆的变量：
````
let foo = 'test'
const fn = function () {
  let name = 'Nicholas'
  console.log(name)
}
let handler = {
  addHandler: function (name, handler) {},
  fire: function () {}
}
````
> 以上我们定义了： *普通变量*、*函数*、*对象变量*。这三种大概就是我们平时打代码过程中定义的最多的东西了。首先要确定的是，基本数据类型是直接引用，而对象（引用变量）则被存储在了内存中，我们无法直接访问到内存，只能取得对象的引用。
> 这句话意思是：变量或者说对象它就安静地在内存里（如果没被垃圾收集机制清除），而我们定义地变量名只是对其引用。于是：
````
let fns = {
  sayHi: function () {
    console.log('Hi')
  },
  fight: function () {
    console.log(`I'll fight with you`)
  }
}
let ffight = fns.fight
````
> 上述代码中，**我自己认为**：首先肯定是有两个函数在内存中躺着，然后 ``fns.sayHi`` 指向了其中一个函数，``fns.fight``指向了另一个函数，而 ``ffight`` 指向的函数和 ``fns.sayHi`` 指向的是同一个函数，它们都只是对内存中存在的对象进行了引用
> 所以，**我认为**：内存和变量实际上较为独立又紧密联系：变量本身存储在这里，它只是指向了内存中的某一个对象而已，并无它用。

## 闭包与this
> 在我**自认为理清楚**了变量与内存的关系之后，我似乎对**闭包与this**也有了一点认识。首先上一节闭包中我其实对闭包是什么已经有了认知，但是那时候对this这个东西有点模糊，只是依稀知道 **在理解this时不可用闭包的词法作用域来认知**。
> 首先看一段代码：
````
let fn = function () {
  let name = 'Nicholas'
  return function () {
    console.log(name)
  }
}

function getTimeout () {
  setTimeout(fn(), 1000)
}

getTimeOut() // 1秒后输出 Nicholas
````
> 上面这段代码的结果应该十分简单，因为 fn() 得到的函数保留了对 fn 作用域中的 name 的引用，所以依然可以访问到 name。
> **我自己的理解**：最开始我认为 this 就是对当前的引用，至于当前是什么，我相信很多人都和我一开始一样认为 **所谓当前就是这行代码执行时它能访问到的变量环境**，于是就有了下面这段代码：
````
var name = 'Bob'

function getTimeout () {
  setTimeout(function () {
    console.log(this.name)
  }, 1000)
}

var obj = {
  name: 'Nicholas',
  getTimeout: getTimeout
}

obj.getTimeout() // 1秒后输出 Bob
````
> 这里我觉得应该先上结论：this指向调用它的执行环境，绝不能用词法作用域的方法去找this的指向

> 如果照着我们最开始的理解，当执行这个函数的时候要去访问 ``name``，我们自然而然会顺着代码书写位置去看，我们在 ``obj`` 函数中找到了``name``，于是我们以为会this就是obj，应该输出 Nicholas。
> 但是这里我们在看看上面这段代码之中的变量和内存：首先我们定义了一个函数 ``getTimeout``，它在内存中，在 ``setTimeout`` 的参数中我们其实又定义了一个匿名函数，它也在内存中，而且**obj.getTimeout**就是那个匿名函数。然后我们调用了``getTimeout``，
> 这个函数中我们使用定时器去调用那个匿名函数，于是1秒后，相当于执行了这个函数，这个函数就是普普通通的执行了：
````
// 像这样执行了：
// 这里我们先取得这个匿名函数的引用，虽然我们是重新定义了一个新的函数，为了演示作用，这里我们假设 noName 就是那个匿名函数
function noName () {
  console.log(this.name)
}
// 1秒后我们调用这个匿名函数
noName()
````
> 这样看，结果就比较明显了，这里我们自然可以去看 name 就是全局的 Bob。
> 而我们最常见的做法是缓存this：
````
var name = 'Bob'

function getTimeout () {
  var that = this
  setTimeout(function () {
    console.log(that.name)
  }, 1000)
}

var obj = {
  name: 'Nicholas',
  getTimeout: getTimeout
}

obj.getTimeout() // 1秒后输出 Nicholas
````
> 我们平时就是用这种绑定的方式得到了我们想要的结果，我们理一下思路：

> 我们知道this的绑定是动态的即它在执行前才能确定指向，就是因为多个变量可以指向同一个变量才有这种说法，``obj.getTimeout()`` 这行代码执行了内存中的代码，但是执行这串代码的环境确实 ``obj``，于是这时候 ``getTimeout`` 这个函数中的 ``this`` 自然就是 ``obj``

> 紧接着我们缓存了 ``this`` 到 ``that`` 变量中，然后1秒之后我们调用了匿名函数，该函数是个闭包，它拥有了 ``getTimeout`` 函数的变量对象的访问权限，自然就可以访问到 ``that`` 变量，于是这个 ``that`` 就指向了 ``obj``,``obj.name``自然就是 Nicholas了。

> 于是我们得到了那个结论：函数中的this，其实是在运行时决定的，虽然我们运行的是同一段代码，但是调用这段代码的方式是不一样的，就为这段代码中的this绑定了不同的值。

## 绑定this
> 我们需要绑定this，才能得到我们想要的结果。
> - 隐式绑定：``obj.fn()``，这个时候我们通过 ``obj`` 调用 ``fn``，其中this自然被 ``obj`` 绑定了
> - 硬绑定：``fn.call(obj, args)``或者``fn.apply(obj, args)``，这样我们其实相当于传入一个对象使它作为 ``fn`` 函数运行时的this。
> - bind函数：``bind`` 是每个 ``Function`` 对象都有的方法，其返回结果是一个函数，使用方法：``let foo = bar.bind(obj)``，这个时候只是将this的指向绑定成obj。
> - new操作符：``let bar = new Fn()``，我们在第六章讲过 ``new``发生过什么，所以这边的this指向就是返回的对象。

> 既然有多种方式，自然分出优先级：
> 1. new
> 2. bind
> 3. 硬绑定
> 4. 隐式绑定

> 证明：
````
// 我们定义一个函数
var name = 'Bob'
var obj1 = {
  name: 'Nicholas',
  test: test
}
var obj2 = {
  name: 'Peter',
}
var obj3 = {
  name: 'Shally'
}
function test () {
  console.log(this.name)
}
// 隐式绑定和硬绑定：
obj1.test() // Nicholas
obj1.test.call(obj2) // Peter
// bind和硬绑定
let fn = test.bind(obj3)
fn() // Shally
fn.call(obj2) // Shally
// new和bind 我觉得这两个其实没什么比较，因为new出来是个对象，这个对象就是this，具体理解还是看代码吧
function Person (name) {
  this.name = name
}
// 首先我们试图创建另一个构造函数，让这个构造函数的this通过bind绑定一个对象
// 因此我们需要一个空对象
var testObj = {}
// 接着我们创建另一个构造函数
var AnotherFn = Person.bind(testObj)
// 紧接着我们调用这个AnotherFn
AnotherFn('Nicholas')
console.log(testObj.name) // Nicholas
// 接着我们通过另一个构造函数 new 一个对象出来
var person = new AnotherFn('Bob')
// 这个结果其实说明，new 这个操作符其实饼不是修改一个对象，而是先创建这个对象，然后this就是这个对象，然后返回这个对象
// 如果bind优先级更高，则这时候 testObj.name 应该是 Bob，而实际情况像我们说明并非如此。
console.log(testObj.name) // Nicholas
console.log(person.name) // Bob
````
> 以上就是几种绑定this的情况。

## 结语
> 写这一节真的是要死要活。因为其实到写之前我都不是很清楚this的语法，只是模棱两可，现在有些理解，就赶紧记录一下。但是我觉得我现在的理解应该也是瑕疵挺多的吧。一步一步来，慢慢学习。

> 应该会挑个时间，写一下前两天写的 MVVM 的那个思路，感觉还是有收获的，虽然别人觉得很简单，但是自己写起来还是印象深刻些。 
