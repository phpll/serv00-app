## 前言
最近很多小伙伴们在我的tg群讨论serv00能不能安装哪吒面板，因为一般自己买的vps都是有root权限的，所以可以使用官方的一键安装脚本，那没有root权限的serv00到底能不能搭建哪吒面板呢，这几天我也在研究，终于，在经过参考了多方面资料并经过测试，serv00是可以搭建哪吒面板的，那今天就来继续serv00系列的第六期，详细讲解一下serv00搭建部署哪吒面板的记录

准备工作
新建2个端口
创建一个域名type选择proxy并绑定其中一个端口
准备Github的OAuth Apps的Client ID和Client secrets
### 安装哪吒
当你准备工作做好了之后就可以进行接下来的操作了，ssh登录服务器

执行以下指令下载哪吒面板的安装包
```
wget https://github.com/bin862324915/serv00-app/releases/download/freebsd/nezha.zip
```
### 解压
```
unzip nezha.zip
```
配置面板
登录网页管理面板>文件管理器，进入哪吒目录下的data目录，打开config.yaml文件

修改以下内容
```
GRPCHost: '[you host[]'
GRPCPort: 
HTTPPort: 
---------------
GRPCHost: '你的域名'
GRPCPort: 创建的另外一个端口
HTTPPort: 填写绑定域名的端口
yaml
Oauth2:
  Admin: [username]
  ClientID: [github clientid]
  ClientSecret: []
  Endpoint: ""
  Type: github
  -------------
  Oauth2:
  Admin: [Github用户名]
  ClientID: [你准备的Client ID]
  ClientSecret: [你准备的Client secrets]
  Endpoint: ""
  Type: github
```
编辑修改后保存，回到ssh客户端，执行以下命令，赋予哪吒权限
```
chmod +x dashboard
```
执行以下命令运行
```
./dashboard
```
浏览器打开你的域名，操作没有问题的话就会显示哪吒面板的页面了

### 配置监控
登录后台管理，新增一个服务器，填写相关信息，新增后在页面复制秘钥保存备用

ssh登录被控端服务器

执行以下指令下载agent安装包
```
wget https://github.com/bin862324915/serv00-app/releases/download/freebsd/nezha-agent.zip
```
解压
```
unzip nezha-agent.zip
```
执行以下命令，赋予权限
```
chmod +x start.sh agent
```
修改start.sh文件内容
```
#!/bin/sh

export TMPDIR=/home/[username[]/nezha-agent/tmp

/home/[username[]/nezha-agent/agent -s [you host]:grcppoet -p [key] -d

EOF
---------
export TMPDIR=这里修改为tmp的实际路径

这里修改为agent的实际路径 -s [这里是哪吒面板的域名]:[这里是哪吒面板GRPCPort设置的端口号] -p [这里填写刚刚新增服务器后保存的秘钥] -d
```
修改后保存，回到ssh客户端执行
```
sh start.sh
```
这时候回到哪吒面板刷新一下，你新增的服务器信息就出来了



B站视频演示地址：https://www.bilibili.com/video/BV1DEeCeJENH/
