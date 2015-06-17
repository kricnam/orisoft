**基本步骤**

  1. 使用ssh客户端软件接入CEWA，用户名root,密码：xxxxx
  1. 设置CEWA时间为当前时间，比如2010年10月2日13点45，输入以下命令
```
date -s 201010021345
```
  1. 接入VPN，运行
```
/startvpn
```
  1. 添加本地IP,以便访问被监控设备，可以使用以下命令添加和监控设备同一网段的IP地址。如果需要监控多个网段的设备，可以增加多个网段IP地址，接口设备名eth0:X，X从0开始需要依次增加。_注意:设备和CEWA必须在同一个以太网冲突域内_
```
ifconfig -a eth0:0 192.168.2.90
```
  1. 配置NAT端口映射，以便数据包可以经过VPN，通过CEWA代理到达被监控设备。比如被监控设备IP是192.168.2.222，通讯端口是TCP的23，将其映射到CEWA的23端口，使用以下命令。有多个设备端口时，分别对他们进行配置
```
iptables -t nat -A PREROUTING -i tun0 -p tcp --dport 23 -j DNAT --to-destination 192.168.2.222:23
```
  1. 配置数据包回程路由，回程IP 192.168.2.90是前面增加的CEWA在被监控网段的IP。如果多个被监控网段，则分别对每个进行网段配置。配置命令格式参考如下：
```
iptables -t nat -A POSTROUTING -d 192.168.2.0/24 -j SNAT --to-source 192.168.2.90
```

**实用技巧**

  * 使用iptables-save可以保存已经使用的配置
```
iptables-save > 配置文件名
```
  * 使用iptables-restore可以使配置文件中的配置内容生效
```
iptables-restore < 配置文件名
```
  * 通过编辑导出的配置文件，可以简化操作

**_备忘_**

  * 192.168.101.4设备上，根目录下test.sh完成CEWA IP地址添加和NAT设置，是对192.168.2.X和169.254.X.X网段的监控