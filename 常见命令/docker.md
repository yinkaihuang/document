# docker相关命令

**查看命令 **

```
1.查看当前docker中镜像信息
docker images

2.查看正在运行的容器
docker ps

3.查看一个镜像由哪些层组成
docker  history 镜像ID
```

**镜像常用操作命令 **

```
1.保存镜像为压缩文件
docker save -o test.tar test-web:v1.0.0

2.导入压缩文件成镜像
docker load -i test.tar

3.删除单个镜像
docker rmi imageId(web-test:v1.0.0) /docker rmi -f imageId(web-test:v1.0.0)

4.下拉镜像
docker pull 镜像url地址

5.上传镜像
docker push 镜像地址
```

**容器常用操作命令 **

```
1.运行docker镜像命令
docker run -it web-test:v1.0.0 /bin/bash

2.重启容器
docker restart 容器名称/容器ID

3.停止容器
docker stop 容器名称/容器ID

4.停止所有容器
docker stop $(docker ps -a -q)

5.删除容器
docker rm 容器名称/容器ID  /docker rm -f 容器名称/容器ID

6.进入容器
docker exec -it 容器ID /bin/bash

7.查看容器挂载情况
docker inspect 容器ID

8.查看容器运行日志
docker logs -f 容器ID
```

