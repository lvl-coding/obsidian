1. 申请虚拟机@边文斌

2. 配置：

3. 250G系统盘
4. 15G内存
5. ip:192.168.10.89

6. ubuntu指令：

7. 网卡文件://etc/netplan/00-installer-config.yaml

8. ![重要](file:///C:/Users/lyl/AppData/Local/Temp/msohtmlclip1/01/clip_image001.png) 问题记录:ens3为外网ip，ens4和ens5ip和路由无效，且无效的路由会影响后续访问管理页面端口，需要删除

9. 修改网卡配置后生效命令:netplan apply

10. 安装docker

11. [【Docker】在 Ubuntu 上安装 Docker 的详细指南_ubuntu安装docker-CSDN博客](https://blog.csdn.net/Stromboli/article/details/142486565)

12. 连接docker镜像库，下载docker镜像

13. [sftp-go/docker/README.md at main · zesle/sftp-go](https://github.com/zesle/sftp-go/blob/main/docker/README.md)
14. docker run --name some-sftpgo \

    -p 8080:8080 \

    -p 2022:2022 \

    -p 10080:10080 \

    -e SFTPGO_WEBDAVD__BINDINGS__0__PORT=10080 \

    -d "drakkan/sftpgo:latest"

1. 浏览器中访问虚拟机ip:8080

2. 创建webdev用户和密码

3. [WebDav多平台挂载详细方法（Linux+Windows+IOS）_davfs2-CSDN博客](https://blog.csdn.net/m0_64405299/article/details/135981250)

4. windows10默认不支持http挂载webdev，需修改注册表后重启服务[windows10挂载webdav - 晴云孤魂 - 博客园](https://www.cnblogs.com/cnhack/articles/17101960.html)

5. 尝试上传下载
![[Pasted image 20250319161146.png]]
![[Pasted image 20250319161140.png]]