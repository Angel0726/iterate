---
title: docker 的使用
toc: true
date: 2019-06-24
---
# 可以补充进来的

- docker 还是需要好好补充的，这种环境的配置很方便，而且方便迁移并保证环境的一致性。
- 关于 docker 和 nvidia-docker 的使用还是要好好掌握下的。

# docker 的使用

<span style="color:red;">不知道还有没有别的安装 docker 的方法。还是说这个就是最官方的方法了？怎么创建自己的镜像并且可以自己下载？</span>

```
# 安装 docker
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
sudo echo "deb https://apt.dockerproject.org/repo ubuntu-xenial main" >/etc/apt/sources.list.d/docker.list
sudo apt-get update
sudo apt-get install docker-engine
```

```
# 安装 nvidia-docker
wget -P /tmp https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.1/nvidia-docker_1.0.1-1_amd64.deb
sudo dpkg -i /tmp/nvidia-docker*.deb && rm /tmp/nvidia-docker*.deb
```


```
# 启动 docker 服务
systemctl start docker
systemctl start nvidia-docker
```

```
# 下载并启动镜像
sudo nvidia-docker pull hubq/dl4img
```


```
# 在 docker 上运行代码
git clone https://github.com/Jinglue/DL4Img
nvidia-docker run -d -v ~/dl4img/notebook/:/srv -p 8888:8888 -p 6006:6006 hubq/dl4img
```


# 相关

- [DL4Img](https://github.com/Jinglue/DL4Img)
