# Docker部署XX-Net

关于XX-Net，请看[github/XX-Net](https://github.com/XX-net/XX-Net)

### 目录

- [安装docker](#安装docker)

- [获取镜像](#获取镜像)

- [构建镜像](#构建镜像)

- [运行](#运行)

- [允许远程连接](#允许远程连接)

- [修改配置](#修改配置)

- [创建appid](#创建appid)

- [远程配置appid](#远程配置appid)

- [导出证书](#导出证书)

- [其它](#其它)

### 安装docker

 参考[官方说明](https://docs.docker.com/engine/installation/)

### 获取镜像

```sh
docker pull zengchw/xx-net
```

### 构建镜像

```sh
docker build . -t zengchw/xx-net
```

### 运行

建议映射容器卷，方便修改配置和导出证书

```sh
docker run -d --name xx-net \
    -p 8085-8087:8085-8087 \
    -p 1080:1080 \
    -v `pwd`/data:/data/xx-net/data \
    zengchw/xx-net
```

### 允许远程连接

1. 编辑容器内的/data/xx-net/data/launcher/config.yaml文件

2. 把allow_remote_connect的值改为1

3. 重启容器

### 修改配置

在容器内创建/data/xx-net/data/gae_proxy/manual.ini文件
添加以下内容，允许指定网段访问。

```
[listen]
ip = 0.0.0.0
port = 8087
visible = 1
debuginfo = 0

[pac]
ip = 0.0.0.0
```

### 创建appid

 参考[这里](https://github.com/XX-net/XX-Net/wiki/how-to-create-my-appids)

### [远程配置appid](https://github.com/XX-net/XX-Net/tree/master/code/default/gae_proxy/server '官方教程')

```
docker exec -it ${CONTAINER_NAME} bash
cd /data/xx-net/code/default/gae_proxy/server
python uploader.py "appid1|appid2" -debug
#下面的看控制台输出，用浏览器访问链接
```

### 导出证书

把容器内/data/xx-net/data/gae_proxy/CA.crt文件复制出来，要使用xx-net服务的客户端都安装该证书

证书错误参考[这里](https://github.com/XX-net/XX-Net/wiki/%E8%AF%81%E4%B9%A6%E9%94%99%E8%AF%AF)，把证书都删除，重启服务，重新安装证书即可。

### 使用xx-tunnel

容器内/data/xx-net/data/x_tunnel/client.json，按需求增加配置

```json
{
  "socks_host" : "127.0.0.1",
  "socks_port" : 1080,
  "server_host": "1234.xx-net.net",
  "server_port": 443,
  "login_account": "xxnet@github.org",
  "login_password": "MTIzNDU2Nzg5MA"
}
```

- 说明

    socks_host 绑定监听的ip，改为0.0.0.0可向其它设备提供代理

    socks_port 绑定监听的端口，默认1080

    server_host,server_port 指定目标服务器

### 其它

[xx-net wiki](https://github.com/XX-net/XX-Net/wiki)

[IPv6 with Docker](https://docs.docker.com/engine/userguide/networking/default_network/ipv6/)

[IPv6 in a Docker container on a non-ipv6 network](https://raymii.org/s/articles/IPv6_in_a_Docker_container_on_a_non-ipv6_network.html)
