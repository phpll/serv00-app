## 前言
前段时间发布了一篇关于serv00部署vless节点的文章，但是很多朋友在使用过后反馈说效果不是很好，会出各种问题，比如连接不上，掉线等等…，最近在测试，发现通过xray搭建的节点可以稳定运行，保持节点的连接，趁着今天有点时间，那今天我们就来记录一下，serv00部署xray搭建多协议节点并保持稳定在线的过程

### 安装
登录ssh连接终端，cd到你想要安装的xray的文件夹，然后执行以下命令下载xray压缩包
```
wget -O 'xray.zip' 'https://raw.githubusercontent.com/phpll/serv00-app/main/xray/xray.zip'
```
执行以下指令解压
```
unzip xray.zip
```
执行以下指令赋予xray权限
```
chmod +x xray
```
### 配置
登录网页管理面板>新建两个端口，然后打开网页版的文件管理器，进入你安装好xray的文件夹，找到config.json文件打开，找到以下内容，并修改为你自己的信息
```
  "inbounds": [{
    "port": 55555, # 改成你新建端口的其中一个
    "listen": "0.0.0.0",
    "tag": "vless-inbound",
    "protocol": "vless", # vless协议
    "settings": {
      "auth": "noauth",
      "udp": false,
      "ip": "0.0.0.0",
      "clients": [{
        "id": "f91c2710-b50c-4ccd-83cf-dd5e1b13eabf" # 修改成你自己的uuid
      }],
---------------
   {
    "port": 44444, # 改成你新建端口的另外一个
    "listen": "0.0.0.0",
    "protocol": "socks", # socks协议
    "settings": {
      "auth": "password",
      "accounts": [
        {
          "user": "user", # 这个是用户名，可改可不改
          "pass": "admin" # 这个是密码，可改可不改
        }
      ],
      "udp": false,
      "ip": "0.0.0.0"
    }
  }],

```
可以点击这里生成一个随机的uuid

修改好后点击保存，回到ssh连接终端

### 运行测试
回到ssh终端后执行以下命令运行xray
```
./xray run
```
如果操作没问题的话，此时xray已经在运行了

打开v2ray客户端，新添加一个vless协议的服务器

在v2ray客户端填写服务器信息

备注: 你可以填写任意的备注名称，例如 ：serv00-vless

地址: 你服务器的 IP 地址或域名

端口: 你自己配置的端口号，比如：44444

用户ID (UUID): f91c2710-b50c-4ccd-83cf-dd5e1b13eabf

加密方式: none

传输协议: WS

路径: /

其他没有提到的留空默认

点击确定，然后测试一下真连接延迟，有显示数值就表示通了，可以正常使用了

再新添加一个socks协议的服务器

在v2ray客户端填写服务器信息

备注: 你可以填写任意的备注名称，例如：serv00-socks

地址: 你服务器的 IP 地址或域名

端口: 你自己配置的端口号，比如：55555

用户名：你自己在配置文件中填写的用户名。比如默认的：user

密码：你自己在配置文件中填写的密码。比如默认的：password

其他没有提到的留空默认

点击确定，然后测试一下真连接延迟，有显示数值就表示通了，可以正常使用了

### 后台运行
可以通过screen新建会话让xray保持在后台运行，执行操作，请将下面的目录修改成你自己的实际目录
```
screen -S xray /home/xxx/domains/xxx.serv00.net/xray/xray run
```
执行后会新建一个screen会话运行xray

可以在当前会话按下 Ctrl + A，然后按D，这会让你从当前 screen 会话中脱离，但 xray 程序仍会在后台运行。

如果你需要重新连接到这个 screen 会话，可以使用以下命令：
```
screen -r xray
```
在网页管理面板进入Cron jobs菜单，点击Add cron job，新建一个脚本，然后在Command框中输入screen -S xray /home/xxx/domains/xxx.serv00.net/xray/xray run，然后点击Add，这样，当服务器重启的时候就会自动执行操作启动xray应用了

参考
YouTube视频演示地址：https://youtu.be/GNDaEy13LZE
