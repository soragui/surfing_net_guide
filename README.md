>记录自己搭建 shadowsocks 的过程

## 购买VPS
我是在[搬瓦工](www.bwh1.net)上购买的最VPS,每个月18元左右，搭建一个ss服务器还是绰绰有余的，每个月也有1000G的流量，根本用不完。[这是教程](byvps.md)，当然你也可以换其他的。

搬瓦工默认安装的是CentOS，我把它换成了 ubuntu 16.04，毕竟比较熟悉。

## 安装 shadowsocks 

shadowsocks server 的实现方法有C/C++, python, go等，这里我安装 C/C++ 版，这个比较稳定，用起来也很方便，在 ubuntu 下安装方法如下：

```bash
sudo add-apt-repository ppa:max-c-lv/shadowsocks-libev -y
sudo apt update
sudo apt install shadowsocks-libev
```

安装成功后会添加 ss-server 命令，接着就配置.json 文件：

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
    "method":"chacha20-ietf-poly1305"
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
