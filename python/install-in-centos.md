# centos 7.5 64位 安装python记录

因为centos默认装了python2.7，所以一般都是新装一个python3.7版本；   
1.安装开发者工具
```
yum -y groupinstall "Development Tools"
```
2.安装Python编译依赖包
```
yum -y install openssl-devel zlib-devel bzip2-devel sqlite-devel readline-devel libffi-devel systemtap-sdt-devel
```
3.下载安装包
```python
wget https://www.python.org/ftp/python/3.7.0/Python-3.7.0.tgz
```
4.解压与编译
```python
tar zvxf Python-3.7.0.tgz
cd Python-3.7.0
./configure --prefix=/usr/local/python3.7 --enable-optimizations
make && make install
```
5.配置文件目录   
添加文件：`vim /etc/profile.d/python37.sh`
```python
if [ -z ${PYTHON37_HOME} ]; then
    export PYTHON37_HOME=/usr/local/python3.7
    export PATH=${PYTHON37_HOME}/bin:${PATH}
fi
```
6.加载环境变量
```python
source /etc/profile.d/python37.sh
```
7.测试
```python
python3 -V
```

**注意：对应的包管理是pip3**
