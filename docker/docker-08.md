# Dockerfile指令详解

## COPY
格式:
```shell
COPY [--chown=<user>:<group>] <源路径>... <目标路径>

COPY [--chown=<user>:<group>] ["<源路径1>",..."<目标路径>"]
```

复制将会将从构建上下文目录的 `<源路径>` 复制到新一层的镜像的目标路径中，源路径可以有多个，也可以是通配符。

目标路径可以是镜像的绝对路径，也可是相对于 `工作目录` 的相对路径（`工作目录`可以使用 `WORKDIR` 指令来指定 ），目标目录不需要先行创建，不存在会在复制前创建。

## ADD
ADD的源路径可以是一个URL，Docker会自动去下载，并复制到目标路径。

但是如果下载的是一个压缩包，则需要另外写RUN命令解压缩。所以还不如直接写RUN命令，通过wget等下载同时解压缩。

如果源路径是一个tar压缩文件，压缩格式格式为 `gzip` 、`bzip`以及`xz`情况下，ADD会解压缩后复制到目标路径。所以如果不想解压缩，就不能使用ADD。

## CMD

CMD也有两种格式：
  
  - shell格式： `CMD <命令>`
  - exec 格式： `CMD ["可执行文件"， "参数1", "参数2", ...]`
  - 参数列表格式： `CMD ["参数1", "参数2", ...]`，在指定了 `ENRTYPOINT` 指令后，使用CMD指定具体的参数。

在指令格式上，推荐使用 exec 格式，这类格式会被转为JSON数组，因此一定要用双引号而不能用单引号。

shell 格式的命令会被转为 `sh -c` 的参数进行执行。比如: `CMD echo $HOME`,在实际执行中，会变为: `CMD ["sh", "-c", "ECHO $HOME"]`。

所以，使用shell格式就要注意前台执行和后台执行的区别。`sh -c`执行完毕，Docker容器主进程也就执行完毕退出，Docker 容器也就退出了。

## ENTRYPOINT

入口点，目的和CMD一样，都是指定容器启动程序和参数。

ENTRYPOINT在运行时也可以被取代，不过要通过`docker run --entrypoint`指定。

当指定了`ENTRYPOINT`之后，`CMD`的含义变为了`ENTRYPOINT`的参数。
