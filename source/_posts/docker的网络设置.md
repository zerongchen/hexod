---
title: docker的网络设置
date: 2019-07-01 23:09:15
tags:
---
# 容器间是如何互相通讯的

1，iptables 是否开启。在容器起来的时候会自动开启Iptables（可以手动开启iptables）
2，需要互相访问的容器是否已经是链接到相同的网桥上面去（即 拓扑图是不是互通的）
    容器起来的时候会创建一对veth ，一个放在网桥（docker0） 对内的接口上，命名一般都是vethXXXX，另外一个veth会放在容器内部命名问veth0(可以联想成，我们自己的电脑的那个veth0，是默认的网卡)
    【veth 在docker0 的对内接口可以通过brctl show 查看docker0 上 interfaces】
    当一个数据包到达veth 一端的时候，另外一段也可以收到一样的数据（这是通过内核数据拷贝实现的，所以两端传输的数据速度很快，容器间的相互访问，基本上就是内核的数据拷贝。几乎可以忽略性能影响
```
bridge name	bridge id		STP enabled	interfaces
docker0		8000.0242f9c70e0f	no		veth0622f45
```


2，容器与外界是如何互相通讯的
docker0 网桥是在docker启动的时候初始话的一个网桥（可以自定义）。然后自动分配一个ip地址（大多例如：172.17.42.1/16）;
一对veth的创建后