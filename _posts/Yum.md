title: Question - Yum No module named yum
categories: Question
tags: [LINUX]
---

#### No module named yum
##### 不知道centOS什么时候更新过yum，今天执行yum intall，居然报问题了。
```shell
There was a problem importing one of the Python modules
required to run yum. The error leading to this problem was:

   No module named yum

Please install a package which provides this module, or
verify that the module is installed correctly.

It's possible that the above module doesn't match the
current version of Python, which is:
2.7.8 (default, Dec 12 2016, 11:07:53) 
[GCC 4.4.7 20120313 (Red Hat 4.4.7-17)]

If you cannot solve this problem yourself, please go to 
the yum faq at:
  http://yum.baseurl.org/wiki/Faq
```

在网上找了很久，都说是Python和yum版本不匹配，我现在的环境，Python有两个版本，v2.6.6，v2.7.8，当前Python运行环境的版本是2.7.8，yum的版本是
```
rpm -qa | grep yum

yum-plugin-security-1.1.30-37.el6.noarch
yum-3.2.29-75.el6.centos.noarch
yum-metadata-parser-1.1.2-16.el6.x86_64
yum-utils-1.1.30-37.el6.noarch
yum-plugin-fastestmirror-1.1.30-37.el6.noarch
```
按道理yum版本没问题，也按照网上的办法，改了/usr/bin/yum的头声明
```
#!/usr/bin/python
-> 
#!/usr/bin/python2.6
```
运行yum还是不行，报同样的问题。

#### 推测问题
推测估计是升级Python2.7.8的时候，原来Python2.6下的包被删了，导致无法运行。重新强制安装一遍所需要的rpm包
```yum.sh
#!/bin/bash
URL= http://mirror.centos.org/centos/6/os/x86_64/Packages/
for package in \
    python-devel-2.6.6-64.el6.x86_64.rpm \
    python-iniparse-0.3.1-2.1.el6.noarch.rpm \
    python-setuptools-0.6.10-3.el6.noarch.rpm \
    python-urlgrabber-3.9.1-11.el6.noarch.rpm \
    rpm-python-4.8.0-55.el6.x86_64.rpm \
    yum-3.2.29-73.el6.centos.noarch.rpm \
    yum-3.2.29-73.el6.centos.src.rpm \
    yum-metadata-parser-1.1.2-16.el6.x86_64.rpm \
do
    \'rpm -Uvh --replacepkgs $URL$package --force\'
done
```

重装完毕以后运行yum可以正常运行了。

