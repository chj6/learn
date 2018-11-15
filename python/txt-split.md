```python
# coding=utf-8

i = 0
j = 0
with open("./pl.txt", 'r', encoding='GB18030') as f:  # 不同os，需要注意编码格式
    for line in f.readlines():
        i = i + 1
        if i % 10000 != 0:
            k = str(j)
            path = './rst/pl' + k + '.txt'
            g = open(path, 'a')
            g.write(line)
        else:
            print(line)
            j += 1
```
