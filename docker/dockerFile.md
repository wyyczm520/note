Dockerfile例子

```
# 后边为注释
```

```FROM``` 必须为大写，后边必须跟着镜像名称

```MAINTAINER``` 意思是镜像作者和作者联系地址

```RUN``` 为运行的DSL命令，构建时运行时

```RUN``` 为运行的DSL命令，构建时运行时

```EXPOST``` 指定运行该镜像的容器使用的端口，但是docker不会自动的打开端口，还需要在使用的时候指定暴露的端口

```RUN``` 为运行的DSL命令，构建时运行时

```RUN``` 为运行的DSL命令，构建时运行时

```CMD```  运行指定命令

三种模式

模式一 ["executable" , "param1" , "param2"]exec模式

模式二 command param1 param2 shell模式

模式三 ["param1","param2"]作为ENTRYPOINT指令的默认参数

```ENTERYPOINT``` 不会被docker run命令中启动命令覆盖

```ADD``` 包含类似tar的解压缩功能，如果单纯的复制文件，docker推荐使用copy

```COPY``` 和add相似，但是不会解压缩

```VOLUME```

WORKDIR 通常使用绝对路径

```ENV```

USER

ONBUILD 添加触发器

例子

```
# Version: 0.0.1
FROM    javaweb/centos:0.1
MAINTAINER  wudixiaowei wudixiaowei@gmail.com
RUN mkdir /root/ceshi.txt
```

运行

```
docker build -t="javaweb/centos:0.1" .
```

-t后边为要运行的容器

.为在当前路径下寻找Dockerfile

docker build -t="javaweb/centos:0.1" \

\为根路径
