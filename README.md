# moonlight-frp-NAT-gamestreaming
frp--内网穿透 Moonlight远程游戏串流 NAT大宽带
##  参考
- [frps一键安装脚本](https://github.com/MvsCode/frps-onekey)
- [moonlight-Android官方版本](https://github.com/moonlight-stream/moonlight-android)
 ## 导入官方的项目--开发环境和官方一致
 [参考官方README](https://github.com/moonlight-stream/moonlight-android)  
 当前版本:v9.5.1
 # Building
- Install Android Studio and the Android NDK
- Run ‘git submodule update --init --recursive’ from within moonlight-android/
- In moonlight-android/, create a file called ‘local.properties’. Add an ‘ndk.dir=’ property to the local.properties file and set it equal to your NDK directory.
- Build the APK using Android Studio or gradle
 
 - 下载android studio 4.0 然后Get from version control,android studio就自己下载并配置好开发环境了(可能需要手动下载NDK)
 
 # frps安装--NAT主机上的操作  
[frp安装一键脚本](https://github.com/MvsCode/frps-onekey),这里我们选用国内NAT主机  
所以用
```shell
wget https://code.aliyun.com/MvsCode/frps-onekey/raw/master/install-frps.sh -O ./install-frps.sh
chmod 700 ./install-frps.sh
./install-frps.sh install
```
一键脚本需要注意的地方:
- frps端口号:要你的NAT机器能用的端口
- token,ip,port保存下来 等会要用,其他默认即可

复制frps配置信息,ip port token下面会用到
 
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
 
## 修改端口
源码修改端口的位置  
### java代码中的修改
- 1.新建文件,方便以后只需要在这个文件里修改端口
文件路径和名称:src/main/java/com/limelight/preferences/CustomizePort.java  
粘贴下面的代码  
```java
package com.limelight.preferences;

public class CustomizePort {
    public static int tcp_47984 = 30000;
    public static int tcp_47989 = 30001;
    //远程唤醒端口 默认的7和9就硬编码了
    public static int udp_47998 = 40000;
    public static int udp_47999 = 40001;
    public static int udp_48000 = 40002;
    public static int udp_48010 = 40003;
    public static int udp_48002 = 48002
    
}
```
对应修改就ok了,依然是上面的映射关系.    
有没有注意到,多了一条    
    public static int udp_48002 = 48002  
这个端口以前有,不记得哪个版本开始被移除了,保持原样就好,改不改不影响.  

以下的修改都可以复制粘贴  
- 2.找到src/main/java/com/limelight/nvstream/http/NvHTTP.java
```java
public static final int HTTPS_PORT = 47984;  
```
将47984改为CustomizePort.tcp_47984
```java
public static final int HTTPS_PORT = CustomizePort.tcp_47984;  

```
将
```java
public static final int HTTP_PORT = 47989;
```
改为
```java
public static final int HTTP_PORT = CustomizePort.tcp_47989;
```  

打开src/main/java/com/limelight/nvstream/wol/WakeOnLanSender.java  
将
```java
    private static final int[] PORTS_TO_TRY = new int[] {
        7, 9, // Standard WOL ports
        47998, 47999, 48000, 48002, 48010 // Ports opened by GFE
    };
```
改为:
```java
    private static final int[] PORTS_TO_TRY = new int[] {
        7, 9, // Standard WOL ports //48010_tcp not controled at here
            CustomizePort.udp_47998, CustomizePort.udp_47999,
            CustomizePort.udp_48000, CustomizePort.udp_48002,
            CustomizePort.udp_48010 // Ports opened by GFE
    };
```

### Ｃ语言代码中的修改
在目录：src/main/jni/moonlight-core/moonlight-common-c/src下新建文件　　　　
CustomizePort.h　　
粘贴以下内容　　
```C
#define tcp_48010 30002
#define udp_47998 40000
#define udp_47999 40001
#define udp_48000 40002
#define udp_48010 40003
```
将3000 40001等修改为前面你自定义的端口  
#### 打开RtspConnection.c
在第一行加入(以下的对C文件的操作都要加)
```C
#include "CustomizePort.h"
```
- 
ctrl + f 搜索48010  
将搜到的除了下面语句中的48010替换为tcp_48010
```java
Limelog("RTSP: Failed to connect to UDP port 48010\n");
```
把上面这一句改为
```c
Limelog("RTSP: Failed to connect to UDP port %d\n", udp_48010);
```
### 打开VideoStream.c
第一行粘贴头文件
```C
#include "CustomizePort.h"
```
将  
```C
#define RTP_PORT 47998
```
注释掉
粘贴以下代码  
```C
static  int RTP_PORT = udp_47998;
```
打开SdpGenerator.c  
搜索47998
将
```c
AppVersionQuad[0] < 4 ? 47996 : 47998);
```
改为  
```c
AppVersionQuad[0] < 4 ? 47996 : udp_47998);//修改47998-udp
```
打开ControlStream.c  
搜索47999   
```c
enet_address_set_port(&address, 47999);
```
改为
```c
enet_address_set_port(&address, udp_47999);
```
将  
```c
Limelog("Failed to connect to UDP port 47999\n");
```
改为    
```c
Limelog("Failed to connect to UDP port %d\n",udp_47999);
```
打开src/main/jni/moonlight-core/moonlight-common-c/src/AudioStream.c  
加入头文件,将  
```c
#define RTP_PORT 48000
```
注释掉
粘贴下面的代码
```c
static int RTP_PORT = udp_48000;
```
## 测试运行
运行frpc  
手动输入NAT主机ip地址    
如以上操作没问题,就可以正常串流了   

