title: 升级PHP，遇到的PHP-FPM问题
categories: TECH
tags: [PHP]
---

### PHP-FPM遇到的问题
昨天安装了composer，今天打算试下，但是发现我
```bash
php -v
PHP 5.5.3 (cli)
```
明明早就升级到5.6了。卸载了5.6，又安装了7.0，还是显示5.5。最后觉得应该是PATH，重新export了一遍PATH,source ~/.bash_profile，再尝试就ok了。

接下来以为一切ok，可以试用7.0了，结果http://127.0.0.1/居然是显示(PHP Version 5.6.13),homebrew已经卸载了5.6版本，重启了nginx也无济于事。

最后觉得应该是fpm需要重启下，不然不可能卸载了还在运行。
```bash
# 先关闭之前的php-fpm
sudo killall php-fpm

# 启动PHP-fpm，但需要退出
/usr/local/Cellar/php70/7.0.1/sbin/php-fpm --fpm-config /usr/local/etc/php/7.0/php-fpm.conf

# 启动PHP-fpm
/usr/local/Cellar/php70/7.0.1/sbin/php70-fpm start
```

重新访问下localhost，版本对了。


### 现在在想php-fpm的问题，难道我卸载了php，只要不重启fpm，服务器还是能正常访问？



