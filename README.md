# 操作方法
## NAT主机端口映射
- TCP端口:共4个.一个frps端口,三个GFE需要的端口(可自行添加一个3306远程桌面映射)  
- UDP端口:共4个 都是GEF用的端口  
至少需要8个端口  
## [frps安装](https://github.com/MvsCode/frps-onekey)
记录ip 端口 token 下面要用  
## frpc配置
- 下载[frp-win-amd64](https://github.com/fatedier/frp/releases)  
- 下载frpc.ini[模板](https://raw.githubusercontent.com/chengziqaq/moonlight-frp-NAT-gamestreaming/master/frp/frpc.ini)    
修改服务器配置,填入三个服务器参数,修改remote_port  
## 用android studio导入项目--参考moonlight-android README  
- android官网下载android studio,NDK,因为核心是用C语言写的  
- 下载git  
- fork [官方项目](https://github.com/moonlight-stream/moonlight-android)到自己仓库  
- git clone到本地  
android studio会自动下载配置好环境      
#### 下载本仓库解压复制(覆盖原文件)code文件夹下的文件到android项目对应位置
[~~这里有啰嗦版本,可以看到哪些地方需要修改,比较麻烦~~](https://github.com/chengziqaq/moonlight-frp-NAT-gamestreaming/blob/master/README-old.md#%E4%BF%AE%E6%94%B9%E7%AB%AF%E5%8F%A3--%E4%BB%A5%E4%B8%8B%E6%93%8D%E4%BD%9C%E5%9C%A8android-studio%E4%B8%AD%E8%BF%9B%E8%A1%8C)
- java文件    
src/main/java/com/limelight/preferences/CustomizePort.java      
src/main/java/com/limelight/nvstream/http/NvHTTP.java      
src/main/java/com/limelight/nvstream/wol/WakeOnLanSender.java        
- C语言文件(以.c和.h结尾)     
全部复制(覆盖原文件)src/main/jni/moonlight-core/moonlight-common-c/src目录下     
## 修改端口  
打开文件CustomizePort.java和CustomizePort.h     
修改端口,这些端口要和frpc.ini中的remote_port对应    
例如:frpc.ini配置  
```
[nvidia-stream-tcp-1]
type = tcp
local_ip = 127.0.0.1
local_port = 47984
remote_port = 30000
```
配置解读为:协议类型TCP 本地端口47984 远程端口30000    
则CustomizePort.java中对应改为     
public static int tcp_47984 = 30000;     
即    
public static int 协议类型_本地端口号 = 远程端口号;    
CustomizePort.h中      
#define 协议类型_本地端口号 远程端口号     
注:你需要修改的是frpc.ini中的remote_port,local_port不能修改  
java和c代码中的只需要修改代码最右边的端口号    
## 测试运行    
- 运行frpc    
有个bat脚本,[下载](https://github.com/chengziqaq/moonlight-frp-NAT-gamestreaming/raw/master/frp/frpc.bat)后放在frpc同目录下   
- 连上手机,打开USB调试模式,运行      
- moonlight客户端里输入frps的IP地址即可    
### 环境--参考moonlight-android README
- Android Studio 4.0 
- ndk 21.0.6113669
- [moonlight-android](https://github.com/moonlight-stream/moonlight-android/tree/581327dc8e331b50ca644936b1225dbf24b04c0c) 当前版本v9.5.1   
本文编辑时间6月2号左右  
- [moonlight-common-c](https://github.com/moonlight-stream/moonlight-common-c/tree/bea625a13dd4f9604e5583744cf7c8e36064f19d)
### 注意事项
- 关闭手机代理软件
- 确认远程端口正确开放
阿里云 腾讯云管理页面还有防火墙设置 未正确开启不能正常使用  
- 建议使用国内NAT主机   
优势:带宽大 价格便宜   
缺点:带宽共享 主机商要求不能长时间占用带宽  
- 其他串流问题可参考[moonlight-doc-wiki](https://github.com/moonlight-stream/moonlight-docs/wiki)
### 遇到的问题
- 测试串流GTA5 刚开始串流电脑就黑屏了 手机也没有画面
重启后,开机时转完windows图标就黑屏了,过一会再次重启,重启两次就进入windows的系统修复了.  
进BIOS设置核显为第一显卡,可以进入系统,顺手把独显驱动和GTA5卸载了,GTA5可能也有问题,在steam下载后,gta5不能直接启动,校验了文件后才正常启动,并且GEF Experience不能主动扫描到这游戏(以前都可以的)   
可能是显卡供电不足,之前我安装黑苹果,把显卡的供电线拔过.可能重新插上后接触不良,串流游戏时显卡占用率比平时高  

### 自动启动
目的:随时连接到电脑   
- frpc中填的服务器ip地址换成域名   
若更换服务器ip只需要重新解析   
- 电脑设置远程唤醒    
可在路由器端花生壳内网穿透(免费版就够用了)和frpc  
一般路由器都带有唤醒功能  
- 设置frpc开机自启  
若在路由器安装frpc则需要给电脑设置静态ip    
电脑端使用frpc设置开机自启   

### 直接修改源码 将自定义端口改为软件设置选项
有这个打算 并且尝试过 还没成功(太菜了)

### 参考
- [frp](https://github.com/fatedier/frp)  
- [Frps服务端一键配置脚本](https://github.com/MvsCode/frps-onekey)  
- [moonlight-android](https://github.com/moonlight-stream/moonlight-android)  
- [moonlight-common-c](https://github.com/moonlight-stream/moonlight-common-c)  
### 其他方法
- 使用Openvpn代替上面的所有操作   
我测试过 在手机上能成功连上服务器 在电脑上连接提示 tls shake hands 出错 暂时没找到解决办法   
