## 前言
最近有很多朋友们反馈说serv00的服务器国内基本是访问不了的，要不就是能访问，但是速度慢，那有没有什么其他方法可以解决这个问题呢？答案是：肯定有的，那么我们今天就来记录一下serv00系列的第五期，如何通过cloudflare提供的免费服务来提升serv00节点的网络速度。

### 前置条件
拥有一个serv00的服务器并且搭建了节点服务，还没有的朋友请点击这里

拥有cloudflare账号，并且将自有域名托管至cloudflare，还没有的朋友请点击这里

### 配置DNS
当你满足前置条件后就可以进行接下来的操作了

在v2ray客户端选中你在serv00搭建的节点，点击右键，选择克隆所选服务器，新增一个节点信息

查看你的节点信息地址这一栏目，保存下来备用，比如：ztest.serv00.net

查看你的节点信息端口这一栏目，保存下来备用，比如：58623

进入cloudflare，管理面板>网站>DNS>记录>添加记录>参考以下信息填写后保存

类型：CNAME

名称：填写一个自己喜欢的，比如：cdn

目标：刚刚在地址栏目复制下来的地址，比如：ztest.serv00.net

代理状态：√

### 配置规则
完成上面的操作后，点击右边菜单栏的规则，点击Origin Rules，点击创建规则>参考以下信息填写后部署

规则名称：填一个自己喜欢的，比如：serv00-cdn

当传入请求匹配时：自定义筛选表达式

字段：主机名

运算符：等于

值：刚刚配置dns记录中名称一栏中填写的加主域名，比如：cdn.zzzwb.com，其中zzzwb.com是你的主域名

目标端口：重写到...，框框中输入刚刚保存下来的端口号。比如：58623

配置CDN
回到v2ray客户端，选中刚刚克隆下来的节点，双击编辑节点，参考以下信息填写后保存

地址：将原来的地址修改为cloudflare的地址，比如：原：ztest.serv00.net，改为：cdn.zzzwb.com

端口：将原来的端口修改为443，比如：原：58623，改为：443

伪装域名：填写跟地址栏的信息一样，比如：cdn.zzzwb.com

传输层安全：选择tls

SNI：填写跟地址栏的信息一样，比如：cdn.zzzwb.com

跳过证书验证：false

修改并保存后选中节点信息，单击右键选择真连接延迟测试一下，如果没有显示-1，那么就说明你已经成功通过cloudflare提供的免费cdn服务来提升serv00节点的网络速度了

优选ip
很多时候cloudflare默认分配的cdn节点ip的加速效果可能并不明显，那么这个时候很多老手就会通过自己手动优选的方式去筛选出最适合自己网络的优选ip了，关于如何筛选最适合自己网络的优质ip，网上一大把，我这边就不再细述了哈

以上就是本次记录的所有内容，如果这篇文章对你有帮助还请多多支持哈

参考
YouTube视频演示地址：https://youtu.be/JWo0RQljN9Q
