>记录自己搭建 shadowsocks 的过程

## 目录

- [购买VPS](#%E8%B4%AD%E4%B9%B0vps)
- [安装 shadowsocks server](#%E5%AE%89%E8%A3%85-shadowsocks-server)
- [Ubuntu 安装 shadowsocks client](#ubuntu-%E5%AE%89%E8%A3%85-shadowsocks-client)
- [服务端使用 TCP BBR 优化网络速度](#%E6%9C%8D%E5%8A%A1%E7%AB%AF%E4%BD%BF%E7%94%A8-tcp-bbr-%E4%BC%98%E5%8C%96%E7%BD%91%E7%BB%9C%E9%80%9F%E5%BA%A6)
- [结语](#%E7%BB%93%E8%AF%AD)

## 购买VPS
我是在[搬瓦工](https://bwh1.net/)上购买的最VPS,每个月18元左右，搭建一个ss服务器还是绰绰有余的，每个月也有1000G的流量，根本用不完。[这是教程](byvps.md)，当然你也可以换其他的。

搬瓦工默认安装的是CentOS，我把它换成了 ubuntu 16.04，毕竟比较熟悉。

## 安装 shadowsocks server

shadowsocks server 的实现方法有C/C++, python, go等，这里我安装 C/C++ 版，这个比较稳定，用起来也很方便，在 ubuntu 下安装方法如下：

```bash
sudo add-apt-repository ppa:max-c-lv/shadowsocks-libev -y
sudo apt update
sudo apt install shadowsocks-libev
```

安装成功后会添加 ss-server 命令，接着就配置.json 文件：

```bash
vim /etc/shadowsocks-libev/config.json
```

如下所示,把服务器、端口、密码等信息修改成自己的就行了：

```json
{
    "server":"127.0.0.1",
    "server_port":8388,
    "local_port":1080,
    "password":"focobguph",
    "timeout":60,
    "method":"aes-256-cfb"
}
```
最后用如下命令启动服务：
```bash
systemctl start shadowsocks-libev.service
```
然后就是在系统启动时自动运行：
```bash
systemctl enable shadowsocks-libev.service
```
可以查看一下运行状态：
```bash
systemctl status shadowsocks-libev.service
```

## Ubuntu 安装 shadowsocks client

> 因为其它平台的客户端 [windows](https://github.com/shadowsocks/shadowsocks-windows) [macOS](https://github.com/shadowsocks/ShadowsocksX-NG) [android](https://github.com/shadowsocks/Shadowsocks-android) 都可以直接下载，而且还很好用。虽然 Linux 平台有客户端，但个人觉得使用起来不方便，而且还不太稳定。

Ubuntu 下安装客户端和安装服务器一样，我这里选择C语言实现的 shadowsocks-libev, 其中就包含客户端命令 ss-local, 下面只需要配置 .json 文件就可以了：

```bash
vim /etc/shadowsocks-libev/config.json
```

```json
{
    "server":"server ip",
    "server_port":8388,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"focobguph",
    "timeout":60,
    "method":"aes-256-cfb"
}
```

配置文件和服务器配置文件基本相同，只需加上本地代理地址就可以了，下面就是启动了：

```bash
sudo systemctl start shadowsocks-libev-local@config.service
```

同意可以配置为开机启动：

```bash
sudo systemctl enable shadowsocks-libev-local@config.service
```

到此，如果一切顺利，客户端配置完成，不过现在还不能连接，还需配置一下浏览器或系统代理，本地代理的地址如下：

```
socks5://127.0.0.1:1080
```

你可以配置系统或浏览器代理，然后就可以尽情的GOOGLE了。

## 服务端使用 TCP BBR 优化网络速度
>TCP BBR(Bottleneck Bandwidth and RTT) 是谷歌开发的一种TCP阻塞控制算法。[根据谷歌的描述](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=0f8782ea14974ce992618b55f0c041ef43ed0b78)，此算法可以不仅可以提高带宽，还可以降低延迟。

首先使用如下命令来检查一下系统可用的阻塞控制算法：
```bash
sysctl net.ipv4.tcp_available_congestion_control
```
输出结果一般如下所示：
```bash
net.ipv4.tcp_available_congestion_control = cubic reno
```
然后通过如下命令检查当前所用的 TCP 阻塞算法：
```bash
sysctl net.ipv4.tcp_congestion_control
```
输出的结果是你系统正在使用的算法，如果是 bbr 那么你可以跳过此段了，如果不是，继续往下看。

接着需要检查一下你的内核版本是不是高于4.9：
```bash
uname -r
```
如果输出结果比4.9版本高，那么可以跳过此步骤，如果不是 16.04 可以使用如下命令升级内核：
```bash
sudo apt update
sudo apt install --install-recommends linux-generic-hwe-16.04
```

好了，到了最后一步很简单使用如下命令修改 sysctl.conf 文件：
```bash
sudo vim /etc/sysctl.conf
```
添加如下两行即可：
```bash
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
```
保持退出后，使用如下命令重新加载：
```bash
sudo sysctl -p
```
好了，你现在可以检查一下系统使用的阻塞算法了。如果是 bbr，那么就加速成功了。

## 结语
如果发现任何错误或建议，欢迎提出PR请求。
