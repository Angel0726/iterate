---
title: 07 Docker 的分区挂载和数据卷
toc: true
date: 2018-06-11 08:14:47
---
# 可以补充进来的


# Docker 的分区挂载和数据卷

容器中的文件系统是独立的， 一旦容器被删除， 则文件系统也会被删除. 如果想容器和实体机在文件系统层面打通， 可以把指定目录挂载到容器当中:

```sh
docker run -d -p 5000:22 -v /home/zys/temp:/root/volumn zys:common
```

使用 `-v` 参数， 就可以把多个实体机目录挂载到容器的文件系统中.

上面是直观的目录挂载. docker 还有自己的一个 **数据卷** 的概念. 它可以在容器中定义一些目录， 这些目录不使用层级的 AUFS 文件系统， 并且这些目录独立于容器而存在:

```sh
docker run -d -p 5000:22 -v /root/a --name=test zys:common
```



这样， 其 `/root/a` 目录就是一个数据卷， 如果使用 `docker inspect` 查看容器， 可以看到类似下面的信息:

```
"Volumes": {
    "/root/a": "/var/lib/docker/vfs/dir/xxx"
},
"VolumesRW": {
    "/root/a": true
}
```

其它的容器可以重用这个数据卷:

```sh
docker run -d -p 5000:22 --volumes-from=test zys:common
```

这里的形式有些别扭啊，数据卷本来是独立于容器，但是要想重用它，又必须基于容器的名字.

当所有容器被删除后，数据卷本身是还存在的，但是这时好像没办法再去直接使用它了， 不过里面的数据你可以想办法弄到容器里去再作下一步处理.




# 相关

- [Docker简单使用指南](https://www.w3cschool.cn/use_docker/)
