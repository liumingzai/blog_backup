---
title: 针对git clon yilia主题到自己的blog中遇到的问题解决方案
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## 按照yilia主题的介绍git clone到自己blog的themes下

### 此时git remote地址不是你的git库，需要手动配置,否则在git操作会提示Updates were rejected beacouse...

``` bash
$ git remote rm ... | git add remote | git remote set-url 
```

### 修改完后去修改yilia下的config.yml会发现git add .没有更新，反复git pull无果，git push提示'XXX' is in submodule, 回到themes目录

``` bash
$ git rm -rf --cached yilia
$ git add yilia
```

### 然后就可以看到提交的目录了

``` bash
$ 正常的git commit | git push
```

### hexo博客的常用部署命令和步骤：


``` bash、
$ hexo clean
$ hexo generate
$ hexo deploy
```

