## 前言
最近发现了一个最新的hysteria2协议节点，于是我就在serv00上部署用上了，经过测试，没想到这速度直接起飞，就算是晚高峰油管4K也是秒开，那今天就来记录一下serv00如何通过sing-box一次性部署hysteria2，vless，tuic，三种协议节点，serv00也能带你体验高速冲浪。

### 安装
登录ssh连接终端，cd到你想要安装的sing-box的文件夹，然后执行以下命令下载sing-box压缩包
```
wget -O 's-box.zip' 'https://raw.githubusercontent.com/phpll/serv00-app/main/sing-box/s-box.zip'
```
执行以下指令解压
```
unzip s-box.zip
```
执行以下指令赋予xray权限
```
chmod +x web
```
### 配置
登录网页管理面板>新建两个UDP端口，一个TCP端口，然后打开网页版的文件管理器，进入你安装好sing-box的文件夹，找到config.json文件打开，找到以下内容，并修改为你自己的信息
```
   "inbounds": [
   {
      "tag": "hysteria-in",
      "type": "hysteria2",
      "listen": "::",
      "listen_port": 33333,# 修改为你新建的其中一个udp端口
      "users": [
        {
            "password": "bc97f674-c578-4940-9234-0a1da46041b9"# 修改为你自己的uuid
        }
    ],
    "masquerade": "https://bing.com",
    "tls": {
        "enabled": true,
        "alpn": [
            "h3"
        ],
        "certificate_path": "cert.pem",
        "key_path": "private.key"
       }
   },
   {
       "tag": "vless-reality-vesion",
       "type": "vless",
       "listen": "::",
       "listen_port": 44444,# 修改为你自己新建的tcp端口
       "users": [
           {
             "uuid": "bc97f674-c578-4940-9234-0a1da46041b9",# 修改为你自己的uuid
             "flow": "xtls-rprx-vision"
           }
       ],
       "tls": {
           "enabled": true,
           "server_name": "www.ups.com",
           "reality": {
               "enabled": true,
               "handshake": {
                   "server": "www.ups.com",
                   "server_port": 443
               },
               "private_key": "sFfFeg0jT8e0lWShEserKYernuR66yldmpV1EMPbHkA",
               "short_id": [
                 ""
               ]
           }
       }
   },
   {
     "tag": "tuic-in",
     "type": "tuic",
     "listen": "::",
     "listen_port": 55555,# 修改为你新建的另外一个udp端口
     "users": [
       {
         "uuid": "bc97f674-c578-4940-9234-0a1da46041b9",# 修改为你自己的uuid
         "password": "password123"# 密码，可改可不改
       }
     ],
     "congestion_control": "bbr",
     "tls": {
       "enabled": true,
       "alpn": [
         "h3"
       ],
       "certificate_path": "cert.pem",
       "key_path": "private.key"
     }
   }

],
```


修改好后点击保存，回到ssh连接终端

### 运行测试
回到ssh终端后执行以下命令获取本机IP地址
```
curl ifconfig.me 
```
执行以下命令运行sing-box
```
./web run
```
如果操作没问题的话，此时sing-box已经在运行了

复制以下节点信息并修改成你自己的，然后复制，打开v2ray客户端粘贴即可使用

hysteria2协议节点
```
hysteria2://bc97f674-c578-4940-9234-0a1da46041b9@1.1.1.1:33333/?sni=www.bing.com&alpn=h3&insecure=1#tg%E6%8A%80%E6%9C%AF%E4%BA%A4%E6%B5%81%E7%BE%A4%EF%BC%9Ahttps%3A%2F%2Ft.me%2Fzzzjsjl
```
vless协议节点
```
vless://bc97f674-c578-4940-9234-0a1da46041b9@1.1.1.1:44444?encryption=none&flow=xtls-rprx-vision&security=reality&sni=www.ups.com&fp=chrome&pbk=SxBMcWxdxYBAh_IUSsiCDk6UHIf1NA1O8hUZ2hbRTFE&type=tcp&headerType=none#tg%E6%8A%80%E6%9C%AF%E4%BA%A4%E6%B5%81%E7%BE%A4%EF%BC%9Ahttps%3A%2F%2Ft.me%2Fzzzjsjl
```
tuic协议节点
```
tuic://bc97f674-c578-4940-9234-0a1da46041b9:password123@1.1.1.1:55555?sni=www.bing.com&alpn=h3&congestion_control=bbr#tg%E6%8A%80%E6%9C%AF%E4%BA%A4%E6%B5%81%E7%BE%A4%EF%BC%9Ahttps%3A%2F%2Ft.me%2Fzzzjsjl
```
### 后台运行
可以通过screen新建会话让sing-box保持在后台运行，执行操作，请将下面的目录修改成你自己的实际目录
```
screen -S box /home/xxx/domains/xxx.serv00.net/box/web run
```
执行后会新建一个screen会话运行sing-box

可以在当前会话按下 Ctrl + A，然后按D，这会让你从当前 screen 会话中脱离，但 sing-box 程序仍会在后台运行。

如果你需要重新连接到这个 screen 会话，可以使用以下命令：
```
screen -r box
```
在网页管理面板进入Cron jobs菜单，点击Add cron job，新建一个脚本，然后在Command框中输入screen -S box /home/xxx/domains/xxx.serv00.net/box/web run，然后点击Add，这样，当服务器重启的时候就会自动执行操作启动sing-box应用了
