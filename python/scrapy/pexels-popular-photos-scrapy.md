# scrapy抓取图片

1.安装
```python
pip/pip3 install scrapy
```

2.使用scrapy生成项目   
因为抓取的图片地址是：https://www.pexels.com，所以项目名叫pexels
```python
scrapy startproject pexels #生成项目
cd pexels
scrapy genspider pexelsSpider pexels.com #生成爬虫脚本，后面一定得带上目标网站得域名
```

3.items.py代码编写   
items.py主要是Model的定义，用于在scrapy框架中流转和数据存储
```python
# -*- coding: utf-8 -*-

import scrapy

class PexelsItem(scrapy.Item):
    # define the fields for your item here like:
    name = scrapy.Field()
    src_href = scrapy.Field()
    page_index = scrapy.Field()
```

4.pexelsSpider.py代码编写   
这里抓取的是pexels.com的流行图片，所以start_urls是从流行图片开始，剩下的都是一些xpath规则编写。   
唯一需要注意的是，在进行next_link请求时，`dont_filter=True`不然?page=会被过滤掉
```python
# -*- coding: utf-8 -*-
import scrapy

from pexels.items import PexelsItem


class PexelsspiderSpider(scrapy.Spider):
    name = 'pexelsSpider'
    allowed_domains = ['pexles.com']
    start_urls = ['https://www.pexels.com/popular-photos/all-time/?page=1']
    page_index = 1

    def parse(self, response):
        src_list = response.xpath("//div[@class='photos']//a[@download]")

        current_page_index = response.xpath("//div[@class='pagination']/em/text()").extract_first()

        for item in src_list:
            tmp_path = item.xpath("@href").extract_first()
            pexels_item = PexelsItem()
            pexels_item['name'] = tmp_path.split('?')[0].split('/')[5]
            pexels_item['src_href'] = tmp_path
            pexels_item['page_index'] = current_page_index
            yield pexels_item
        next_link = response.xpath("//div[@class='pagination']/a[@rel='next']/text()").extract_first()
        if next_link:
            yield scrapy.Request("https://www.pexels.com/popular-photos/all-time/?page=" + next_link,
                                 callback=self.parse, dont_filter=True)
```

5.middlewares.py代码编写   
中间件处理，这里添加一个useragent中间件，防止请求被服务器阻止掉
```python
class my_useragent(object):
    def process_request(self, request, spider):
        # user agent 列表
        USER_AGENT_LIST = [
            "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; AcooBrowser; .NET CLR 1.1.4322; .NET CLR 2.0.50727)",
            "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.0; Acoo Browser; SLCC1; .NET CLR 2.0.50727; Media Center PC 5.0; .NET CLR 3.0.04506)",
            "Mozilla/4.0 (compatible; MSIE 7.0; AOL 9.5; AOLBuild 4337.35; Windows NT 5.1; .NET CLR 1.1.4322; .NET CLR 2.0.50727)",
            "Mozilla/5.0 (Windows; U; MSIE 9.0; Windows NT 9.0; en-US)",
            "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Win64; x64; Trident/5.0; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 2.0.50727; Media Center PC 6.0)",
            "Mozilla/5.0 (compatible; MSIE 8.0; Windows NT 6.0; Trident/4.0; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; .NET CLR 1.0.3705; .NET CLR 1.1.4322)",
            "Mozilla/4.0 (compatible; MSIE 7.0b; Windows NT 5.2; .NET CLR 1.1.4322; .NET CLR 2.0.50727; InfoPath.2; .NET CLR 3.0.04506.30)",
            "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN) AppleWebKit/523.15 (KHTML, like Gecko, Safari/419.3) Arora/0.3 (Change: 287 c9dfb30)",
            "Mozilla/5.0 (X11; U; Linux; en-US) AppleWebKit/527+ (KHTML, like Gecko, Safari/419.3) Arora/0.6",
            "Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.8.1.2pre) Gecko/20070215 K-Ninja/2.1.1",
            "Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN; rv:1.9) Gecko/20080705 Firefox/3.0 Kapiko/3.0",
            "Mozilla/5.0 (X11; Linux i686; U;) Gecko/20070322 Kazehakase/0.4.5",
            "Mozilla/5.0 (X11; U; Linux i686; en-US; rv:1.9.0.8) Gecko Fedora/1.9.0.8-1.fc10 Kazehakase/0.5.6",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/535.11 (KHTML, like Gecko) Chrome/17.0.963.56 Safari/535.11",
            "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_3) AppleWebKit/535.20 (KHTML, like Gecko) Chrome/19.0.1036.7 Safari/535.20",
            "Opera/9.80 (Macintosh; Intel Mac OS X 10.6.8; U; fr) Presto/2.9.168 Version/11.52",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/536.11 (KHTML, like Gecko) Chrome/20.0.1132.11 TaoBrowser/2.0 Safari/536.11",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.1 (KHTML, like Gecko) Chrome/21.0.1180.71 Safari/537.1 LBBROWSER",
            "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E; LBBROWSER)",
            "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; QQDownload 732; .NET4.0C; .NET4.0E; LBBROWSER)",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/535.11 (KHTML, like Gecko) Chrome/17.0.963.84 Safari/535.11 LBBROWSER",
            "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.1; WOW64; Trident/5.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)",
            "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; WOW64; Trident/5.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E; QQBrowser/7.0.3698.400)",
            "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; QQDownload 732; .NET4.0C; .NET4.0E)",
            "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1; Trident/4.0; SV1; QQDownload 732; .NET4.0C; .NET4.0E; 360SE)",
            "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; QQDownload 732; .NET4.0C; .NET4.0E)",
            "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 6.1; WOW64; Trident/5.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; .NET4.0E)",
            "Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.1 (KHTML, like Gecko) Chrome/21.0.1180.89 Safari/537.1",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.1 (KHTML, like Gecko) Chrome/21.0.1180.89 Safari/537.1",
            "Mozilla/5.0 (iPad; U; CPU OS 4_2_1 like Mac OS X; zh-cn) AppleWebKit/533.17.9 (KHTML, like Gecko) Version/5.0.2 Mobile/8C148 Safari/6533.18.5",
            "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:2.0b13pre) Gecko/20110307 Firefox/4.0b13pre",
            "Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:16.0) Gecko/20100101 Firefox/16.0",
            "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.11 (KHTML, like Gecko) Chrome/23.0.1271.64 Safari/537.11",
            "Mozilla/5.0 (X11; U; Linux x86_64; zh-CN; rv:1.9.2.10) Gecko/20100922 Ubuntu/10.10 (maverick) Firefox/3.6.10"]
        User_Agent = random.choice(USER_AGENT_LIST)
        request.headers['User_Agent'] = User_Agent

```

6.piplines.py代码编写    
管道处理，这里添加了一个图片的管理处理
```python
# -*- coding: utf-8 -*-

import scrapy
from scrapy.exceptions import DropItem
from scrapy.contrib.pipeline.images import ImagesPipeline

class PexelsImagesPipeline(ImagesPipeline):
    def get_media_requests(self, item, info):
        image_url = item['src_href']
        referer = image_url  # 处理防盗链
        yield scrapy.Request(image_url, meta={'item': item, 'referer': referer})

    # 图片存储路径设置
    def file_path(self, request, response=None, info=None):
        item = request.meta['item']
        folder = item['page_index']
        image_name = item['name']
        filename = u'{0}/{1}'.format(folder, image_name)
        return filename

    def item_completed(self, results, item, info):
        image_paths = [x['path'] for ok, x in results if ok]
        if not image_paths:
            raise DropItem("Item contains no images")
        print(image_paths)
        return item
```

7.setting.py配置    
配置文件
```python
# robots协议改成False
ROBOTSTXT_OBEY = False 

# 中间件添加
DOWNLOADER_MIDDLEWARES = {
    'pexels.middlewares.my_useragent': 400
}

# 管道添加
ITEM_PIPELINES = {
   'pexels.pipelines.PexelsImagesPipeline': 300,
}
# 图片存放的路径
IMAGES_STORE = 'images'
# 下载延迟，防止请求太快被服务器阻止掉
DOWNLOAD_DELAY = 0.1

```

7.__init__.py代码编写    
方便启动
```python
from scrapy import cmdline
cmdline.execute("scrapy crawl pexelsSpider".split())
```

最后在当前目录的终端下运行：`scrapy`。
