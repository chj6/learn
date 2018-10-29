# sessionStorage无法在用户主动打开的tab页进行共享   
a.利用BroadcastChannel（兼容性一般）；

b.参考如下js代码，自己实现   
``` javascript
$(window).on('storage', message_receive);

function message_broadcast(message)
{
    localStorage.setItem('message',JSON.stringify(message));
    localStorage.removeItem('message');
}

function message_receive(ev)
{
    if (ev.originalEvent.key!='message') return; // ignore other keys
    var message=JSON.parse(ev.originalEvent.newValue);
    if (!message) return; // ignore empty msg or msg reset

    // here you act on messages.
    // you can send objects like { 'command': 'doit', 'data': 'abcd' }
    if (message.command == 'doit') alert(message.data);
    // etc.
}
```