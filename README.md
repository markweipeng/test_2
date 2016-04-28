
# #如何制作一个定制的 Python 基础 Docker 镜像

> 目标：准备一个定制的 Python 基础镜像。基础镜像，通常为含最小功能的系统镜像，之后的应用镜像都以此为基础。
> 
> 本项目代码维护在 **[DaoCloud/python-sample-base-image](https://github.com/DaoCloud/python-sample-base-image)** 项目中。
>
> 您可以在 GitHub 找到本项目并获取本文中所提到的所有脚本文件。

#### 制作基础镜像

* 选择 Ubuntu 官方的 14.04 版本为我们依赖的系统镜像。

```dockerfile
FROM ubuntu:trusty
```

> 因所有官方镜像均位于境外服务器，为了确保所有示例能正常运行，可以使用与官方镜像保持同步的 DaoCloud 境内镜像：
>
>```dockerfile
>FROM daocloud.io/ubuntu:trusty
>```

* 设置镜像的维护者，相当于镜像的作者或发行方。

```dockerfile
MAINTAINER Captain Dao <support@daocloud.io>
```

* 用 `RUN` 命令调用 apt-get 包管理器安装 Python 环境所依赖的程序包。

> 安装依赖包相对比较固定，因此该动作应该尽量提前，这样做有助于提高镜像层的复用率。
> 
> 安装完依赖后打扫卫生可以显著的减少镜像大小。

```dockerfile
RUN apt-get update && \
  	apt-get install -y python \
    				   python-dev \
                       python-pip && \
  	rm -rf /var/lib/apt/lists/*
```

> *以下是一个不建议的做法，原因是比上述命令多添加了一层镜像，然而并没有降低总镜像的体积。*
>
>```dockerfile
>RUN apt-get update && \
>  	apt-get install -y python \
>    				   python-dev \
>                       python-pip 
>RUN rm -rf /var/lib/apt/lists/*
>```

* 用 `RUN` 命令调用 `mkdir` 来准备一个干净的放置代码的目录。

```dockerfile
RUN mkdir -p /app
```

* 指定其为当前的工作目录。

```dockerfile
WORKDIR /app
```

* 指定暴露的容器内端口地址，最后设置默认启动命令。

```dockerfile
EXPOSE 80
CMD ["bash"]
```
