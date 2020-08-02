网卡的配置在：/etc/sysconfig/network-scripts/下，配置文件：ifcfg-网卡名。
网络配置参考文件 /usr/share/doc/initscripts-*/sysconfig.txt

示例：

```properties
# 配置文件应用到的设备名称，必须和 `NAME` 值一样
DEVICE=ens32
# 网卡物理设备名称
NAME=ens32
# 接口类型，常见有的Ethernet, Bridge
TYPE=Ethernet
# 代理方式：默认关闭状态
PROXY_METHOD=none
# 仅浏览器：否
BROWSER_ONLY=no
# 设置网卡获得ip地址的方式，可能的选项为static(静态)，dhcp(dhcp协议)或bootp(bootp协议).
BOOTPROTO=static
# 默认路由：是
DEFROUTE=yes
# 如果ipv4配置失败禁用设备,默认否
IPV4_FAILURE_FATAL=no
# ip地址
IPADDR=192.168.188.129
# 网关
GATWAY=192.168.188.2
# 子网掩码24位，和NETMASK=255.255.255.0作用相同
PREFIX=24
# 子网掩码
NETMASK=255.255.255.0
# DNS服务器
DNS1=114.114.114.114
DNS2=8.8.8.8
# 如果ipv6配置失败禁用设备,默认否
IPV6_FAILURE_FATAL=no
# IPV6是否自动初始化: 是[不会有任何影响, 现在还没用到IPV6]
IPV6INIT=yes
# IPV6是否自动配置：是[不会有任何影响, 现在还没用到IPV6]
IPV6_AUTOCONF=yes
# IPV6是否可以为默认路由：是[不会有任何影响, 现在还没用到IPV6]
IPV6_DEFROUTE=yes
# IPV6地址生成模型：stable-privacy [这只一种生成IPV6的策略]
IPV6_ADDR_GEN_MODE=stable-privacy
# 设备的惟一标识
UUID=eced9b2f-8470-4f35-a7bf-15b311199b8b
# mac地址
HWADDR=78:2B:CB:57:28:E5
# 在系统引导时是否激活此设备
ONBOOT=yes

```

