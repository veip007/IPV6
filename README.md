## 一、纯IPV6服务器安装V2ray

**1.首先修改DNS，输入以下命令即可。这是为了能访问v4网址。**
```
echo -e "nameserver 2001:67c:2b0::4 \nnameserver 2001:67c:2b0::6" > /etc/resolv.conf
```
**2.安装unzip，解压v2ray需要。还有psmisc，防止某些系统不自带killall。以下命令是针对debian的。**
```
apt update
apt install unzip psmisc
```
**3.新建文件夹/root/v2，并移动过去。后面所有操作都在这里进行。 ```此处V2文件夹名字随意```**
```
mkdir -p /root/v2
cd /root/v2
```
**4.下载caddy和v2ray。默认下载的是Linux amd64位版本。**
```
wget --no-check-certificate "https://raw.githubusercontent.com/veip007/IPV6/master/caddy" 
wget --no-check-certificate "https://github.com/v2ray/v2ray-core/releases/download/v4.28.2/v2ray-linux-64.zip" && unzip -o v2ray-linux-64.zip v2ray v2ctl geosite.dat geoip.dat && rm v2ray-linux-64.zip
chmod +x caddy v2ray v2ctl
```
**5.新建文件：Caddyfile。这是caddy的配置文件。**
```
http://example.com {
    log stdout
    root /root/share
    browse
    proxy /hello 127.0.0.1:10000 {
      websocket
      header_upstream -Origin
    }
}

##把http://example.com 换成自己的域名，/hello 可以默认或者换成自己的
```
**6.新建文件夹：/root/share。这是caddy的根目录。只是创建而已，不要移动过去。**
```
mkdir -p /root/share
```
**7.新建文件：config.json。这是v2ray的配置文件**
```
{
    "log": {
        "loglevel": "info"
    },
    "inbounds": [
        {
            "protocol": "vmess",
            "port": 10000,
            "listen": "127.0.0.1",
            "settings": {
                "clients": [
                    {
                        "alterId": 64,
                        "id": "d80d1881-a00f-7153-1740-0ccff5b916ca"
                    }
                ]
            },
            "streamSettings": {
                "network": "ws",
                "wsSettings": {
                    "path": "/hello"
                }
            }
        }
    ],
    "outbounds": [
        {
            "tag": "direct",
            "protocol": "freedom"
        }
    ]
}


##自行更改ID和"/hello"  "port": 10000, 可自己更改
```
**8.新建文件：start.sh。这是启动脚本。总之就是在后台运行./v2ray和./caddy**
```
#!/bin/bash

killall caddy v2ray 2>/dev/null

./caddy > caddy.log 2>&1 &
./v2ray > v2ray.log 2>&1 &
sleep 5
[ -z "$(pidof caddy)" ] || echo "caddy started"
[ -z "$(pidof v2ray)" ] || echo "v2ray started"
```
**9.运行```bash start.sh```即可启动**
输出caddy started和v2ray started表示成功，否则请看caddy.log和v2ray.log的报错内容。


## 二、纯IPV6服务器安装宝塔面板

**1.首先修改DNS，输入以下命令即可。这是为了能访问v4网址。**
```
echo -e "nameserver 2001:67c:2b0::4 \nnameserver 2001:67c:2b0::6" > /etc/resolv.conf
```

**2.安装依赖，如```Curl```等等，如已安装可忽略此步**

**3.安装带 IPv6 的宝塔，执行命令。**
```
curl -sSO http://download.bt.cn/install/new_install.sh && bash new_install.sh
```
**4.管理宝塔面板。**
```
bt
```
**5.在```CF```AAAA记录到小鸡ip**

然后访问```AAAA记录地址:宝塔端口号``` 例如：```eu.xxx.com:8080```就是你小鸡的宝塔面板了



##[纯IPV6增加IPV4](https://github.com/veip007/IPV6/blob/master/Add-ipv4.md)
