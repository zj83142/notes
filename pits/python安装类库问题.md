## 在win7平台上安装gevent和gevent-socketio

在Linux平台上安装gevent就是几个命令的事情, 有了gcc之类的编译环境很快就可以搞定, 但是windows上要编译什么的就麻烦太多了

gevent依赖greenlet, 从源码安装的话, 还需要cython的支持和编译libev. 而greenlet在windows平台上则需要编译libevent. 因此我们至少需要:

- libevent的dll文件
- greenlet的免编译安装包
- gevent的免编译安装包

### libevent.dll

下载地址：
  - http://www.dll-found.com/libevent-2-0-5.dll_download.html (需要翻墙)
  - http://pan.baidu.com/s/1ge5DxT5 (百度云盘)

安装方法：
  测试环境是windows 7 64bit, 安装了32bit的Python 2.7版本. 因此将下载到的libevent-2-0-5.dll复制到C:\Windows\SysWOW64目录下即可.

### greenlet 和 gevent

下载地址：** http://www.lfd.uci.edu/~gohlke/pythonlibs/ **

安装方法：
  - pip install greenlet-0.4.5-cp27-none-win32.whl
  - pip install gevent-1.0.1-cp27-none-win32.whl

### gevent-socketio

安装方法：pip install gevent-socketio
