---
title: Python rtree install | Python中Rtree配置问题
layout: post
tags:
  - Python
  - Pip
  - Brew

---
  
最近老是遇到配置Python环境的问题，特别是遇到一些小众一点的包，并没有包括在一些IDE里，所以得各种自己搞。这里写几个常用的方法，主要当做备忘录。

在做空间计算的时候，即便用了numpy，scipy，虽然对于矩阵运算有了很大的提高。但是并没有对类型地理空间数据进行优化，一般要用一个Rtree的算法来减小索引次数，这个算法在Python中有个对应的包。但Rtree需要先装一个叫libspatialindex的包，具体可以去![这里下载安装](http://libspatialindex.github.io/install.html)，有种更简单的办法是用**brew**来装。只用一行命令：

>brew install spatialindex

然后去![这儿把Rtree下载下来](https://pypi.python.org/pypi/Rtree/0.8.2)，找到相应目录后安装一下：

>python setup.py install

用Brew和Pip这两个工具安装管理个种包还是比较方便的，自己的感觉是安一个IDE，相当于把主流的包都装上，然后再用Brew和Pip安一些自己可能用到但又没有那么常用的包。不过装IDE之后有的会修改默认的Python路径，改成自己的，但卸载后并不会删除这个路径，会导致Python出错，如果有类似的情况，把出错信息Google一下一般会有答案。一般是把~/.bash_profile中的IDE注释掉就行了。

找到一个链接，对于怎么配置说得比较明白：http://matrix.windhunter.net/blog/2012/01/setup-python-env-on-lion-471.html
