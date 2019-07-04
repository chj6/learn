## JS基本类型
基本数据类型(primitive values)（按值进行访问）：`Undefined`、`Null`、`Boolean`、`Number`、`String`、`Symbol`(new in ES6，表示独一无二的值)    
引用类型(reference values)(按引用进行访问)：`Object`、`Array`、`Function`、`Date`、`RegExp`等     


## JS类型检测
- **typeof：操作符返回一个字符串，表示未经计算的操作数的类型**
```javascript
var a;        typeof a; // undefined
a = null;     typeof a; // object
a = true;     typeof a; // boolean
a = 555;      typeof a; // number
a = "sam";    typeof a; // string
a = Symbol(); typeof a; // symbol
a = function(){};      typeof a; // function
a = []或{}或/aaa/g;    typeof a; // object
```
```javascript
typeof null === 'object' // 从一开始出现Javascript就是这样的
```
在JavaScript最初的实现中，JavaScript中的值是由一个表示类型的标签和实际数据值表示的。对象的类型标签是0。由于`null`代表的是空指针（大多数平台下值为0x00）,因此null的类型标签也成为了0，`typeof null`就错误的返回了`'object'`。参考地址：http://2ality.com/2013/10/typeof-null.html
```javascript
// source code for : typeof
JS_PUBLIC_API(JSType)
JS_TypeOfValue(JSContext *cx, jsval v)
{
    JSType type = JSTYPE_VOID;
    JSObject *obj;
    JSObjectOps *ops;
    JSClass *clasp;

    CHECK_REQUEST(cx);
    if (JSVAL_IS_VOID(v)) {  // (1)
        type = JSTYPE_VOID;
    } else if (JSVAL_IS_OBJECT(v)) {  // (2)
        obj = JSVAL_TO_OBJECT(v);
        if (obj &&
            (ops = obj->map->ops,
             ops == &js_ObjectOps
             ? (clasp = OBJ_GET_CLASS(cx, obj),
                clasp->call || clasp == &js_FunctionClass) // (3,4)
             : ops->call != 0)) {  // (3)
            type = JSTYPE_FUNCTION;
        } else {
            type = JSTYPE_OBJECT;
        }
    } else if (JSVAL_IS_NUMBER(v)) {
        type = JSTYPE_NUMBER;
    } else if (JSVAL_IS_STRING(v)) {
        type = JSTYPE_STRING;
    } else if (JSVAL_IS_BOOLEAN(v)) {
        type = JSTYPE_BOOLEAN;
    }
    return type;
}
```   
- 在步骤(1)先检查值v是不是`undefined(VOID)`，代码：`#define JS_VAL_IS_VOID(v) ( (v) == JSVAL_VOID )`    
- 后续在步骤(2)判断v是否有一个对象标签。如果v是可调用的(3)，或者它的内部属性[[class]]标记为一个function(4)，那么v是一个function。否则是一个object。这里就是typeof null的造成原因。
- 后面的检查是number、string、boolean。这里并没有一个明确检查null的，which could be performed by the following C macro.  `#define JSVAL_IS_NULL(v)  ((v) == JSVAL_NULL)
`

- **instanceof：用于测试构造函数的prototype属性是否出现在对象的原型链中的任何位置**
```javascript
({}) instanceof Object              // true
([]) instanceof Array               // true
(/aa/g) instanceof RegExp           // true
(function(){}) instanceof Function  // true

function Car(year){ this.year = year; }
var auto = new Car(2018);
console.log(auto instanceof Car);    // true
console.log(auto instanceof Object); // true
```
注意点：`auto instanceof Car`返回`true`，并不意味这改表达式会永远返回true，因为`Car.prototype`属性的值有可能会改变，改变之后的值很有可能不存在于`obj`的原型链上，这是auto instanceof Car就是`false`。另外一种情况下，原表达式的值也会改变，就是改变对象auto的原型链的情况，虽然在目前的ES规范中，我们只能读取对象的原型而不能改变它，但借助于非标准的__proto__伪属性，是可以实现的。比如执行`auto.__proto__ = {}`之后，`auto instanceof Car`就会返回false了。

- **constructor：返回创建实例对象的Object构造函数的引用，所有对象都会从它的原型上继承一个constructor属性，对原始类型来说，如1，true和"test"，该值只可读。**
```javascript
var o = {};
o.constructor === Object; // true
o.constructor = 2; // 修改构造函数
o.constructor === Object; // false
o.constructor === 2; // true，所以用constructor判断对象类型不一定准确
var o = new Object;
o.constructor === Object; // true
var a = [];
a.constructor === Array; // true
var a = new Array;
a.constructor === Array; // true

function Tree(name) {
   this.name = name;
}

var theTree = new Tree("Redwood");
theTree.constructor === Tree; // true
theTree.constructor === Object; // false
```
总结：手动设置或更新构造函数可能会导致不同且有时令人困惑的后果。为了防止它，只需在每个特定情况下定义构造函数的角色。在大多数情况下，不使用构造函数，并且不需要重新分配构造函数。

- **Object.prototype.toString：返回一个表示该字符串的对象。用于验证时，与constructor一样，不一定准确**
```javascript
function Tree(name){
    this.name = name;
}
var theTree = new Tree("Redwood");
theTree.toString(); // [object Object]

// 修改toString定义
Tree.prototype.toString = function(){
    return "Tree"
}
theTree.toString(); // Tree
```


## 函数定义
构造函数、函数声明、函数表达式、块级函数
函数声明：
块级函数：从ECMAScript 6开始，在严格模式下，块里的函数作用域为这个块。ECMAScript 6之前不建议块级函数在严格模式下使用。非严格模式下来说**不要用**块级函数。
```javascript
// 一个用Function构造函数定义的函数，被赋值给变量multiply
var multiply = new Function('x', 'y', 'return x*y ');

// 一个名为multiply函数的声明
function multiply(x, y){ return x*y; }

// 一个匿名函数表达式
var multiply = function(x, y){ return x*y; }
// 一个名为func_named函数表达式，被赋值给变量multiply，注意外部无法访问到func_named
var multiply = function func_named(x,y){ return x*y; }

// 块级函数
'use strict'; // 注意严格模式，必须是小写use strict的字符串
function f() { return 1; }

{  
  function f() { return 2; }
}

f() === 1; // true
// f() === 2 in non-strict mode（非严格模式下）
```
函数声明和函数表达式，主要区别是：函数声明在代码执行以前被加载到作用域，而函数表达式在代码执行到那一行的时候才会有定义；     

函数声明：可以在函数定义前调用；`test();function test(){ console.log("test info"); }`，正常输出：test info，如果将`test()`替换成`test`，则会输出：f test(){ console.log("test info") }；    

函数表达式：无法在函数定义去调用；`test(); var test = function(){ console.log("test info"); }`，报错：**test is not function**，如果将`test()`替换成`test`，则会输出：undefined

函数表达式定义的函数继承了当前的作用域。换言之，函数构成了闭包。另一方面，Function构造函数定义的函数不继承任何全局作用域以外的作用域(那些所有函数都继承的)。    

通过函数表达式定义的函数和通过函数声明定义的函数只会被解析一次，而Function构造函数定义的函数却不同。也就是说，每次构造函数被调用，传递给Function构造函数的函数体字符串都要被解析一次 。虽然函数表达式每次都创建了一个闭包，但函数体不会被重复解析，因此函数表达式仍然要快于"new Function(...)"。 所以Function构造函数应尽可能地避免使用。


## JS如何正确遍历对象属性（主要根据是否可枚举、是否包括原型链上的属性进行选择用哪个api）
**可枚举属性是指那些内部`可枚举`标志设置为true的属性，对于通过直接的赋值和属性初始化的属性，该标识指为true，对于通过Object.defineProperty等定义的属性，该标识值enumerable默认为false.**
```javascript
var o = {}; // 创建一个新对象

// 在对象中添加一个属性与数据描述符的示例
Object.defineProperty(o, "a", {
  value : 37, // 默认值，可以用get()、set()方法代替，但是value不能和get、set同时存在
  writable : true, // 是否可以被修改
  enumerable : true, // 可枚举属性
  configurable : true // 表示对象的属性是否可以被修改，以及除writable特性外的其他特性是否可以被修改
});
// 对象o拥有了属性a，值为37
```
`var obj = {"name":"chj","company":"ms"}`
- Object.keys(obj)：返回一个数组，包括对象自身的（不含继承的：即不包括原型链上的）所有可枚举属性（不含Symbol属性）。数组中属性排序顺序和使用for .. in ..返回的顺序一致。
```javascript
Object.keys(obj).forEach(function(key){ 
    console.log(obj[key]);
})
```
- for (var prop in obj){ }：以任意顺序遍历一个对象的（包括原型链上的）可枚举属性（不含Symbol属性）。通常，在迭代过程中最好不要在对象上进行添加、修改或者删除属性的操作，除非是对当前正在被访问的属性。这里并不保证是否一个被添加的属性在迭代过程中会被访问到，不保证一个修改后的属性（除非是正在被访问的）会在修改前或者修改后被访问，不保证一个被删除的属性将会在它被删除之前被访问。**for (var prop in obj){ }不应该用于迭代一个Array，因为迭代顺序是依赖于执行环境的，所以数组最好用证书索引去进行for循环（或者使用Array.prototype.forEach()或for (let o of array){ } 循环）**
```javascript
for(var i in obj){ console.log(obj[i]); // i是key值 }
```
- for (var prop of iterable()){  }，**es6新增语法，迭代的对象一定要是可迭代对象（包括Array、Map、Set、String、TypedArray、arguments对象等）**
```javascript
function* foo(){
  yield 1;
  yield 2;
}
var b ={"a":"aa"}
for (let o of b) {
  console.log(o);
  // expected output: 1

 break; // closes iterator, triggers return
}
```

- Object.getOwnPropertyNames(obj)：返回一个数组，包括对象自身的所有属性（不含Symbol属性，但是包括不可枚举属性）。**如果要只获取不可枚举的属性，可以通过Object.keys()获取可枚举的属性，然后在过滤掉getOwnPropertyNames获取的属性即可。**
```javascript
// 演示了getOwnPropertyNames，不会获取到原型链上的属性
function ParentClass() {}
ParentClass.prototype.inheritedMethod = function() {};

function ChildClass() {
  this.prop = 5;
  this.method = function() {};
}

ChildClass.prototype = new ParentClass;
ChildClass.prototype.prototypeMethod = function() {};

console.log(
  Object.getOwnPropertyNames(
    new ChildClass()  // ["prop", "method"]
  )
);
```
- Reflect.ownKeys(obj)：返回一个数组，包含对象自身（不包含继承属性）的所有属性，不管属性名是Symbol或字符串，也不管是否可枚举。（es6新增）它的返回值等同于`Object.getOwnPropertyNames(target).concat(Object.getOwnPropertySymbols(target))`。


## for...in和for...of的区别
这2者都是进行循环遍历。主要区别在于他们的迭代方式。    
`for...in`以原始插入顺序迭代对象的可枚举属性。    
`for...of`遍历可迭代对象定义要迭代的数据。    
以下示例显示了与Array一起使用时，for...of循环和for...in循环之间的区别。
```javascript
Object.prototype.objCustom = function() {}; 
Array.prototype.arrCustom = function() {};

let iterable = [3, 5, 7];
iterable.foo = 'hello';

// 此循环仅以原始插入顺序记录iterable 对象的可枚举属性。它不记录数组元素3, 5, 7 或hello，因为这些不是枚举属性。但是它记录了数组索引以及arrCustom和objCustom。
for (let i in iterable) {
  console.log(i); // 0, 1, 2, "foo", "arrCustom", "objCustom"
}

// 这个循环类似于第一个，但是它使用hasOwnProperty() 来检查，如果找到的枚举属性是对象自己的（不是继承的）。如果是，该属性被记录。记录的属性是0, 1, 2和foo，因为它们是自身的属性（不是继承的）。属性arrCustom和objCustom不会被记录，因为它们是继承的。
for (let i in iterable) {
  if (iterable.hasOwnProperty(i)) {
    console.log(i); // 0, 1, 2, "foo"
  }
}

// 该循环迭代并记录iterable作为可迭代对象定义的迭代值，这些是数组元素 3, 5, 7，而不是任何对象的属性。
for (let i of iterable) {
  console.log(i); // 3, 5, 7
}
```
每个对象将继承objCustom属性，并且作为Array的每个对象将继承arrCustom属性，因为将这些属性添加到`Object.prototype`和`Array.prototype`。由于继承和原型链，对象iterable继承属性objCustom和arrCustom。


## post、get、put区别？
post:
get:
put:


## websocket如何实现服务端推送的？

## websocket在连接过程中，会有新包？

## websocket和http的区别？websocket优点？

## 为什么使用websocket？websocket是如何连接的，一定要通过http协议吗？短轮询、长轮询、comment区别，各自优缺点？

## websocket消息发送失败，如何解决？如何发送图片？文字、图片不同的数据类型如何进行数据存储，如何设计，前端如何获取？

## 聊天室如何兼容ie8

## 304和200区别，常用状态码


## 回调地狱是什么，有什么问题。异常捕获怎么做？

## 同源策略？JSONP写一个代码？

## 跨域解决，跨域的安全性举例

## XSS是什么？如何防止XSS?

## 深拷贝和浅拷贝区别，手写深拷贝

## Object的几种方式，如何区分？

## 登陆状态如何使用cookie保持，最好的方法是什么？

## html5新增了哪些标签？什么语义？废除了哪些标签？

## setTimeout setInterval区别


## 如何实现一个倒计时功能，类似于秒杀
   使用setInterval，定时（每秒、每毫秒等）去执行交互方法，并且接受一个步长参数，然后到时间之后再clearInterval掉；可以在这个交互方法中去请求ajax，方便处理特殊情况下的延迟或提前秒杀活动。
   
   
## 怎么理解es6箭头函数中的this，它和一般函数的this指向有什么区别？
**箭头函数：**   
1. 函数体内的this对象，就是定义时所在的对象，而不是使用时所在的对象。
2. 不可以当作构造函数，也就是说，不可以使用new命令，否则会抛出一个错误。
3. 不可以使用arguments对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。
4. 不可以使用yield命令，因此箭头函数不能用作 Generator 函数。     

上面四点中，第一点尤其值得注意。this对象的指向是可变的，但是在箭头函数中，它是固定的；    
**一般函数**：   
this指向它的直接调用者;如果找不到直接调用者,则是window；（如果是严格模式下，直接在函数里面输出this，会是undefined）
```javascript
window.val = 1;
var obj = {
    val: 2,
    dbl: function () {
      this.val *= 2;
      val *= 2;
      console.log(val);
      console.log(this.val);
    }
};
//说出下面的输出结果
obj.dbl();//2,4
var func = obj.dbl;
func();//8,8
```


## 同源策略
指相同协议、域名、端口号，主要是出于安全考虑；




## 异步编程的方式有哪些
事件绑定，onclick   
函数回调：f1();f2()；——》f1(f2);   
观察者模式：发布、订阅事件；   
Promises对象：f1().then(f2)；   
链式写法；只用过基本的；   
es7里面好像还有async await；   




## 轮播图
通过横向布局方式，然后控制左右移动坐标来实现图片切换；   
纵向布局，通过opacity实现图片的切换；   
直接通过js去修改div的图片url；   
通过css的动画：-webkit-keyframes；   


## 你最喜欢的es6中的特性是什么，为什么。   
块级作用域（在某些情况下省去写闭包）；   
模板语法（字符串处理起来更加优雅，不用使用加号等符号了）；   
对象、数组解构；





## 终止ajax请求
```javascript
// jquery：（请求时判断是否ajaxclient是否存在，如果存在就终止，然后如果已经发送到服务器，服务器还是会正常执行，只不过是ajax的success是不会执行了）
var ajaxClient = null;
function test(params){
if(ajaxClient) ajaxClient.abort();
    ajaxClient = $.ajax({ type:'post', url:'/api/test' .... })
}
// axios：(本质还是调用的还是XMLHttpRequest的abort)
const CancelToken = axios.CancelToken;
const source = CancelToken.source();
axios.get('/api/test', {cancelToken:source.token}).catch(function(ex){
    if(axios.isCancel(ex)){ //取消请求的回调
    }
    else {// other error
    }
})；
source.cancel(); //取消请求
```


## 为什么用本地存储，而不用session，session更加简单啊！
session是基于cookie的。   
首先cookie只有4kb大小，并且cookie是跟随域名的，同一个域名下的所有请求都会携带cookie（除非设置cookie的path：字段为可以访问此cookie的页面路径，比如domain是abc.com,path是/test，那么只有/test路径下的页面可以读取此cookie），对服务器造成额外的开销。


## cookie和localStorage区别
cookie是用于维持状态的，localStorage是持久化的本地存储，除了手动删除，否则一直不会过期。而cookie是设置有效期的。并且cookie是可以与服务器发生通信的。    
cookie可以通过设置secure 字段，来判断是否只能通过https来传递此条cookie。


## localStorage和sessionstorage的区别是什么
生命周期和作用域不同。    
生命周期：Local Storage 是持久化的本地存储，存储在其中的数据是永远不会过期的，使其消失的唯一办法是手动删除；而 Session Storage 是临时性的本地存储，它是会话级别的存储，当会话结束（页面被关闭）时，存储内容也随之被释放。    
作用域：Local Storage、Session Storage 和 Cookie 都遵循同源策略。但 Session Storage 特别的一点在于，即便是相同域名下的两个页面，只要它们不在同一个浏览器窗口中打开，那么它们的 Session Storage 内容便无法共享。


## 如果LocalStorage中数据的值超过了范围，你该如何去处理这个问题
localstorage是每个域名有5m的大小，超出可以申请其他域名。   
并且结合postMessage()去实现跨域访问；otherWindow.postMessage(message, targetOrigin, [transfer]);    
message：将要发送到其他 window的数据。   
targetOrigin:通过窗口的origin属性来指定哪些窗口能接收到消息事件，其值可以是字符串"*"（表示无限制）或者一个URI。   
接受的窗口：window.addEventListener("message", receiveMessage, false);


## var a=b=1; var a=1;var b=1;
在非严格模式下，b的作用域会被提到全局。   
在严格模式下"use strict"，会报错：b未定义。


## dom、虚拟dom、diff算法
浏览器引擎工作流程:创建DOM Tree->创建Style Rules->构建Render Tree->布局Layout->绘制Painting    
DOM操作代价高：因为哪怕一个很简单的div都包括很多属性，这些属性大部分都会影响到布局和绘制；    
虚拟DOM：使用js对象来模拟DOM对象，因为在内存中操作js对象要比直接操作DOM快得多。最后完成虚拟dom操作之后再同步到DOM上。    
Diff算法：    
a.用js对象模拟dom tree，只需要记录它的节点类型、属性、子节点；function Element(tagName, props, children){ //返回存储dom的js对象 }    
b.根据a步骤中的Element方法返回的js对象，构建真正的dom节点，并设置这个节点的属性，再递归它的子节点进行构建，最后再append到body中；    
c.比较2棵虚拟dom tree的差异，2棵树的完全的diff算法时间复杂度是O(n^3)，但是在前端当中，很少会跨层次的去移动dom节点，所以virtual dom只会对同一个层次的节点进行对比，比如第二层级的div只会跟同一层次的div进行对比，这样时间复杂度就成了O(n)；    
d.对2棵树进行深度优先的遍历，每遍历一个节点就会把该节点和新的树进行对比，如果有差异就记录一个对象（这个对象一般叫patches）中，并且在记录差异的同时还需要记录差异类型，比如：替换节点，移动、删除、新增节点，修改节点属性等，其中列表对比（这就是为什么ul中的li需要添加key的原因）算法抽象出来其实就是字符串的最新编辑距离问题；    
e.把差异节点更新到真实的dom tree中：因为步骤a构建的js对象和真实的dom tree是一致的，所以我们可以对这个js对象进行深度优先的遍历，遍历时从差异对象（pathces）找出当前节点差异，然后进行DOM操作。    
总结：     
Virtual DOM 算法主要是实现上面步骤的三个函数：element，diff，patch。然后就可以实际的进行使用：
```javascript
// 1. 构建虚拟DOM
var tree = Element('div', {'id': 'container'}, [
    el('h1', {style: 'color: blue'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li')])
])

// 2. 通过虚拟DOM构建真正的DOM
var root = tree.render()
document.body.appendChild(root)

// 3. 生成新的虚拟DOM
var newTree = el('div', {'id': 'container'}, [
    el('h1', {style: 'color: red'}, ['simple virtal dom']),
    el('p', ['Hello, virtual-dom']),
    el('ul', [el('li'), el('li')])
])

// 4. 比较两棵虚拟DOM树的不同
var patches = diff(tree, newTree)

// 5. 在真正的DOM元素上应用变更
patch(root, patches)
```
当然这是非常粗糙的实践，实际中还需要处理事件监听等；生成虚拟 DOM 的时候也可以加入 JSX 语法。这些事情都做了的话，就可以构造一个简单的ReactJS了。    
参考地址：https://github.com/livoras/blog/issues/13


## CommonJS、AMD、CMD、UMD
CommonJS ：加载模块使用require方法，该方法读取一个文件并执行，最后返回文件内部的exports对象。并且加载模块是同步的，所以只有加载完成才能执行后面的操作。像Node.js主要用于服务器的编程，加载的模块文件一般都已经存在本地硬盘，所以加载起来比较快，不用考虑异步加载的方式，所以CommonJS规范比较适用。但如果是浏览器环境，要从服务器加载模块，这是就必须采用异步模式。所以就有了 AMD CMD 解决方案。   
AMD：是requirejs 在推广过程中对模块定义的规范化产出，它的模块支持对象 函数 构造器 字符串 JSON等各种类型的模块。
```javascript
define(['someModule1', ‘someModule2’], function (someModule1, someModule2) {
	function foo () {
	/// someing
		someModule1.test();
	}
	return {foo: foo}
});
```
CMD：是SeaJS 在推广过程中对模块定义的规范化产出   

CMD和AMD的区别有以下几点：    
1.对于依赖的模块AMD是提前执行，CMD是延迟执行。不过RequireJS从2.0开始，也改成可以延迟执行（根据写法不同，处理方式不通过）。   
2.CMD推崇依赖就近，AMD推崇依赖前置。    
3.两者定位有差异。RequireJS 想成为浏览器端的模块加载器，同时也想成为 Rhino / Node 等环境的模块加载器。SeaJS 则专注于 Web 浏览器端，同时通过 Node 扩展的方式可以很方便跑在 Node 服务器端    
4.两者遵循的标准有差异。RequireJS 遵循的是 AMD（异步模块定义）规范，SeaJS 遵循的是 CMD （通用模块定义）规范。规范的不同，导致了两者 API 的不同。SeaJS 更简洁优雅，更贴近 CommonJS Modules/1.1 和 Node Modules 规范。   
5.两者社区理念有差异。RequireJS 在尝试让第三方类库修改自身来支持 RequireJS，目前只有少数社区采纳。SeaJS 不强推，而采用自主封装的方式来“海纳百川”，目前已有较成熟的封装策略。    
6.两者代码质量有差异。RequireJS 是没有明显的 bug，SeaJS 是明显没有 bug。   
7.两者对调试等的支持有差异。SeaJS 通过插件，可以实现 Fiddler 中自动映射的功能，还可以实现自动 combo 等功能，非常方便便捷。RequireJS 无这方面的支持。    
8.两者的插件机制有差异。RequireJS 采取的是在源码中预留接口的形式，源码中留有为插件而写的代码。SeaJS 采取的插件机制则与 Node 的方式一致：开放自身，让插件开发者可直接访问或修改，从而非常灵活，可以实现各种类型的插件。
```javascript
//AMD
define(['./a','./b'], function (a, b) {
	//依赖一开始就写好
	a.test();
	b.test();
});
//CMD
define(function (requie, exports, module) {
	//依赖可以就近书写
	var a = require('./a');
	a.test();
	...
	//软依赖
	if (status) {
		var b = requie('./b');
		b.test();
	}
});
```
3.AMD的api默认是一个当多个用，CMD严格的区分推崇职责单一。例如：AMD里require分全局的和局部的。CMD里面没有全局的 require,提供 seajs.use()来实现模块系统的加载启动。CMD里每个API都简单纯粹。    
UMD：AMD和commonjs的综合产物；AMD是浏览器第一的发展原则，使用异步加载模块；CommonJS是服务器为第一的发展原则，选择同步加载。UMD是先判断是否支持Node.js的模块（export），存在则使用nodejs的模块方式也就是commonjs，再判断是否支持amd（define是否存在）；
```javascript
(function (window, factory) {
	if (typeof exports === 'object') {
		module.exports = factory();
	} else if (typeof define === 'function' && define.amd) {
		define(factory);
	} else {
		window.eventUtil = factory();
	}
	})(this, function () {
	//module ...
	}
);
```


## 说一说原型链、原型（这个要非常清楚），object是最上面的吗（null是最上面的）
javascript万物皆对象，分为普通对象和函数对象。凡是通过 new Function() 创建的对象都是函数对象，其他的都是普通对象。f1,f2,归根结底都是通过 new Function()的方式进行创建的。Function Object 也都是通过 New Function()创建的。   
实例的构造函数属性（constructor）指向构造函数。如下：
```javascript
function Person(name, age, job) {
 	this.name = name; 
 	this.sayName = function() { alert(this.name) }
 } 
var person1 = new Person('chj');
var person2 = new Person('sam',);
console.log(person1.constructor == Person); //true
console.log(person2.constructor == Person); //true
```
每个函数对象都有一个prototype属性，指向函数的原型对象。每个对象都有__proto__属性，但是只有函数对象才有prototype属性；   
原型对象：`var A = Person.prototype`
在默认情况下，所有的原型对象都会自动获得一个 constructor（构造函数）属性，这个属性（是一个指针）指向 prototype 属性所在的函数（Person）(constructor指向new的对象来源:Person)，即：   
```javascript
Person.prototype.constructor == Person    
person1.constructor == Person
```
原型对象（Person.prototype）是 构造函数（Person）的一个实例。如下：
```javascript
var A = new Person();    
Person.prototype = A;   
```
凡是通过 new Function( ) 产生的对象都是函数对象。因为 A 是函数对象，所以Function.prototype 是函数对象。    
原型对象主要是用来做继承；    
js在创建对象（不论是普通对象还是函数对象）都有一个__proto__的内置属性，用于指向创建它的构造函数的原型对象。所以：
```javascript
Person.prototype.constructor == Person
person1.__proto__ == Person.prototype
person1.constructor == Person
```
Object.prototype 对象也有proto属性，但它比较特殊，为 null 。因为 null 处于原型链的顶端，这个只能记住。
```javascript
Object.prototype.__proto__ === null
```
**每个对象的__proto__等于它构造函数的prototype，即：**
```javascript
person1.__proto__ 是什么？
因为 person1.__proto__ === person1 的构造函数.prototype
因为 person1的构造函数 === Person
所以 person1.__proto__ === Person.prototype
```
```javascript
Person.__proto__ 是什么？
因为 Person.__proto__ === Person的构造函数.prototype
因为 Person的构造函数 === Function
所以 Person.__proto__ === Function.prototype
```
```javascript
Person.prototype.__proto__ 是什么？
Person.prototype 是一个普通对象，我们无需关注它有哪些属性，只要记住它是一个普通对象。
因为一个普通对象的构造函数 === Object
所以 Person.prototype.__proto__ === Object.prototype
```
```javascript
Object.__proto__ 是什么？
因为Object.__proto__ === Object.constructor.prototype
因为Object.constructor === Function
所以Object.__proto__ === Function.prototype
```
```javascript
Object.prototype__proto__ 是什么？
Object.prototype 对象也有proto属性，但它比较特殊，为 null 。因为 null 处于原型链的顶端，这个只能记住。
Object.prototype.__proto__ === null
```
所有的构造器都来自于 Function.prototype，甚至包括根构造器Object及Function自身。所有构造器都继承了·Function.prototype·的属性及方法。如length、call、apply、bind    
知道了所有构造器（含内置及自定义）的__proto__都是Function.prototype，那Function.prototype的__proto__是谁呢？   
```javascript
console.log(Function.prototype.__proto__ === Object.prototype) // true
```
在 ECMAScript 核心所定义的全部属性中，最耐人寻味的就要数 prototype 属性了。对于 ECMAScript 中的引用类型而言，prototype 是保存着它们所有实例方法的真正所在。换句话所说，诸如 toString()和 valuseOf() 等方法实际上都保存在 prototype 名下，只不过是通过各自对象的实例访问罢了。   
```javascript
var Person = new Object()
```
Person 是 Object 的实例，所以 Person 继承了Object 的原型对象Object.prototype上所有的方法；
```javascript
var num = new Array()
```
num 是 Array 的实例，所以 num 继承了Array 的原型对象Array.prototype上所有的方法；
原型链：Array.prototype 继承了对象的所有方法，当你用num.hasOwnPrototype()时，JS 会先查一下它的构造函数 （Array） 的原型对象 Array.prototype 有没有有hasOwnPrototype()方法，没查到的话继续查一下 Array.prototype 的原型对象 Array.prototype.__proto__有没有这个方法。   
所以在我们输入console.log(Object.getOwnPropertyNames(Array))，并没有hasOwnPrototype，但是Array有原型对象：Array.prototype.__proto__ == Object.prototype
```javascript
function Person(){}
var person1 = new Person();
console.log(person1.__proto__ === Person.prototype); // true
console.log(Person.prototype.__proto__ === Object.prototype) //true
console.log(Object.prototype.__proto__) //null

Person.__proto__ == Function.prototype; //true
console.log(Function.prototype)// function(){} (空函数)

var num = new Array()
console.log(num.__proto__ == Array.prototype) // true
console.log( Array.prototype.__proto__ == Object.prototype) // true
console.log(Array.prototype) // [] (空数组)
console.log(Object.prototype.__proto__) //null

console.log(Array.__proto__ == Function.prototype)// true
```
原型和原型链是JS实现继承的一种模型。
原型链的形成是真正是靠__proto__ 而非prototype   
参考：[https://www.jianshu.com/p/dee9f8b14771](https://www.jianshu.com/p/dee9f8b14771)      
![原型链图](http://www.chenhongjun.cn/images/js_proto.png)


## 属性继承
如果访问者的属性是被继承的，他的`get`和`set`方法会在子对象的属性被访问或者修改时被调用。如果这些方法用一个变量寸值，该值会被所有实例化的对象共享。
```javascript
function myclass(){ }

var value;
Object.defineProperty(myclass.prototype, "x", {
   get() { return value; },
   set(x) { value = x; }
});

var a = new myclass();
var b = new myclass();
a.x = 1;
console.log(b.x); // 1
```
可以通过将值存储在另一个属性中固定。在`get`和`set`方法中，`this`指向某个被访问和修改属性的对象。
```javascript
function myclass(){ }

Object.defineProperty(myclass.prototype, "x", {
   get(){ return this.stored_x; },
   set(x){ this.stored_x = x; }
});

var a = new myclass();
var b = new myclass();
a.x = 1;
console.log(b.x); // undefined
```
不像访问者属性，值属性始终在对象自身上设置，而不是一个原型。然而，如果一个不可写的属性被继承，它仍然可以防止修改对象的属性。
```javascript
function myclass(){ }

myclass.prototype.x = 1;
Object.defineProperty(myclass.prototype, "y", {
   wirtable: false,
   value: 1
});

var a = new myclass();
a.x = 2;
console.log(a.x); // 2
console.log(myclass.prototype.x); // 1
a.y = 2; // 忽略，在严格模式下报错
console.log(a.y); // 1
console.log(myclass.prototype.y); // 1
```


## 继承的几种方式
```javascript
// 定义一个父类，用于实现下面的继承
function Animal (name) {
  // 属性
  this.name = name || 'Animal';
  // 实例方法
  this.sleep = function(){
    console.log(this.name + '正在睡觉！');
  }
}
// 原型方法
Animal.prototype.eat = function(food) {
  console.log(this.name + '正在吃：' + food);
};
```
- **原型链继承**   
**核心**：将父类的实例作为子类的原型
```javascript
function Cat(){ 
}
Cat.prototype = new Animal();
Cat.prototype.name = 'cat';

//　Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.eat('fish'));
console.log(cat.sleep());
console.log(cat instanceof Animal); //true 
console.log(cat instanceof Cat); //true
```
    特点：   
    1.非常纯粹的继承关系，实例是子类的实例，也是父类的实例   
    2.父类新增原型方法/原型属性，子类都能访问到   
    3.简单，易于实现   
    缺点：   
    1.要想为子类新增属性和方法，必须要在new Animal()这样的语句之后执行，不能放到构造器中   
    2.无法实现多继承   
    3.来自原型对象的所有属性被所有实例共享   
    4.创建子类实例时，无法向父类构造函数传参   
    
- **构造继承**   
**核心**：使用父类的构造函数来增强子类实例，等于是复制父类的实例属性给子类（没用到原型）
```javascript
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```
    特点：
    1.解决了1中，子类实例共享父类引用属性的问题
    2.创建子类实例时，可以向父类传递参数
    3.可以实现多继承（call多个父类对象）
    缺点：
    1.实例并不是父类的实例，只是子类的实例
    2.只能继承父类的实例属性和方法，不能继承原型属性/方法
    3.无法实现函数复用，每个子类都有父类实例函数的副本，影响性能
- **实例继承**   
**核心**：为父类实例添加新特性，作为子类实例返回
```javascript
function Cat(name){
  var instance = new Animal();
  instance.name = name || 'Tom';
  return instance;
}

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // false
```
    特点：
    1.不限制调用方式，不管是new 子类()还是子类(),返回的对象具有相同的效果
    缺点：
    1.实例是父类的实例，不是子类的实例
    2.不支持多继承
- **拷贝继承**   
**核心**：将父类的属性复制到子类上
```javascript
function Cat(name){
  var animal = new Animal();
  for(var p in animal){
    Cat.prototype[p] = animal[p];
  }
  Cat.prototype.name = name || 'Tom';
}

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```
    特点：
    1.支持多继承
    缺点：
    1.效率较低，内存占用高（因为要拷贝父类的属性）
    2.无法获取父类不可枚举的方法（不可枚举方法，不能使用for in 访问到）
- **组合继承**  
**核心**：通过调用父类构造，继承父类的属性并保留传参的优点，然后通过将父类实例作为子类原型，实现函数复用
```javascript
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
Cat.prototype = new Animal();

// 组合继承也是需要修复构造函数指向的。
Cat.prototype.constructor = Cat;

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // true
```
    特点：
    1.弥补了方式2的缺陷，可以继承实例属性/方法，也可以继承原型属性/方法
    既是子类的实例，也是父类的实例
    2.不存在引用属性共享问题
    3.可传参
    4.函数可复用
    缺点：
    1.调用了两次父类构造函数，生成了两份实例（子类实例将子类原型上的那份屏蔽了）
- **寄生组合继承**   
**核心**：通过寄生方式，砍掉父类的实例属性，这样，在调用两次父类的构造的时候，就不会初始化两次实例方法/属性，避免的组合继承的缺点
```javascript
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
(function(){
  // 创建一个没有实例方法的类
  var Super = function(){};
  Super.prototype = Animal.prototype;
  //将实例作为子类的原型
  Cat.prototype = new Super();
})();

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); //true

Cat.prototype.constructor = Cat; // 需要修复下构造函数
```
    特点：
    1.堪称完美
    缺点：
    1.实现较为复杂

参考：https://www.cnblogs.com/humin/p/4556820.html

## DOM中的attribute(特性)、property(属性)
`<div id='test' class='test' style='display:none;'></div>`   
`var dom = document.querySelector('#test')`
- attribute   
dom标签元素内的所有定义都是attribute特性，取值方法`dom.getAttribute('id')`

- property   
上述中的dom本质是一个js对象，可以直接操作dom的属性，取值方法`dom.id`，获取到的值和使用attribute获取的一致（前提是非自定义的dom特性，比如：id，class(js中是className)，style（js中style需要通过style.display来获取）等），如果是自定义的特性，则以分别设置的值为准

```javascript
// 自定义attribute设置
dom.setAttribute("test","1");
dom.test = "2";

dom.getAttribute("test"); // 1
dom.test; // 2

// 原生的attribute设置
dom.id = "test2";
dom.getAttribute("id"); // test2
```

使用javascript操作property更为方便、快捷，property支持各种不同的类型，并且对于布尔类型的attribute会进行自动转化，如：checked、disabled、selected等；   
MDN上ELement的attributes是属于property下面的一个属性；
