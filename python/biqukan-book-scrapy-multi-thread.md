```python
# -*- coding: utf-8 -*-
import datetime
import ssl
import threading
import urllib.request

import bs4

ssl._create_default_https_context = ssl._create_unverified_context

# 输入参数定义
book_url = 'https://www.biqukan.com/1_1408/'
book_name = 'fjwd'
break_url = ''  # 中断处的url，如果为空，则从头开始爬取
ajax_repeat_count = 10  # 最大重试次数不能超过9999
thread_count = 6


# 模拟浏览器访问url并获取页面内容（即爬取源码）
def get_html(url):
    ajax_count = 0
    user_agent = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/" \
                 "51.0.2704.103 Safari/537.36"
    headers = {"User-Agent": user_agent}
    html = ""
    while ajax_count < ajax_repeat_count:
        try:
            request = urllib.request.Request(url, headers=headers)
            response = urllib.request.urlopen(request, None, 10)
            html = response.read()
            ajax_count = 9999
        except Exception as e:
            ajax_count += 1
            print('请求{}失败，正在尝试再次请求！错误信息：{e}'.format(url, str(e)))
    return html


# 爬取整个网页（这里就细致一些，指定编码之类的）
def parse(url):
    html_doc = get_html(url)
    sp = bs4.BeautifulSoup(html_doc, 'html.parser', from_encoding="utf-8")
    return sp


# 获取书籍目录（正式开始了）
def get_book_dir(url):
    books_dir = []
    name = parse(url).find('div', class_='listmain')
    if name:
        dd_items = name.find('dl')
        dt_num = 0
        for n in dd_items.children:
            ename = str(n.name).strip()
            if ename == 'dt':
                dt_num += 1
            if ename != 'dd':
                continue
            books_info = {}
            if dt_num == 2:
                durls = n.find_all('a')[0]
                books_info['name'] = (durls.get_text())
                books_info['url'] = 'https://www.biqukan.com' + durls.get('href')
                books_dir.append(books_info)
    return books_dir


# 获取章节内容
def get_charpter_text(curl):
    text = parse(curl).find('div', class_='showtxt')
    if text:
        cont = text.get_text()
        return cont
    else:
        return ''


# 获取书籍（目录和内容整合）
def get_book(dir_list, book_name_index):
    # 目录
    if not dir_list:
        return dir_list
    start = break_url == ''  # 判断是否从中断处爬取，否则直接从头开始
    errors = []
    # 内容
    for d in dir_list:
        curl = d['url']
        try:
            title = '正在获取章节【{}】【内容】【{}】'.format(d['name'], d['url'])
            if (curl == break_url) and (start is False):
                start = True
            if start:
                print(datetime.datetime.now().strftime('%Y-%m-%d %H-%M-%S') + ":" + title)
                ctext = get_charpter_text(curl)
                with open("{}{}.txt".format(book_name, book_name_index), "a") as myfile:
                    myfile.write("【{0}】\n {1} \n".format(d['name'], ctext))
        except Exception as err:
            errors['text'] = "【{0}】\n {1} \n".format(d['name'], 'get failed')
            print(err)
    return errors


# 多线程爬取
def multi_thread_get_book(dir_list):
    # 定义二维数组
    _dirs_list = [[] for i in range(thread_count)]
    _each_count = int(len(dir_list) / thread_count)
    for i in range(thread_count):
        for j in range(len(dirs)):
            _filter = False
            if (i + 1) == thread_count:
                _filter = _each_count * i <= j
            else:
                _filter = _each_count * i < j <= _each_count * (i + 1)
            if _filter:
                _dirs_list[i].append(dirs[j])

    _threads = []
    for i in range(thread_count):
        t = threading.Thread(target=get_book, args=(_dirs_list[i], str(i + 1)))
        _threads.append(t)

    for i in range(thread_count):
        _threads[i].start()

    for i in range(thread_count):
        _threads[i].join()

    with open(book_name+".txt", 'a+') as rst_file:
        for i in range(thread_count):
            with open("{}{}.txt".format(book_name, str(i + 1))) as each_file:
                rst_file.write(each_file.read())


# 入口函数
if __name__ == '__main__':
    dirs = get_book_dir(book_url)
    if len(dirs) >= thread_count:
        multi_thread_get_book(dirs)
    else:
        get_book(dirs, "all")
```
