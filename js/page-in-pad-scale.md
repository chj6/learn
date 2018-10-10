
# 网页在平板上面的缩放适配

PC网页在平板上面的缩放适配，防止页面横向宽度超出，从而出现滚动条
``` javascript
    function isInPad() {
        var userAgentInfo = navigator.userAgent;
        var rgx = /pad|iPad/i;
        return rgx.test(userAgentInfo);
    }

    function autoSetViewport() {
        var winWidths = window.outerWidth;
        var _winViewport = document.getElementById("viewportid");
        if (winWidths >= 1170 || !_winViewport) {
            return;
        }
        var scallVal = winWidths/1170 < 0.5 ? 0.5 : Math.floor((winWidths/1170)*10)/10;
        _winViewport.setAttribute('content', 'width=device-width, initial-scale=' + scallVal + ', minimum-scale= '+ scallVal +', maximum-scale=1.0, user-scalable=no');
    }

    if (isInPad()) {
        autoSetViewport();
        var evt = "onorientationchange" in window ? "orientationchange" : "resize";
        window.addEventListener(evt,function(){
            autoSetViewport();
        },false);
    }
```