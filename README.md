> 记录自己搭建 shadowsocks 的过程
> 最新发现的很详细的SSR安装教程，不过需要英文基础 [Tips For China SSR](https://www.tipsforchina.com/how-to-setup-a-fast-shadowsocks-server-on-vultr-vps-the-easy-way.html)，需要翻墙看😯.

## 最近发现打击力度有点大，后来测试发现自己搭建 shadowsocks 相关技术的都被墙了，而 outline 可以使用，不过客户端也需要使用 outline !

## 管理服务器[Outline](https://github.com/Jigsaw-Code/outline-server)

## 打击力度超级大，刚搭建的不到一天就寿终正寝，好了，只好在有需要的时候再搭建吧，shadowsocks-as-a-server。

1. 去 [vultr](https://www.vultr.com/?ref=7513206)申请，部署服务器，地址选美国比较好。在安装 之前 务必 Ping 一下。
2. ssh 进入，使用如下命令安装 
```bash
bash -c "$(wget -qO- https://raw.githubusercontent.com/Jigsaw-Code/outline-server/master/src/server_manager/install_scripts/install_server.sh)"
```
3. 然后安装，outline-manager 和 outline-client 就可以了。使用愉快！
