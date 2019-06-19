# 质数    
质数（素数）：除本身的绝对值外，不可能被大于1的整数除尽的数


- **双for实现**
```javascript
function test(n) {
    var count = 0;
    if (n < 2) {
      return count;
    }
    
    for (var i = 2; i <= n; i++) {
      for (var j = 2; j < i; j++) {
        if (i % j == 0) {
          count++;
          break;
        }
      }
    }
    return n - count - 1;
  }
  
  var dt_start = new Date();
  var rst = test(1000000);
  console.log((new Date()) - dt_start, '运行时间');
  console.log(rst, '素数个数');
```

- **筛选法**
```javascript
function test2(n) {
    var count = 0;
    if (n < 2) {
      return 0;
    }
    var remove_obj = {};
    for (var i = 2; i < n; i++) {
      if (remove_obj[i] == 0) {
        continue;
      }
      var tmp_value = 2;
      while ((tmp_value * i) <= n) {
        if (remove_obj[tmp_value * i] == 0) {
          tmp_value++;
          continue;
        }
        remove_obj[tmp_value * i] = 0;
        tmp_value++;
        count++;
      }
    }
    return n - count - 1;
  }
  
  var dt_start = new Date();
  var rst = test2();
  console.log((new Date()) - dt_start, '运行时间ms');
  console.log(rst, '素数个数');

```
