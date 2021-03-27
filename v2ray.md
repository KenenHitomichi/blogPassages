---
title: 记一次CentOS7中V2Ray的安装配置
date: 2021-01-23 23:16:50
categories:
	- network
tags:
	- v2ray
---

## 前言
不管怎么说 既然在vultr买了centos7的服务器，不搭个梯子似乎也说不大过去。以前搭过几次ss，效果不尽如人意，这次便尝试着试试v2ray吧。（不用担心屏蔽词的感觉真好）



## 前期准备

基本的准备也没啥好说的，putty或者xshell之类的工具，一个国外服务器（不要用国内代理商的，会被封机子）。看看安装手册，研究研究配置和协议，OK，准备开工！

## V2Ray服务端安装配置

先用date -R看看服务器时间，误差在90s之内就ok。如果差距较大的话就重设一下系统时间。

确认时间没问题后，开始下载脚本。

```
wget https://install.direct/go.sh
bash go.sh
```

运行完之后直接报错，

```
ERROR: This script has been DISCARDED, please switch to fhs-install-v2ray project.
```

没啥好说的，看来是我看的官方文档过时了，只能去git看看了。（看的是作者的blog，原来应该看v2fly的文档才对）

按照git的说明，再运行如下命令

```
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
```

这次顺利地安装完成了，按照安装路径的提示打开config.json，发现一片空白。那么就直接按照<a href="https://www.v2fly.org/guide/start.html">教程</a>复制了。

```
{
    "inbounds": [
        {
            "port": 10086, // 服务器监听端口
            "protocol": "vmess",
            "settings": {
                "clients": [
                    {
                        "id": "b831381d-6324-4d53-ad4f-8cda48b30811"
                    }
                ]
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom"
        }
    ]
}
```

然后通过systemctl start v2ray运行，用netstat看了看10086端口，确实有在监听，看上去不错。

## V2Ray客户端安装配置

服务端完成后，就是尝试客户端。由于客户端多是windows，那么我也按照windows的来。

同样采用官网的配置

```
{
    "inbounds": [
        {
            "port": 1080, // SOCKS 代理端口，在浏览器中需配置代理并指向这个端口
            "listen": "127.0.0.1",
            "protocol": "socks",
            "settings": {
                "udp": true
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "vmess",
            "settings": {
                "vnext": [
                    {
                        "address": "server", // 服务器地址，请修改为你自己的服务器 ip 或域名
                        "port": 10086, // 服务器端口
                        "users": [
                            {
                                "id": "b831381d-6324-4d53-ad4f-8cda48b30811"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "protocol": "freedom",
            "tag": "direct"
        }
    ],
    "routing": {
        "domainStrategy": "IPOnDemand",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:private"
                ],
                "outboundTag": "direct"
            }
        ]
    }
}
```

替换掉ip地址，使用<a href="https://github.com/2dust/v2rayN/releases">V2RayN</a>。一开始疯狂连接失败，显示outbound traffic failed。tcping了一下发现是vultr防火墙没有开端口的问题，开了firewall的端口后成功连接，使用了<a href="https://github.com/Loyalsoldier/v2ray-rules-dat">v2ray-rules</a>作为路由规则。虽然是3.5刀的机子，但速度没得说，vultr每月500G流量，直接起飞！

## 总结

自己搭的服务器果然要比别人那买来的稳定和快速很多，而且用着也还放心，就是只有一个节点，但也算够用。只是单纯的记录一下搭建的流程，因此写的不是很详尽，而且研究也不深，能用即可。

<br>

Author: Hitomichi

<span style="color:red">欢迎转载，请注明出处</span>