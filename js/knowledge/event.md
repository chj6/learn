## js运行机制
JS 执行是单线程的，它是基于事件循环的。事件循环大致分为以下几个步骤：   
（1）所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。   
（2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。   
（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。   
（4）主线程不断重复上面的第三步。   


## 事件捕获、冒泡
当一个事件发生在具有父元素的元素上时，现代浏览器会运行2个不同的阶段 - 捕获阶段和冒泡阶段，这2个阶段就是获取这个元素所在的DOM树分支上的所有祖先节点然后进行捕获和冒泡。    
捕获阶段：   
- 浏览器检查原属的最外层祖先<html>，是否在捕获阶段中注册一个onclick事件处理程序，如果是，则运行它
- 然后，它移动到<html>中单击元素的下一个祖先元素，并执行相同的操作，然后是单击元素再下一个祖先元素，直到到达实际点击的元素    
冒泡阶段：   
- 浏览器检查实际点击的元素是否在冒泡阶段注册了一个onclick事件处理程序，如果是则运行onclick的事件
- 然后它移动到下一个直接的祖先元素，并做同样的事情，然后是下一个，等等，直到它到达<html>元素    
`e.preventDefault()`：阻止默认行为，比如在表单校验时：
```javascript
form.submit = function(e) {
  if( name ==='' ){
    e.preventDefault();
    alert("用户名不能为空")
  }
}
```
`e.stopPropagation()`：停止冒泡，它只会让当前事件处理程序运行，但事件不会在冒泡链上进一步扩大，因此将不会有更多事件处理器被运行(不会向上冒泡)。
```javascript
// 此时只会执行method1、method2，因为div是冒泡链的上一层
var btn = document.getElementById("btn_id");
var btn_parent_div = document.getElementById("btn_parent_div_id");
btn_parent_div.addEventListener("click", div_method1);

btn.addEventListener("click", method1);
btn.addEventListener("click", method2);

function method1(e){
  e.stopPropagation(); // 停止冒泡
  console.loog("method1");
}

function method2(e){
  console.loog("method2");
}
```

## 事件绑定addEventListener、attachEvent
**addEventListener(type, listener[, options])：**   
*注意：那些不支持参数options的浏览器，会把第三个参数默认为useCapture，即设置useCapture为true*    
options表示{   
capture:false（capture值为true时：表示listener会在该类型的事件捕获阶段传播到该EventTarget时触发）,     

passive:false（passive值为true时：表示listener永远不会调用preventDefault()，如果listener仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告，在后续的chrome版本中已经升级成为错误了）（*PS：由于浏览器必须在事件执行结束之后才能知道有没有使用e.preventDefault(),这样就导致了浏览器不能及时响应滚动，从而产生卡顿。所以为了让页面滚动流畅，从chrome56开始，在window、document和body上面的touchstart、touchmove事件处理函数，默认passive:true，从而达到忽略preventDefault，这样浏览器在第一事件就可以进行滚动了。但是此时如果在事件里面调用preventDefault，浏览器就会报错:Unable to preventDefault inside passive event listener due to target being treated as passive，比如fastclick库里就存在这种情况，而且fastclick已经停更好几年了，所以最好自己处理一下*）,     

once:false（once值为true时表示listener在添加之后最多调用一次。如果是true，listener会在其被调用之后自动移除）   
}；   
addEventListener优势：
- 允许给一个事件注册多个监听器
- 提供了更精细的手段控制listener的触发阶段（可以选择冒泡或者捕获）
- 它对任何DOM元素（元素Element、Document、Window）都有效而不仅仅只是对HTML有效；
addEventListener注意点：
- 当注册了同一个listener时，重复的实例会被抛弃掉，**capture参数的值需要一样，否则会重复2次，但是passive、once参数就算不一样也只会执行一次**：
```javascript
// 执行顺序：method1 -> method2
btn.addEventListener("click", method1);
btn.addEventListener("click", method1); // 相同的会被抛弃掉一个，抛弃原则：抛弃后面注册的，所以当前的method1会被抛弃
btn.addEventListener("click", method2);

// 执行顺序：method1 -> method1 -> method2
btn.addEventListener("click", method1, { capture:false });
btn.addEventListener("click", method1, { capture:true }); // 因为capture参数不一致，所以都会执行，但是passive、once参数不一致也只会执行一次，会将这个method1抛弃
btn.addEventListener("click", method2);
```
- addEventListener的listener中的this是指向当前元素的，btn.onclick=method：this指向的是btn，相比之下：<a onclick='method'>、attachEvent的this指向的是window;
- addEventListener在IE8下无法使用，所以在做PC端的时候需要处理一下，removeEventListener：移除事件的绑定

**attachEvent(type, listener)，IE特有**
attachEvent绑定的事件都是在冒泡阶段时触发。detachEvent：移除事件的绑定

```javascript
var btn = document.getElementById("btn_id");

// 执行顺序：method3，前面2个方法会被覆盖掉
btn.onclick = method1;
btn.onclick = method2;
btn.onclick = method3;

// 执行顺序：method3 -> method2 -> method1
btn.attachEvent("onclick", method1);
btn.attachEvent("onclick", method2);
btn.attachEvent("onclick", method3);

// 执行顺序：method1 -> method2 -> method3
btn.addEventListener("click", method1);
btn.addEventListener("click", method2);
btn.addEventListener("click", method3);

// 此时如果将btn元素，置于div中
// 执行顺序：div_method2 -> method1 -> method2 -> div_method1
var btn_parent_div = document.getElementById("btn_parent_div_id");
btn_parent_div.addEventListener("click", div_method1);
btn_parent_div.addEventListener("click", div_method2, { capture: true }); // 表明在div_method2事件在捕获阶段就会被触发，默认是在冒泡时才会触发事件
btn.addEventListener("click", method1);
btn.addEventListener("click", method2);
```

## oninput，onchange，onpropertychange区别
1、onchange 事件与 onpropertychange 事件的区别：
- onchange 事件在内容改变（两次内容如果相同，则不会触发change）且失去焦点时触发。
- onpropertychange 事件却是实时触发，即每增加或删除一个字符就会触发，通过 js 改变也会触发该事件，但是该事件 IE 专有。
 
2、oninput 事件与 onpropertychange 事件的区别：
- oninput 事件是 IE 之外的大多数浏览器支持的事件，在 value 改变时触发，实时的，即每增加或删除一个字符就会触发，然而通过 js 改变 value 时，却不会触发，需要自己主动调用input事件，`var event = new Event('input''); element.dispatchEvent(event);`  
- onpropertychange 事件是任何属性改变都会触发的，而 oninput 却只在 value 改变时触发，oninput 要通过 addEventListener() 来注册，onpropertychange 注册方式跟一般事件一样。（此处都是指在js中动态绑定事件，以实现内容与行为分离）

3、oninput 与 onpropertychange 失效的情况：
- oninput 事件：a). 当脚本中改变 value 时，不会触发；b).从浏览器的自动下拉提示中选取时，不会触发。
- onpropertychange 事件：当 input 设置为 disable=true后，onpropertychange 不会触发。
 
 
## 节流(Throttle)和防抖(Debounce)
目的：防止scroll、resize、keyup、mousemove等事件被频繁触发，导致页面出现抖动或卡顿的情况。    
**函数节流throttle**：throttle会强制函数以固定的速率（时间或滚动举例等）执行。因此这个方法比较适合于动画相关的场景。    
实现思路：在每次触发input事件时，以固定的时间间隔去执行方法，先判断是否时间间隔满足条件，如果不满足就赋值到定时器中，以防止如果是最后一次，能正常执行。
```javascript
function throttle(fn, threshhold) {
 var timeout
 var start = new Date;
 var threshhold = threshhold || 160
 return function () {

 var context = this, args = arguments, curr = new Date() - 0
 
 clearTimeout(timeout)// 清除回调事件
 if(curr - start >= threshhold){ 
     console.log("now", curr, curr - start)// 注意这里相减的结果，都差不多是160左右
     fn.apply(context, args) // 只执行一部分方法，这些方法是在某个时间段内执行一次
     start = curr
 }else{
 //让方法在脱离事件后也能执行一次
     timeout = setTimeout(function(){
        fn.apply(context, args) 
     }, threshhold);
    }
  }
}
var mousemove = throttle(function(e) {
 console.log(e.pageX, e.pageY)
});

// 绑定监听
document.querySelector("#panel").addEventListener('mousemove', mousemove);
```

**函数防抖debounce**：指定时间内js中多个调用的方法，只执行最后一次调用的。适用场景：全文搜索、用户校验等；     
实现思路：在每次触发input事件时，防抖时不需要进行时间间隔的判断，只需要clear掉上一次的timeout，然后设置一个新的定时器，这样确保在规定的间隔时间内只会执行最后一次。
```javascript
function debounce(func, delay) {
    var timeout;
    return function(e) {
        clearTimeout(timeout);
        var context = this, args = arguments
        timeout = setTimeout(function(){
          func.apply(context, args);
        },delay)
    };
};

var validate = debounce(function(e) {
    console.log("change", e.target.value, new Date-0)
}, 300);

// 绑定监听
document.querySelector("input").addEventListener('input', validate);
```
