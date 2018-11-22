# scrapy在windows安装

```
building 'twisted.test.raiser' extension
error: [WinError 3] 系统找不到指定的路径。: 'C:\\Program Files (x86)\\Micros
oft Visual Studio 14.0\\VC\\PlatformSDK\\lib'
```

解决方法：   
- [下载地址](http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted )：下载对应的文件，根据系统的版本和python的版本选择：Twisted‑18.9.0‑cp37‑cp37m‑win_amd64.whl（python3.7 64位操作系统）
- 打开下载的目录执行：`pip install wheel`
- 继续执行：`pip install Twisted‑18.9.0‑cp37‑cp37m‑win_amd64.whl`
- 最后：`pip install scrapy`即可

mac和centos应该是自带了这个依赖，windows需要处理一下。
