# nginx-rtmp
开箱即用的Nginx-RTMP服务器

提供`X86_64`、及`arm64`平台，PC和树莓派都能用

基于`alpine:3.14`镜像构建，体积超小，利于使用

# 版本记录
版本号默认跟随 Nginx Stable 分支的版本号，从1.21.1开始创建
- `1.21.1`, `latest`

# Github仓库地址
镜像Github仓库：[https://github.com/7YHong/nginx-rtmp](https://github.com/7YHong/nginx-rtmp)

# 本镜像的用途
`Nginx`是一个高性能的HTTP服务器，本镜像集成了`nginx-rtmp-module`插件，可以配置为直播推流服务器

本镜像适用于`PS4`、`PS5`的直播推流，也可以用作一般的串流服务器来使用

# 使用方法
## 开启服务
- 作为直播推流服务器使用，可通过以下命令启用服务：

```docker run -d --name nginx-rtmp -p 1935:1935 -p 8080:8080 7yhong/nginx-rtmp```

浏览器访问`http://localhost:8080`，显示如下界面：说明服务开启成功
![stat界面](https://i.loli.net/2021/08/03/Z3WGqAxMyXiJ4Rc.png)  
`如果是在路由器、nas或者树莓派上运行，请自行把localhost改成宿主机IP地址`
- 如果不需要面板，可以不映射8080：

```docker run -d --name nginx-rtmp -p 1935:1935 7yhong/nginx-rtmp```

## PS5推流
目前PS5直播功能只能支持Twitch和油罐，因此大体思路是通过劫持Twitch推流到`nginx-rtmp`上以便达成目的
- 添加DNSMasq列表，把Twitch直播地址劫持为你的docker主机地址
- 保存应用路由器设置
- 打开PS5，按分享键，选择从Twitch直播
- 如果成功劫持了推流且模块工作正常，接下来会在网页中显示一段ID

`live_***************`
- 把这段ID复制下来粘贴到以下这个链接中替换live_******的部分注意这个链接的localhost也要替换为你宿主机的IP

`rtmp://localhost:1935/app/live_******`
- 使用OBS加载然后直播就完事了

# 高级设置
当然也可以直接使用Nginx推流
- 前往[Github仓库](https://github.com/7YHong/nginx-rtmp)获取`nginx.conf`文件
- 在`live on;`下面添加一行`直播推流带key的完整地址`即可，例如

`push rtmp://live-push.bilivideo.com/live-bvc/?streamname=[name]&key=[key];`
- 重新设置容器，使用自定义的配置文件即可

```
docker run -d --name nginx-rtmp -p 1935:1935 -p 8080:8080 \
-v nginx.conf:/etc/nginx/nginx.conf 7yhong/nginx-rtmp
```