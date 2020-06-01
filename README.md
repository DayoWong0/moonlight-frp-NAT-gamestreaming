# frp-Moonlight-Docker-NAT
测试Demo frp--内网穿透 Moonlight远程游戏串流 NAT大宽带 Docker端口复用(貌似不该这么说)
##  参考
- [frps一键安装脚本](https://github.com/MvsCode/frps-onekey)
- [Moonlight-Android官方版本](https://github.com/moonlight-stream/moonlight-android)
- [Moonlight-Android本人修改版](https://github.com/chengziqaq/moonlight-android)
- [导入别人的Android studio项目]()
 ## 导入官方的项目
 - 下载android studio 4.0 然后git clone项目,android studio就自己下载开发环境了
参考官方README.md

端口占用查看
```python
import time
import os
while(1):
        os.system("netstat -nltp | grep 12023")
        time.sleep(1)
        os.system("clear")
```

## 测试各个端口的作用
- 1.修改app/src/main/java/com/limelight/nvstream/http/NvHTTP.java
```java
public static final int HTTP_PORT = 47989;
```
47989 --> 0

