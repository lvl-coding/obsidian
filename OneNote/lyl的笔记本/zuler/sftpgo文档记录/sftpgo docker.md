环境准备：

在docker内进行部署，因此需要docker服务，且配置可以连通社区docker镜像或自定义docker的镜像源

社区docker镜像tags选择 [https://hub.docker.com/r/drakkan/sftpgo](https://hub.docker.com/r/drakkan/sftpgo)：

- [v2.6.4, v2.6, v2, latest](https://github.com/drakkan/sftpgo/blob/2.6.x/Dockerfile)//提供标准镜像
- [v2.6.4-plugins, v2.6-plugins, v2-plugins, plugins](https://github.com/drakkan/sftpgo/blob/2.6.x/Dockerfile) //*plugins在标准图像的基础上在/usr/local/bin目录下安装了所有社区的插件
- [v2.6.4-alpine, v2.6-alpine, v2-alpine, alpine](https://github.com/drakkan/sftpgo/blob/2.6.x/Dockerfile.alpine)//基于Alpine Linux的镜像
- [v2.6.4-slim, v2.6-slim, v2-slim, slim](https://github.com/drakkan/sftpgo/blob/2.6.x/Dockerfile)//精简镜像，不包含jq/git/rsync
- [v2.6.4-alpine-slim, v2.6-alpine-slim, v2-alpine-slim, alpine-slim](https://github.com/drakkan/sftpgo/blob/2.6.x/Dockerfile.alpine)//基于Alpine Linux的镜像的精简镜像
- [v2.6.4-distroless-slim, v2.6-distroless-slim, v2-distroless-slim, distroless-slim](https://github.com/drakkan/sftpgo/blob/2.6.x/Dockerfile.distroless)
- [edge](https://github.com/drakkan/sftpgo/blob/main/Dockerfile)
- [edge-plugins](https://github.com/drakkan/sftpgo/blob/main/Dockerfile)
- [edge-alpine](https://github.com/drakkan/sftpgo/blob/main/Dockerfile.alpine)
- [edge-slim](https://github.com/drakkan/sftpgo/blob/main/Dockerfile)
- [edge-alpine-slim](https://github.com/drakkan/sftpgo/blob/main/Dockerfile.alpine)
- [edge-distroless-slim](https://github.com/drakkan/sftpgo/blob/main/Dockerfile.distroless)