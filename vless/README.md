## 前言
昨天发布了一篇关于serv00部署alist的详细记录，很多朋友们反馈按照步骤来都成功部署用上了，趁今天有时间，记录一下sev00部署vless的详细记录，为了方便大家安装，我写了一个一键安装脚本，可以省去一下操作

新建端口
在后台管理面板新建一个端口，进入网页管理面板点击左侧的Port reservation菜单，点击Add port，建议选择Random，新建一个随机端口，点击Add创建，比如新建的端口号是45476

如果需要使用自己的域名的话，点击WWW Websites-点击Add website，新建一个自定义网站

使用自定义域名需要将你的域名添加A记录到serv00的服务器ip，比如128.204.223.98

也可用系统分配的二级域名，比如xxx.serv00.net，本文记录的操作以系统分配的二级域名为例


以下的操作中所有指令目录中的xxx请修改成你自己的

### 登录SSH
执行操作，cd到你的网站名字的文件夹，比如
```
cd domains/xxx.serv00.net/
```
复制执行下面的一键安装脚本
```
wget -O 'vless-run.sh' 'https://raw.githubusercontent.com/phpll/serv00-app/main/vless/vless-run.sh' && chmod +x vless-run.sh && ./vless-run.sh
```
执行完成后会显示安装完成的提示

可以输入ls命令查看当前目录内容

### 修改配置信息
从网页管理面板进入文件管理器file_manager，然后进入到刚刚部署的网站文件夹下的vless目录，比如/domains/xxx.serv00.net/vless/

点击打开app.js文件修改信息
```
const uuid = (process.env.UUID || '264f7731-d14a-43f4-9f53-80e0bb9756dc').replace(/-/g, '');
//将上面的这段配置的uuid修改成你自己的，比如修改成↓
const uuid = (process.env.UUID || '334f7731-d14a-56f4-9f53-80e0bb2256dc').replace(/-/g, '');

const port = process.env.PORT || 36699;
//将上面的这段配置的端口号修改成你自己的，比如修改成↓
const port = process.env.PORT || 5566;
```
修改好后点击Save保存。

### 运行测试
回到SSH客户端，执行操作，请将下面的目录修改成你自己的实际目录
```
node /home/xxx/domains/xxx.serv00.net/vless/app.js
```
运行后会显示你的端口信息，比如：listen: 7060

### 配置v2ray
在v2ray客户端填写服务器信息

备注: 你可以填写任意的备注名称，例如 serv00-vless。
地址: 你服务器的 IP 地址或域名
端口: 3669
用户ID (UUID): 264f7731-d14a-43f4-9f53-80e0bb9756dc
加密方式: none
传输协议: WS
路径: /

其他没有提到的默认留空

点击确定，然后测试一下真连接延迟，有显示数值就表示通了，可以正常使用了

后台运行
可以通过screen新建会话让vless保持在后台运行，执行操作，请将下面的目录修改成你自己的实际目录
```
screen -S vless node /home/xxx/domains/xxx.serv00.net/vless/app.js
```
执行后会新建一个screen会话运行vless

可以在当前会话按下 Ctrl + A，然后按D，这会让你从当前 screen 会话中脱离，但 vless 程序仍会在后台运行。

如果你需要重新连接到这个 screen 会话，可以使用以下命令：
```
screen -r vless
```
在网页管理面板进入Cron jobs菜单，点击Add cron job，新建一个脚本，然后在Command框中输入screen -S vless node /home/xxx/domains/xxx.serv00.net/vless/app.js，然后点击Add，这样，当服务器重启的时候就会自动执行操作启动vless应用了

参考
YouTube视频演示地址：https://www.youtube.com/watch?v=uyhqkn4lVgc

B站视频演示地址：https://www.bilibili.com/video/BV1UhhQeaEsx/

