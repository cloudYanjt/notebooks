# vi编辑器兼容模式

## 问题背景
在 Debian 下使用 vi 编辑器时发现 方向键变成了 ABCD 且退格键也没用。

## 解决方案

使用 vi 编辑如下配置文件
`vi /etc/vim/vimrc.tiny`

在文末添加两行就可以切换到非兼容模式了。

```
set nocompatible
set backspace=2
```
