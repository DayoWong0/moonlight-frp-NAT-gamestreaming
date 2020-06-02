# moonlight-frp-NAT-gamestreaming
frp+moonlight+NAT 花少的钱体验高画质的远程游戏串流
# moonlight-frp-NAT-gamestreaming
frp--内网穿透 Moonlight远程游戏串流 NAT大宽带
##  参考
- [frps一键安装脚本](https://github.com/MvsCode/frps-onekey)
- [moonlight-Android官方版本](https://github.com/moonlight-stream/moonlight-android)

# NAT主机端口映射
待补充
 # frps安装--NAT主机上的操作  
[frp安装一键脚本](https://github.com/MvsCode/frps-onekey),这里我们选用国内NAT主机  
所以用
```shell
wget https://code.aliyun.com/MvsCode/frps-onekey/raw/master/install-frps.sh -O ./install-frps.sh
chmod 700 ./install-frps.sh
./install-frps.sh install
```
一键脚本需要注意的地方:
- frp下载镜像:国内选阿里云
- frps端口号:要你的NAT机器能用的端口
- token,ip,port保存下来 等会要用,其他默认即可

复制frps配置信息,ip port token下面会用到,其他的不用管
```
============== Check your input ==============
You Server IP      : 你的主机ip
Bind port          : 你选择的端口号
kcp support        : true
vhost http port    : 80
vhost https port   : 443
Dashboard port     : 6443
Dashboard user     : admin
Dashboard password : C4yqSsaX
token              : 你的token
subdomain_host     : 你的主机ip
tcp_mux            : true
Max Pool count     : 50
Log level          : info
Log max days       : 3
Log file           : enable
==============================================
```
 
 # [参考wiki需要开放的端口以及协议](https://github.com/moonlight-stream/moonlight-docs/wiki/Setup-Guide#manual-port-forwarding-advanced)
 
 # frpc映射--本地电脑的操作
 假设NAT主机端口可以使用:    
 - TCP:30000,30001,30002,  
 - UDP:40000,40001,40002,40003    
 查询上面的官方wiki需要开放的端口为:    
- TCP 47984, 47989, 48010  
- UDP 47998, 47999, 48000, 48010  
假设对应端口一一对应  
对应的映射关系为:  
local_port)    remote_port  
 30000         47984    
 30001         47989  
 以此类推  
 则frp配置文件为:  
 ```
 [common]
server_addr = frp服务器ip
server_port = frp服务器端口
token = frp验证token

[nvidia-stream-tcp-1]
type = tcp
local_ip = 127.0.0.1
local_port = 47984
remote_port = 30000

[nvidia-stream-tcp-2]
type = tcp
local_ip = 127.0.0.1
local_port = 47989
remote_port = 30001

[nvidia-stream-tcp-3]
type = tcp
local_ip = 127.0.0.1
local_port = 48010
remote_port = 30002

[nvidia-stream-udp-1]
type = udp
local_ip = 127.0.0.1
local_port = 47998
remote_port = 40000

[nvidia-stream-udp-2]
type = udp
local_ip = 127.0.0.1
local_port = 47999
remote_port = 40001

[nvidia-stream-udp-3]
type = udp
local_ip = 127.0.0.1
local_port = 48000
remote_port = 40002

[nvidia-stream-udp-4]
type = udp
local_ip = 127.0.0.1
local_port = 48010
remote_port = 40003
 ```
 测试下frpc是否能正常连接frps
 
  ## 导入官方的项目--开发环境和官方一致
 [参考官方README的Building](https://github.com/moonlight-stream/moonlight-android#building)  
 当前版本:v9.5.1
 - 下载android studio 4.0 然后Get from version control,android studio就自己下载并配置好开发环境了(可能需要手动下载NDK,可能还要下载git)
 
## 修改端口--以下操作在android studio中进行
源码修改端口的位置  

