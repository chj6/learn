# 文件夹、文件操作
##### 文件夹合并

```python
# -*- coding: utf-8 -*-
import os
import shutil

path = os.path.dirname(os.getcwd()) + '/bak/'

# 合并images目录下所有的子目录中的文件到images根目录下
for root, dirs, files in os.walk(path):
    for file in files:
        old_path = root + "/" + file
        new_path = path + "/" + file
        shutil.move(old_path,new_path)
```

##### 删除空的文件夹
```python
# -*- coding: utf-8 -*-
import os

path = os.path.dirname(os.getcwd()) + '/bak/'

# 删除images目录下的空文件夹
for root,dirs,files in os.walk(path):
    if len(files) == 0:
        os.rmdir(root)
```

##### 根据json对文件重命名

```python
# -*- coding: utf-8 -*-
import json
import os

# 根据json重新命名文件
with open("items.json", "r") as json_file:
    json_data = json.load(json_file)

path = os.path.dirname(os.getcwd()) + '/images/'

for item in json_data:
    file_name = item['file_name']
    tmp_src = item['src_href']
    old_file_name = tmp_src.split('?')[0].split('/')[5]
    old_file_path = path + '/' + old_file_name
    new_file_path = path + '/' + file_name

    if os.path.exists(old_file_path):
        os.rename(old_file_path, new_file_path)
```
