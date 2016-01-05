title: Composer初始化
categories: TECH
tags: [PHP, Composer]
---
### Composer[http://docs.phpcomposer.com/]
- 之前一直听说和使用compose，最近开始研究composer。Composer 类似于包管理器，但它在每个项目的基础上进行管理，在你项目的某个目录中（例如 vendor）进行安装。默认情况下它不会在全局安装任何东西。因此，这仅仅是一个依赖管理。

### 搭建Composer，以及遇到的问题
- 环境 ： Mac OS X EI Captian = 10.11.2 (15C50)
- 使用homebrew直接安装
```bash
brew update
brew tap homebrew/php
brew tap homebrew/versions
brew install php56-intl
brew install homebrew/php/composer
```

[之前按照文档走，brew tap josegonzalez/php，然后就会出现两个环境，
```bash
Error: Formulae found in multiple taps:
```
最后brew untap josegonzalez/php，删除掉，然后在原来的环境里安装]

 
- 新建项目文件夹 (composer/vendor)
```bash
composer init //按照步骤一个个输入
composer install //这时候就已经在项目文件夹下安装好了
```

#### [遇到的问题]
- [composer diagnose]
- [Checking composer version: FAIL]执行 
```bash
composer global self-update
curl -sS https://getcomposer.org/installer | php -- --check
composer dump-autoload
rm -rf /path/to/composer.lock /path/to/vendor/
```
- [Checking composer.json: FAIL],composer validate
return:
```bash
./composer.json is valid, but with a few warnings
See https://getcomposer.org/doc/04-schema.md for details on the schema
No license specified, it is recommended to do so. For closed-source software you may use "proprietary" as license.
```
在.composer.json中配置两个字段：
```language
{
    "name": "xxx/test_composer",
    "description": "dev",
    "authors": [
        {
            "name": "xxx",
            "email": "xxx@cric.com"
        }
    ],
    "minimum-stability": "alpha",
	//这个字段解决The property description is required
	"license": "proprietary",
	"require": {}
}
```

##### 遇到的问题基本都解决了，环境也搭建好了，接下来就能使用composer了。感谢作者Rob [https://github.com/alcohol?tab=activity] 耐心解答所有使用者的问题，收获良多。
