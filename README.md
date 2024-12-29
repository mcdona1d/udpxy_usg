# udpxy_usg

仓库中udpxy是为mips64的usg编译好的二进制文件，可以直接执行

原始项目：https://github.com/pcherenkov/udpxy


启动命令：
```
chmod +x udpxy
./udpxy -S -a eth1 -p 4022 -m eth2 -c 10 -B 1MB -M 30
```
* 启动后会转后台运行
* eth1是usg的lan口
* eth2是usg的第二个wan口，直接连猫上的任意一个口（北京联通路由器没有专门的iptv口）


### 说明
```
udpxy 1.0-25.1 (prod) standard [Linux 3.10.107-UBNT mips64]
usage: ./udpxy [-vTS] [-a listenaddr] -p port [-m mcast_ifc_addr] [-c clients] [-l logfile] [-B sizeK] [-n nice_incr]
	-v : enable verbose output [default = disabled]
	-S : enable client statistics [default = disabled]
	-T : do NOT run as a daemon [default = daemon if root]
	-a : (IPv4) address/interface to listen on [default = 0.0.0.0]
	-p : port to listen on
	-m : (IPv4) address/interface of (multicast) source [default = 0.0.0.0]
	-c : max clients to serve [default = 3, max = 5000]
	-l : log output to file [default = stderr]
	-B : buffer size (65536, 32Kb, 1Mb) for inbound (multicast) data [default = 2048 bytes]
	-R : maximum messages to store in buffer (-1 = all) [default = 1]
	-H : maximum time (sec) to hold data in buffer (-1 = unlimited) [default = 1]
	-n : nice value increment [default = 0]
	-M : periodically renew multicast subscription (skip if 0 sec) [default = 0 sec]
Examples:
  ./udpxy -p 4022
	listen for HTTP requests on port 4022, all network interfaces
  ./udpxy -a lan0 -p 4022 -m lan1
	listen for HTTP requests on interface lan0, port 4022;
	subscribe to multicast groups on interface lan1

  udpxy and udpxrec are Copyright (C) 2008-2018 Pavel V. Cherenkov and licensed under GNU GPLv3
  Email: support@udpxy.com; Telegram: GigaX-discussions; Google+: udpxy community
```

## Unifi平台配置方法

IPTV网上相关的方案很多，但是在Unifi平台的案例不是很多。

这里使用组播（多播）转单播的方案：

背景：北京联通，未开通IPTV服务（可以白嫖IPTV直播）
主路由：Unifi Security Gateway （USG-3P）
无需其他设备

### 1. 接线
wan1接路由lan1，pppoe拨号
wan2接路由lan2，dhcp获取ip（192.168.1.3）

双WAN策略：故障转移

### 2. Unifi配置
全程无需添加静态路由，无需开启IGMP相关功能，仅需编辑防火墙

设置-->安全性-->防火墙规则-->创建条目
```
类型：Internet Local
名称：IPTV Multicast Rule
协议：UDP，预定义前
目的地：地址组：239.0.0.0/8
其他保持默认即可
```

### 3. 运行udpxy
见上文
启动后访问`http://10.0.0.1:4022/status/`确保运行正常

### 4. 测试
使用VLC播放
`http://10.0.0.1:4022/rtp/239.3.1.241:8000`

## AppleTV IPTV App推荐
* 「BestTV」换台超快，逻辑简单
* 「APTV」界面不错，一直保持更新，免费也可以用，换台相对略慢

## IPTV节目单
北京联通IPTV播放列表：https://github.com/wuwentao/bj-unicom-iptv

## 参考文档
https://www.haoyizebo.com/posts/6a0c2301/
https://www.xheldon.com/life/iptv-for-apple-tv-in-beijing.html