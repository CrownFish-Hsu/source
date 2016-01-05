title: 搭建Hexo
categories: TECH
tags: [Git]
---
Welcome to [Hexo](http://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/zh-cn/docs/index.html) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](http://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](http://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](http://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](http://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](http://hexo.io/docs/deployment.html)

### 以上命令都可以简写
``` bash
$ hexo g
$ hexo s
$ hexo d
```

### 遇到的问题
_config.yml文件内容：
```yaml
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: git@github.com:<uesrname>/<uesrname>.github.io.git
  branch: master
```
执行git deploy时，windows下没有问题，Mac下回报错：
*ERROR Deployer not found: git*
论坛里有说配置时候冒号后面要空格，(type: xxx)，也有说
repository: git@github.com:<uesrname>/<uesrname>.github.io.git
修改为：
repository: https://github.com/<uesrname>/<uesrname>.github.io.git，
还有的说是版本问题：
#### 查看版本 （windows和Mac都是V_3.1）
```
hexo -v
##hexo: 3.1.1
```

最后在[github的hexojs板块找到解决方案](https://github.com/hexojs/hexo/issues/1154)：
```bash
npm install hexo-deployer-git --save
```

