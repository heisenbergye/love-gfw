# `Linux` 和 `MacOS` 设备智能分流方案

![](./img/socialist.jpg) ![](./img/gfw.jpg)

通过路由器 360 度无痛爱国的方案已经层出不穷，然而我们不得不面对一个很现实的问题：**你不可能走到哪里都带着一个路由器！**

为了解决这个问题，本教程就诞生了，目标是手把手教你在不同操作系统上 360 度无死角自动翻越万里长城。:clap:

PS: 目前只能在 Linux 和 MacOS 系统上实现， Windows
 用户请绕行

## 本教程爱国方案的特点
放弃建立黑名单的方案吧，被墙的网站每天在大量增加，有限的人生不能在无穷的手工添加黑名单、重启设备中度过。

大道至简，一劳永逸！

+ 建立国内重要网站名单，在国内进行dns查询
+ 其他网站通过 `shadowsocks` 客户端向 `shadowsocks` 服务端进行 dns 查询
+ 国内或亚洲的 IP 流量走国内通道
+ 其他流量通过 `shadowsocks` 服务端转发

## 知识若不分享，实在没有意义

什么是圣人，圣人就是得到和付出比较均衡的人。天地生我，我敬天地；父母育我，我亦养父母；网上获得知识，也要在网上分享知识。于是，花了许多天，查资料，写教程，调试固件，不知不觉一天就过去了。

自由的感觉真好:　`youtube`, `hulu`, `twitter`, `facebook`, `google`...

本文档不涉及 `shadowsocks` 的原理及基础配置，如果你连这些基本的知识也没掌握，请左转绕道而行，了解这些基本的知识之后再来看本教程。

欢迎提 `Issues` 参与维护项目。

<br />

> 这里有两种思路可以实现全局智能分流，一种思路是通过防火墙策略，另一种思路是通过策略路由表。

## 通过防火墙策略实现

防火墙工具有很多种，我尝试过并且成功实现功能的有两种，一个是 `iptables`，另一个是 `nftables`。`iptables` 大家应该都比较熟悉，`nftables` 对于大多数人来说也许比较陌生，如果你想进一步了解，请参考 [Linux 首次引入 nftables，你可能会喜欢 nftables 的理由](http://blog.jobbole.com/59624/)

**遗憾的是，该方案并不适用于 MacOS 系统，如果你有什么好的建议，欢迎给我提供帮助。**

### 1. 通过 iptables 实现智能分流

这种方案的思路是使用 `ipset` 载入 chnroute 的 IP 列表并使用 `iptables` 实现带自动分流国内外流量的全局代理

+ [Linux 系统](./docs/iptables-linux.md)
+ MacOS 系统：暂无实现，与之类似的方案请参考 [一个基于 VirtualBox 和 openwrt 构建的项目, 旨在实现 macOS / Windows 平台的透明代理](https://github.com/icymind/VRouter)

### 2. 通过 nftables 实现智能分流

+ [Linux 系统](./docs/nftables-linux.md)
+ MacOS 系统：暂无实现

## 通过策略路由表实现

这种方案的大致思路是先启动一个本地 `socks` 代理，然后通过工具将 `socks` 代理伪装成 `vpn`，最后再通过策略路由进行分流。

有两种工具可以将 `socks` 代理伪装成 `vpn`。

### 1. 通过 badvpn 实现智能分流

主要介绍一下 `tun2socks`，它其实是 `badvpn` 的一个组成部分。

`tun2socks` 实现一种机制，它可以让你无需改动任何应用程序而完全透明地将数据用 `socks` 协议封装，转发给一个 `socks` 代理，然后由该代理程序负责与真实服务器之间转发应用数据。

使用代理有两种方式，一种是你自己显式配置代理，这样一来，数据离开你的主机时它的目标地址就是代理服务器，另一种是做透明代理，即在中途把原始数据重定向到一个应用程序，由该代理程序代理转发。

`tun2socks` 在第二种的基础上，完成了`socks` 协议的封装，并且实现该机制时使用了强大的 `tun` 网卡而不必再去配置复杂的 `iptables` 规则。

+ [Linux 系统](./docs/badvpn-linux.md)
+ MacOS 系统：暂时无法编译成功，如有人编译成功，望告知

### 2. 通过 gotun2socks 实现智能分流

`gotun2socks` 实际上是 `badvpn` 的 `go` 语言实现方式，而且更加智能化，它会在启动时自动帮你添加 tuntap 网卡，停止时自动删除该网卡，不需要我们手动添加删除。怎么样，是不是有点小激动呢？是不是从此爱上 go 语言了呢？:relieved:

+ [Linux 系统](./docs/gotun2socks-linux.md)
+ [MacOS 系统](./docs/gotun2socks-macos.md)
