## 前言
最近在serv00薅了个服务器，经过一系列的折腾，发现还是有一点可玩性的，本文将记录在serv00服务器上部署alist应用的详细过程

绑定端口
在后台管理面板新建一个端口，比如新建的端口号是45476，点击WWW Websites-点击Add website，新建一个自定义网站，类型选择proxy，端口就是刚刚新建的那个端口45476。

也可用系统分配的二级域名，比如bin001.serv00.net，用系统分配的域名的话需要先删除掉，然后重新添加上，类型选proxy，端口就是刚刚新建的那个端口45476,系统分配的域名不用申请证书，如果是自定义域名的话就到SSL菜单，给自定义域名申请一下证书

### 登录SSH
执行操作，cd到你的网站名字的文件夹，比如
```
cd domains/ztest.serv00.net/
```
新建一个文件夹
```
mkdir alist
```
然后输入ls，可以看到刚刚新建的alist文件夹，然后cd到alist文件夹
```
cd alist
```
一键安装alist脚本
复制下面的代码执行操作
```
wget -O alist-freebsd.sh https://raw.githubusercontent.com/phpll/serv00-app/main/alist/alist-freebsd.sh && sh alist-freebsd.sh
```
执行完成后会弹出对应的提示

### 修改配置信息
先进入网页管理面板MySQL菜单，点击Add database新建数据库，填写好信息，保存备用，下面是数据库创建的示例信息，实际操作中填写为自己的即可
```
Database name：m5022_alist
Username：m5022_alist
Password：Test240706
```
从网页管理面板进入文件管理器file_manager，然后进入到刚刚部署的网站文件夹下的alist目录，比如/home/ztest/domains/ztest.serv00.net/alist/data/

修改config.json文件的配置，只需要找到对应字段，修改对应字段的信息即可，其他配置信息不要删除或修改
```
  "database": {
    "type": "sqlite3",
    "host": "",
    "port": 0,
    "user": "",
    "password": "",
    "name": "",
    "db_file": "data/data.db",
    "table_prefix": "x_",
    "ssl_mode": "",
    "dsn": ""
  },
#将上面的这段配置的数据库信息修改成你自己的，比如修改成↓
  "database": {
    "type": "mysql",
    "host": "mysql6.serv00.com",
    "port": 3306,
    "user": "m5022_alist",
    "password": "Test240706",
    "name": "m5022_alist",
    "db_file": "data/data.db",
    "table_prefix": "x_",
    "ssl_mode": "",
    "dsn": ""
  },

------------

"scheme": {
    "address": "0.0.0.0",
    "http_port": 5244,
    "https_port": -1,
    "force_https": false,
    "cert_file": "",
    "key_file": "",
    "unix_file": "",
    "unix_file_perm": ""
  }
#将上面的这段配置的端口号修改成你自己的，比如修改成↓
  "scheme": {
    "address": "0.0.0.0",
    "http_port": 45476,
    "https_port": -1,
    "force_https": false,
    "cert_file": "",
    "key_file": "",
    "unix_file": "",
    "unix_file_perm": ""
  }
```
修改好后点击Save保存。

### 运行测试
回到SSH客户端，执行操作
```
./alist server
```
首次运行后会生成一个默认的密码，找到password后面的字符串就是默认密码，保存备用

如果忘记密码可以使用以下指令重置密码
```
# 随机生成一个密码

./alist admin random
# 手动设置一个密码,`NEW_PASSWORD`是指你需要设置的密码
./alist admin set NEW_PASSWORD
```
进入浏览器，打开你刚刚部署的网站，比如https://bin001.serv00.net/，输入用户名admin，密码是刚刚生成的那个，至此，整个alist的安装部署就到此结束了,在SSH客户端 按Ctrl + C，结束alist的运行

### 后台运行
可以通过screen新建会话让alist保持在后台运行，执行操作
```
screen -S alist /home/ztest/domains/ztest.serv00.net/alist/alist server
```
执行后会新建一个screen会话运行alist

可以在当前会话按下 Ctrl + A，然后按D，这会让你从当前 screen 会话中脱离，但 alist 程序仍会在后台运行。

如果你需要重新连接到这个 screen 会话，可以使用以下命令：
```
screen -r alist
```
在网页管理面板进入Cron jobs菜单，点击Add cron job，新建一个脚本，然后在Command框中输入screen -S alist /home/ztest/domains/ztest.serv00.net/alist/alist server，然后点击Add，这样，当服务器重启的时候就会执行操作自动启动alist应用了
```
screen -S alist /home/ztest/domains/ztest.serv00.net/alist/alist server
```
参考
alist官方参考文档：https://alist.nn.ci/zh/

YouTube视频演示地址：https://youtu.be/L9f4CmiDr0Y

B站视频演示地址：https://www.bilibili.com/video/BV1JHaueaEbP/

