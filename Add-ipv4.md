### 一、安装wireguard-go
#### 编译安装
可能需要的依赖：```apt install -y wget git make```
##### 1.安装golang1.16
```
wget https://golang.org/dl/go1.16.1.linux-amd64.tar.gz
tar xvf go1.16.1.linux-amd64.tar.gz -C /usr/local
cat <<EOF >> /etc/profile
#golang env config
export GO111MODULE=on
export GOROOT=/usr/local/go 
export GOPATH=~/gopath
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
EOF
source /etc/profile
```
##### 2.拉取代码并编译
```
git clone https://git.zx2c4.com/wireguard-go.git
git checkout 0.0.20201118
cd wireguard-go
make
mv wireguard-go /usr/local/sbin
```
#### 下载编译好的二进制文件安装
需要的依赖：```apt install -y wget```
```
wget https://github.com/peng4740/wireguard-go-builder/releases/download/0.0.20201118/wireguard-go-linux-amd64.tar.gz
tar zxf wireguard-go-linux-amd64.tar.gz
mv wireguard-go /usr/local/sbin
rm -f wireguard-go-linux-amd64.tar.gz
```
### 二、安装wgcf
##### 安装
```
wget https://github.com/ViRb3/wgcf/releases/download/v2.2.2/wgcf_2.2.2_linux_amd64 -O /usr/local/sbin/wgcf
chmod +x /usr/local/sbin/wgcf
```
### 三、用wgcf生成配置
```
echo|wgcf register
wgcf generate
sed -i '/\:\:\/0/d' wgcf-profile.conf 
mkdir -p /etc/wireguard
cp -f wgcf-profile.conf /etc/wireguard/wgcf.conf
```
### 安装wireguard-tools
依赖：```apt install -y wireguard-tools```

如果提示找不到包安装失败了可能是没有epel源，先安装:```apt install epel-release -y```

Debian:
```
echo "deb http://deb.debian.org/debian/ unstable main" > /etc/apt/sources.list.d/unstable.list
printf 'Package: *\nPin: release a=unstable\nPin-Priority: 150\n' > /etc/apt/preferences.d/limit-unstable
apt update
apt install -y wireguard
```
Ubuntu：
```apt install -y wireguard```

### 四、启动
```wg-quick up wgcf```
### 五、配置开机自启(务必要运行测试过后再配置)
```systemctl enable wg-quick@wgcf```
