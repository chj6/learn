# mac安装python记录

因为mac默认装了python2.7，所以一般都是新装一个python版本；

- 1.下载最新版 [官网地址](https://www.python.org/downloads/)
- 2.直接安装
- 3.终端中执行`open ~/.bash_profile`
- 4.修改文件之后保存，并重启终端，配置如下：
   ```
   export PATH="/Library/Frameworks/Python.framework/Versions/3.7/bin:${PATH}"
   alias python="/Library/Frameworks/Python.framework/Versions/3.7/bin/python3.7"
   ```
- 5.在终端运行`python -V`验证版本即可

**注意：pip和pip3，如果sdk是用的python3.7，此时安装包一定是要用通过pip3安装，否则无法找到对应的包**