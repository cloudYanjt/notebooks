# Debain执行sh脚本报错[[: not found

最近在把 Centos 上的脚本移到 Debian 下执行时遇到报错 [[: not found
在 Centos 上一点问题没有，但在 Debian 下就无法执行。

```
#!bin/bash

pkill -f frps

if [[ $? == 1 ]]; then
    echo "not found frps process"
fi
```

最后网上找到了解释：”Ubuntu默认的sh是连接到dash，又因为dash跟bash的不兼容所以出错了...“。
想想 Ubuntu 也是基于 Debian 的衍生版本，那问题应该是一样的。

两种解决方式
* 第一种：
修改sh默认连接到bash：
执行命令 `sudo dpkg-reconfigure dash` 选择no即可.
* 第二种：
修改不兼容的语法，例如：
if [[ ]] 改成 if []
== 改成 =

参考：[https://blog.csdn.net/kevin_liu11/article/details/51822992](https://blog.csdn.net/kevin_liu11/article/details/51822992)

