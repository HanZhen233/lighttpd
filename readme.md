### 基于NewIP的lighttpd 

#### 一、环境要求
最新版newip内核，ubuntu系统
#### 二、编译安装

1. 依赖相关
```shell
sudo apt-get install  autoconf automake libtool libpcre3 libpcre3-dev  libbz2-dev zlib1g zlib1g-dev
```
2. 编译与安装
```shell
cd lighttpd
./autogen.sh 
./configure 
make #编译
sudo make install # 安装
make uninstall # 卸载 
```

#### 三、配置相关

1. 配置newip及路由

```shell
ifconfig ens33 newip add --level 1-1-1 --address 01-02-cd
route -A ninet add level 1-1-1 address 01-02-ab  dev ens33
```


2. 配置lighttpd.conf文件

```shell
server.document-root = "/home/hanzhen/Projects/www/pages/" #网页根目录
server.port = 8080 # 绑定监听端口号
server.bind = "level 1-1-1 address 01-02-cd" # newip地址
server.use-newip = "enable"   # 使newip生效
server.event-handler = "select" # 目前是选择select IO复用

mimetype.assign = (
  ".html" => "text/html", 
  ".txt" => "text/plain",
  ".jpg" => "image/jpeg",
  ".png" => "image/png" 
)

static-file.exclude-extensions = ( ".fcgi", ".php", ".rb", "~", ".inc" )
index-file.names = ( "index.html" ) # 首页
```
3. 配置文件权限

``` shell
chmod 755 lighttpd.conf
```
#### 四、启动

```shell
lighttpd -D -f lighttpd.conf # 非守护进程启动
lighttpd -f lighttpd.conf # 守护进程启动

```
