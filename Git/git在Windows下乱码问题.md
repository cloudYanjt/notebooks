# git在Windows下乱码问题

## 1、统一设置编码为 utf-8

```
$ git config --global core.quotepath false # 解决 git status 中文件路径的编码问题
$ git config --global gui.encoding utf-8 # 图形Git GUI界面编码
$ git config --global i18n.commit.encoding utf-8 # 提交信息编码
$ git config --global i18n.logoutputencoding utf-8 # 输出 log 编码
```

## 2、修改 git 安装目录下 /etc/profile 文件

在 profile 文件中添加环境变量 `export LESSCHARSET=utf-8`