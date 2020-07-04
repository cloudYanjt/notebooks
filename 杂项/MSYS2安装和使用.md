### 1、安装MSYS2

官网下载： https://www.msys2.org/

### 2、更换国内源
安装目录etc\pacman.d 下有三个文件。 这三个文件记录了都有哪些源，mirrors.ustc.edu.cn 是中科大的源，把这个放在第一位会优先使用。
mirrorlist.msys               mirrorlist.mingw64                mirrorlist.mingw32

### 3、引入windows的PATH环境变量

默认MSYS2环境变量的PATH中隔离了windows的PATH，要打通MSYS2和windows的环境变量，需要在Windows下创建一个环境变量。

 MSYS2_PATH_TYPE=inherit

### 4、更新环境
3.1 更新本地软件包数据库：
pacman -S --refresh
可以缩写为：pacman -Sy

3.2 然后升级软件包：
pacman -S --refresh --sysupgrade 
可以缩写为：pacman -Syu

### 5、常用 pacman 命令
4.1 安装
pacman -S package-name

4.2 删除
pacman -R package-name

4.3 列出所有安装了的软件
pacman -Q --explicit
或者
pacman -Q -e

4.4 搜索软件包
列出可用的包，包含未安装的包
pacman -Sl
使用grep查找相关的包
pacman -Sl|grep package-name
使用正则查找
pacman -Ss package-name

### 设置右键上下文

将下面内容保存为.reg文件，双击运行。“D:\\MSYS2”替换成本机msys2的安装路径。
Extended项表示“shift+右键”触发。

```reg
Windows Registry Editor Version 5.00
;空白处点击右键
[HKEY_CLASSES_ROOT\Directory\Background\shell\msys2_shell]
@="Msys2 Ba&sh Here"
"Icon"="D:\\MSYS2\\msys2.ico"
;"Extended"=""

[HKEY_CLASSES_ROOT\Directory\Background\shell\msys2_shell\command]
@="D:\\MSYS2\\msys2_shell.cmd -msys -here"

;文件夹上点击右键
[HKEY_CLASSES_ROOT\Directory\shell\msys2_shell]
@="Msys2 Ba&sh Here"
"Icon"="D:\\MSYS2\\msys2.ico"
;"Extended"=""

[HKEY_CLASSES_ROOT\Directory\shell\msys2_shell\command]
@="D:\\MSYS2\\msys2_shell.cmd -msys -where \"%V\""
```

