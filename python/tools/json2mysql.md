# json转存到mysql中
注意一下如果是批量的sql，需要批量执行`executemany`
```python
# -*- coding: utf-8 -*-
import json
import pymysql

with open("items.json", "r") as json_file:
    json_data = json.load(json_file)

conn = pymysql.connect(host='192.168.1.100', user='root', passwd='password', db='chj')
cursor = conn.cursor()
sql = "INSERT INTO images_info (name,description,is_deleted,create_time,update_time) VALUES (%s,%s,0,CURRENT_TIMESTAMP,CURRENT_TIMESTAMP)"
param = []
for item in json_data:
    param.append([item['file_name'], item['desc']])
cursor.executemany(sql, param)
conn.commit()
conn.close()
```
