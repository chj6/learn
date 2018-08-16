
# fix滚动问题

fix元素跟在滚动条一起左右滚动，防止在width不足以显示fix元素的时候，导致显示不全；
``` javascript
    var sl = - Math.max(document.body.scrollLeft, document.documentElement.scrollLeft);
    $("fixele").css({ "left": sl + "px" });
```