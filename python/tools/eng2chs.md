# 英文转换成中文，并更新到数据库中
```python
# -*- coding: utf-8 -*-

import datetime
import json
import ssl
from urllib import parse
from urllib import request

import pymysql

ssl._create_default_https_context = ssl._create_unverified_context

# 加id主要是为了方便观察数据进行到哪里了
def eng_to_chs(query_str ,id):
    url = "https://fanyi.so.com/index/search"
    Form_Data = {}
    Form_Data['query'] = query_str
    Form_Data['eng'] = '1'
    data = parse.urlencode(Form_Data).encode('utf-8')
    response = request.urlopen(url, data)
    html = response.read().decode("utf-8")
    result = json.loads(html)
    translate_result = result["data"]["fanyi"]
    print(str(id)+":"+translate_result)
    return translate_result


def update_sql():
    conn = pymysql.connect(host='192.168.1.100', user='root', passwd='password', db='chj', charset='utf8')
    cursor = conn.cursor()
    select_sql = "select * from images_info"
    update_sql = "update images_info set ch_description=%s where id = %s "
    cursor.execute(select_sql)
    results = cursor.fetchall()
    param = []
    for row in results:
        id = row[0]
        description = row[3]
        chs = eng_to_chs(description, id)
        param.append([chs, datetime.datetime.now(), id])
        # 每100条数据进行一次提交，防止积累过多的未提交数据
        if len(param) > 100:
            cursor.executemany(update_sql, param)
            conn.commit()
            param = []
    conn.close()


if __name__ == "__main__":
    update_sql()
```
编码问题，容易造成异常如下：**InternalError:(pymysql.err.InternalError) (1366, "Incorrect string value:....**：   
1.确保数据库编码为 `utf-8`   
2.确保数据库表的编码为`utf-8`   
3.确保数据库字段编码为 `utf-8`   
4.程序连接数据库指定编码：`pymysql.connect(host='192.168.1.100', user='root', passwd='password', db='chj', charset='utf8')`   
5.文本的编码问题也应该指定成：`utf-8`
