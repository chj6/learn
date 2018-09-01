# height属性记录
1. 当子元素的height:100%时，100%是从父元素继承而来，如果父元素未指定高度，此时高度就是0，所以如果想要body的height:100%生效，html的height也必须设置成100%；  


2. 当元素只设置min-height时，子元素设置height:100%时是无效的，因为子元素的height:100%是继承父元素的height，但是min-heights是在height生效之后才会起作用；


3. 页面body中有3个元素  
   div1 -> height:100px;   
   div2 -> height:100px;  
   div3 -> height:100%;  
   此时，**html和body必须同时都为100%**时，div3的高度才会是100%，否则div3的高度是0；或者body的高度设置为100px时，div3的高度则也是100px;