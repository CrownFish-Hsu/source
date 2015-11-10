title: PHP相关
categories: PHP
tags: [tech]
---


### PHP文件的解释过程
##### 前提：[需要弄清楚cli, cgi, sapi的概念](http://php.net/manual/zh/features.commandline.php)
SAPI是Server Application Programming Interface（服务器应用编程接口）的缩写。PHP通过SAPI提供了一组接口，供Application和PHP内核之间进行数据交互。

在代码库里和命令行下分别查看sapi名称：
```php
echo php_sapi_name();  //cgi-sapi
```
```bash
php -r "echo php_sapi_name();" //sli-sapi
```
这就清楚了，应用在Web服务器中的就叫做CGI SAPI，应用在终端上的SAPI就叫做CLI SAPI。在windows下安装php你会看到两个exe：php.exe和php-cgi.exe这个就对应的是这两种SAPI。

终端查看PHP版本时，也会输出相关信息  ***5.6.12 (cli)***
```bash
php -v 	//PHP 5.6.12 (cli) (built: Aug  8 2015 01:24:09) Copyright (c) 1997-2015 The PHP Group Zend Engine v2.6.0, Copyright (c) 1998-2015 Zend Technologies
```



