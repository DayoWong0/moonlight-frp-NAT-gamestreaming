# 操作方法
## NAT主机端口映射
- TCP端口:共4个.一个frps端口,三个GFE Experience需要的端口(可自行添加一个3306远程桌面映射)
- UDP端口:共4个 都是GEF Expertise用的端口
至少需要8个端口
## [frps安装](https://github.com/MvsCode/frps-onekey)
记录下ip 端口 token 下面要用
## frpc配置
- 下载[frp-win-amd64](https://github.com/fatedier/frp/releases)
- 下载frpc.ini[模板](https://raw.githubusercontent.com/chengziqaq/moonlight-frp-NAT-gamestreaming/master/frp/frpc.ini)  
修改服务器的选项,填入三个服务器参数,修改remote_port
## 用android studio导入项目
- android官网下载android studio,NDK,因为核心是用C语言写的
- 下载git
- fork [官方项目](https://github.com/moonlight-stream/moonlight-android)到自己仓库
- git clone到本地
android studio会自动下载配置好环境    
#### 下载本仓库解压复制code文件夹下的文件到android项目对应位置
- java文件
src/main/java/com/limelight/preferences/CustomizePort.java  
src/main/java/com/limelight/nvstream/http/NvHTTP.java  
src/main/java/com/limelight/nvstream/wol/WakeOnLanSender.java    
- C语言文件(以.c和.h结尾制到src/main/jni/moonlight-core/moonlight-common-c/src目录下


