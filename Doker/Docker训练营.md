# Docker相关概念介绍

## 镜像

`Docker` 的镜像概念类似于虚拟机里的镜像，是一个只读的模板，一个独立的文件系统，包括运行容器所需的数据，可以用来创建新的容器。

创建镜像

1. 编写 `Dockerfile`
2. 从类似 `github` 的 `Docker Hub` 上下载镜像使用。

## 容器(建立在Docker镜像上)

`Docker` 容器是由 `Docker` 镜像创建的运行实例。`Docker`容器类似虚拟机，可以支持的操作包括启动，停止，删除等。每个容器间是相互隔离的，但隔离的效果比不上虚拟机。容器中会运行特定的应用，包含特定应用的代码及所需的依赖文件。

## 仓库

`Docker` 仓库是用来包含镜像的位置，`Docker` 提供一个注册服务器（`Registry`）来保存多个仓库，每个仓库又可以包含多个具备不同 `tag` 的镜像。`Docker` 运行中使用的默认仓库是 `Docker Hub` 公共仓库。

仓库支持的操作类似 `git`，创建了新的镜像后，我们可以 `push` 提交到仓库，也可以从指定仓库 `pull` 拉取镜像到本地。
# 安装Docker

## 1. 设置存储库

更新 `apt` 软件包索引：

```
$ sudo apt-get update
```

安装一些软件包，以允许 `apt` 通过 `HTTPS` 使用存储库：

```
$ sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
```

这里我们使用阿里云提供的源，添加相应的密钥：

```
$ curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

添加相应源的信息：

```
$ sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

## 2. 安装 docker-engine

查看此时 `docker` 的版本信息:

```
# 更新 apt 索引库
$ sudo apt-get update

# 查看可用的版本
$ sudo apt-cache madison docker-engine
```

## 3. 安装Docker

```
$ sudo apt-get install docker-engine=17.05.0~ce-0~ubuntu-trusty 
```

> 如果要安装最新版，可使用 `sudo apt-get install docker-ce`。

在安装成功后，`Docker` 的守护进程自动启动，不需要手动启动服务。

此时，我们可以查看其版本信息，使用如下命令：

```
$ docker version
```

## 4. 让普通用户也可以执行docker命令
默认情况下，该套接字归属于 `root` 用户，对于其它用户只能通过 `sudo`来进行访问。

因此我们如果要让 `shiyanlou` 用户可以执行 `docker` 命令，需要创建一个名为 `docker` 的用户组，并将我们要执行 `docker` 命令的用户添加到该用户组中。该用户组会在安装后自动创建，我们只需执行添加用户到 `docker` 用户组的操作

```
$ sudo gpasswd -a shiyanlou docker
```

添加用户到一个用户组中的方式有很多，例如我们还可以使用如下命令：

```
$ sudo usermod -aG docker shiyanlou
```

在添加成功后，我们还需要重新开始一个 `shell` 修改才能生效。这时可以尝试打开一个新的终端或者使用如下命令：

```
$ sudo su shiyanlou
```

## 5.  启动 Docker 服务

对于 `Docker` 的镜像仓库来说，国内访问速度较慢，我们添加一个阿里云提供的 `Docker` 镜像加速器。

首先，我们需要编辑 `/etc/docker/daemon.json` 文件：

```
$ sudo vi /etc/docker/daemon.json
```

然后加入如下内容：

```
{
  "registry-mirrors": ["https://n6syp70m.mirror.aliyuncs.com"]
}
```

修改之后，需要重启 `docker` 服务，让修改生效。使用如下命令：

```
$ sudo service docker restart
```

## 6. 查看docker是否安装成功

在安装之后，我们可以通过运行一个 `hello-world` 的镜像来验证 `Docker CE` 是否被正确的安装，使用如下命令：

```
$ docker run hello-world
```

该命令会下载一个名为 `hello-world` 的镜像并运行于一个容器中。当这个容器运行时，会输出一些信息并退出:

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1515563587185.png/wm)

如上图中标注出的提示信息所示，提示我们安装正确。

# 容器管理

## Docker命令

### 1. 查看系统信息

除了查看版本信息之外，在 `docker` 的命令组中还有一个较为常用的命令，查看系统的一些相关信息：

```
docker system info

或者使用命令

docker info
```
### 2. Management Commands

在 `Docker 1.12 CLI` 中大约有四十个左右的顶级命令，这些命令没有经过任何组织，显得十分混乱，对于新手来说，学习它们并不轻松。

而在 `Docker 1.13` 中将命令进行分组，就得到如上图中所示的 `Management Commands`。例如经常使用的容器的一些相关命令：

```
# 创建一个新的容器，下面分别为 Commands 和 Management Commands，作用相同
docker create
docker container create

# 显示容器列表
docker ps
docker container ls

# 在一个新的容器中运行一个命令
docker run
docker container run

...
```

如上所示，对于新的命令而言相比于旧命令明显更具有可读性。并且在实验环境中的 `docker` 版本以及最新版本中两者都是有效的命令，所以在这里我们将一些常用的命令，及其对应的 `Management Commands` 命令都列举出来，方便大家在后续的学习过程中可以进行参考。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517897142154.png/wm)

### 3.help

我们可以直接通过 `help` 或者使用 `man` 手册的方式查看相关命令的详细说明，例如我们直接使用如下命令:

```
$ docker --help
```

## 容器

### 查看容器列表

查看容器列表可以使用如下命令：

```
docker container ls [OPTIONS]

或者旧的命令

docker ps [OPTIONS]
```

在使用命令时，我们可以使用一些可选的配置项 `[OPTIONS]`。

- `-a` 显示所有的容器
- `-q` 仅显示 `ID`
- `-s` 显示总的文件大小

> 这些配置项对于上述的两个命令都是有效的，在后面的内容不会再特殊说明。

默认情况下，直接使用该命令仅显示正在运行的容器，如下所示：

```
$ docker container ls
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1515566501674.png/wm)

此时并没有处于运行中的容器，所以显示为空。我们可以使用 `-a` 参数，来显示所有的容器，并加上 `-s` 选项，显示大小，命令如下：

```
$ docker container ls -a -s
```

### 创建一个容器

#### docker run

首先，我们回顾在上一节使用到的 `docker run hello-world` 命令，该命令的格式为：

```
docker run [OPTIONS] IMAGE [COMMAND]
```

对应于 `Management Commands` 的命令为：

```
docker container run [OPTIONS] IMAGE [COMMAND]
```

上述两个命令的作用相同，`docker run` 命令会在指定的镜像 `IMAGE` 上创建一个可写的容器（因为镜像是只读的），然后开始运行指定的命令 `[COMMAND]`。

一些常用的配置项为：

- `-i` 或 `--interactive`， 交互模式
- `-t` 或 `--tty`， 分配一个 `pseudo-TTY`，即伪终端
- `--rm` 在容器退出后自动移除
- `-p` 将容器的端口映射到主机
- `-v` 或 `--volume`， 指定数据卷

> 关于该命令的详细参数较多，并且大多数参数在很多命令中的意义是相同的，将在后面的内容中使用到时进行相应的介绍。

我们指定 `busybox` 镜像，然后运行命令 `echo "hello shiyanlou"` 命令，如下所示：

```
$ docker container run busybox echo "hello shiyanlou"
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1515568678271.png/wm)

在上图中，我们可以看到该命令执行的过程：

1. 对于指定镜像而言，首先会从本地查找，找不到时将会从镜像仓库中下载该镜像
2. 镜像下载完成后，通过镜像启动容器，并运行 `echo "hello shiyanlou"` 命令，输出运行结果之后退出。

在执行命令之后，容器就会退出，如果我们需要一个保持运行的容器，最简单的方法就是给这个容器一个可以保持运行的命令或者应用，比如 `bash`，例如我们在 `ubunutu` 容器中运行 `/bin/bash` 命令：

```
$ docker container run -i -t ubuntu /bin/bash
```

对于交互式的进程而言（例如这里的 bash），必须将 `-i` 和 `-t` 参数一起使用，才能为容器进程分配一个伪终端，通常我们会直接使用 `-it`。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516695039457.png/wm)

如上所示，我们已经进入到分配的终端中了，这时如果我们需要退出 `bash`，可以使用以下两种方式，它们的效果完全不同：

1. 直接使用 `exit` 命令，这时候 `bash` 程序终止，容器进入到停止状态
2. 使用组合键退出，容器仍然保持运行的状态，可以再次连接到这个 `bash` 中，组合键是 `ctrl + p` 和 `ctrl +q`。即先同时按下 `ctrl` 和 `p` 键，再同时按 `ctrl` 和 `q` 键。就可以退出

这里我们使用第二种方式，然后使用 `docker container ls` 命令，可以看到该容器仍然处于运行中。

#### docker container create

严格意义上来讲，`docker run` 命令的作用并不是创建一个容器，而是在一个新的容器中运行一个命令。而用于创建一个新容器的命令为

```
docker container create [OPTIONS] IMAGE [COMMAND] [ARG...]

或者使用旧的

docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
```

该命令会在指定的镜像 `IMAGE` 上创建一个可写容器层，并 **准备** 运行指定的命令。需要着重强调的是，这里是准备运行，并不是立即运行。即该命令只创建容器，并不会运行容器。

一些常见的配置项如下所示：

- `--name` 指定一个容器名称，未指定时，会随机产生一个名字。
- `--hostname` 设置容器的主机名
- `--mac-address` 设置 `MAC` 地址
- `--ulimit` 设置 Ulimit 选项。

> 关于上述提到的 `ulimit`，我们可以通过其对容器运行时的一些资源进行限制。`ulimit` 是一种 `linux` 系统的内建功能，一些简单的描述，可以参考 <https://www.ibm.com/developerworks/cn/linux/l-cn-ulimit/> ，而对于在下面我们将要设置的部分值的含义，可以参考 ttps://access.redhat.com/solutions/61334 。

除此之外，关于创建容器，我们还可以设置有关存储和网络的详细内容，将会在下一节的内容中进行介绍。

如下示例，我们指定容器的名字为 `shiyanlou`，主机名为 `shiyanlou`，设置相应的 `MAC` 地址，并通过 `ulimit` 设置最大进程数（`1024:2048` 分别代表软硬资源限制，详细内容可以参考上面的链接），使用 `ubuntu` 的镜像，并运行 `bash`：

```
$ docker container create --name shiyanlou --hostname shiyanlou --mac-address 00:01:02:03:04:05 --ulimit nproc=1024:2048 -it ubuntu /bin/bash
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516694843573.png/wm)

此时，容器创建成功后，会打印该容器的 `ID`，这里需要简单说明一下，在 `docker` 中，容器的标识有三种比较常见的标识方式：

- `UUID` 长标识符，例如 `1f6789f885029dbdd4a6426d7b950996a5bcc1ccec9f8185240313aa1badeaff`
- `UUID` 短标识符，从长标识符开始，只要不与其它标识符冲突，可以从头开始，任意选用位数，例如针对上面的长标识符，可以使用 `1f`，`1f678` 等等
- `Name` 最后一种方式即是使用容器的名字

在容器创建成功后，我们可以查看其运行状态，使用如下命令：

```
# 此时该容器并未运行，需要使用 -a 参数
$ docker container ls -a
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516695279979.png/wm)

新创建的容器的状态 (`STATUS`) 为 `Created`，并且其容器名被设置为对应的值。

## 查看容器的详细信息

查看容器的详细信息可以使用如下命令：

```
docker container inspect [OPTIONS] CONTAINER [CONTAINER...]

或者旧的

docker inspect [OPTIONS] CONTAINER [CONTAINER...]
```

例如我们查看刚刚创建的容器的详细信息就可以使用以下命令：

```
# 使用容器名
$ docker container inspect shiyanlou

# 使用 ID ，因生成的 ID 不同，需要修改为相应的 ID
$ docker container inspect 1f6789

$ docker container inspect 1f6
```

例如，我们查看刚刚创建的名为 `shiyanlou` 的容器的 `MAC`地址，就可以使用如下命令：

```
$ docker container inspect shiyanlou | grep "00:01"
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516695478022.png/wm)

## 容器的启动和暂停及退出

容器的启动命令为：

```
docker container start [OPTIONS] CONTAINER [CONTAINER...]
```

对于上面我们创建的容器而言，此时处于 `Created` 状态，需要使用如下命令启动它：

```
$ docker container start shiyanlou
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516695629395.png/wm)

此时，运行一个容器我们分成了两个步骤，即创建和启动，使用的命令如下：

```
# 创建
$ docker container create --name shiyanlou --hostname shiyanlou --mac-address 00:01:02:03:04:05 --ulimit nproc=1024:2048 -it ubuntu /bin/bash

# 启动
$ docker container start shiyanlou
```

上述的两个命令如果我们使用 `docker container run` 只需要一步即可，即此时 `run` 命令同时完成了 `create` 及 `start` 操作：

```
$ docker container run --name shiyanlou --hostname shiyanlou --mac-address 00:01:02:03:04:05 --ulimit nproc=1024:2048 -it ubuntu /bin/bash
```

> 除此之外，上面的 `run` 命令还完成一些其它的操作，例如没有镜像时会 `pull` 镜像，使用 `-it` 参数时完成了 `attach` 操作（后面会学习该操作），使用 `--rm` 参数在容器退出后还会完成 `container rm` 操作。

> `run` 命令是一个综合性的命令，如果能够熟练的使用它可以简化很多步骤，但是其使用方式较为复杂

启动之后，暂停容器可以使用如下命令：

```
# 暂停一个或多个容器
docker container stop [OPTIONS] CONTAINER [CONTAINER...]

# 暂停一个或多个容器中的所有进程
docker container pause CONTAINER [CONTAINER...]
```

上述两个命令的区别在于一个是暂停容器中的进程，而另外一个是暂停容器，例如，我们使用 `stop` 停止刚刚启动的容器就可以使用如下命令：

```
$ docker container stop shiyanlou

# 查看容器的状态
$ docker container ls -a
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516695730293.png/wm)

如上图所示，容器被暂停后，此时处于 `Exited` 状态。

## 连接到容器

上述操作我们启动的容器运行于后台，所以，我们需要使用 `attach` 操作将本地标准输入输出流连接到一个运行中的容器，命令格式为：

```
docker container attach [OPTIONS] CONTAINER
```

如下示例，我们启动容器，并使用连接命令：

```
$ docker container start shiyanlou

$ docker container attach shiyanlou
```
## 其他常用命令
### 获取日志

获取容器的输出信息可以使用如下命令：

```
docker container logs [OPTIONS] CONTAINER
```

常用的配置项有：

- `-t` 或 `--timestamps` 显示时间戳
- `-f` 实时输出，类似于 `tail -f`

如下所示，我们查看刚刚创建的容器的日志，使用如下命令：

```
$ docker container logs -tf shiyanlou
```

### 显示进程

除了获取日志之外，还可以显示运行中的容器的进程信息，例如查看刚刚创建的容器的进程信息：

```
$ docker container top shiyanlou
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516696183042.png/wm)

> 需要注意的是，该命令对于并未运行的容器是无效的

### 查看修改

查看相对于镜像的文件系统来说，容器中做了哪些改变，可以使用如下命令：

```
docker container diff shiyanlou
```

例如我们在 `shiyanlou` 容器中创建一个文件，就可以使用 `diff` 命令查看到相应的修改：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516696518553.png/wm)

### 重启

重启容器可以使用如下命令：

```
docker container restart shiyanlou
```

### 执行命令

除了使用 `docker container run` 执行命令之外，我们还可以在一个运行中的容器中执行命令，使用如下格式：

```
docker container exec [OPTIONS] CONTAINER COMMAND [ARG...]
```

例如，我们在刚刚创建的容器中执行 `echo "test_exec"` 命令，就可以使用如下命令：

```
$ docker container exec shiyanlou echo "test_exec"
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516696614726.png/wm)

### 删除容器

删除容器的命令：

```
docker container rm [OPTIONS] CONTAINER [CONTAINER...]
```

> 需要注意的是，在删除容器后，在容器中进行的操作并不会持久化到镜像中

# 镜像管理

## 镜像

镜像存储中的核心概念仓库（Repository）是镜像存储的位置。Docker 注册服务器（Registry）是仓库存储的位置。每个仓库包含不同的镜像。

比如一个镜像名称 `ubuntu:14.04`，冒号前面的 `ubuntu` 是仓库名，后面的 `14.04` 是 TAG，不同的 TAG 可以对应相同的镜像，TAG 通常设置为镜像的版本号。

### 查看镜像列表

我们查看镜像可以使用如下命令：

```
$ docker image ls
```

也可以查看指定仓库的镜像，例如。查看 `ubuntu` 仓库的镜像：

```
$ docker image ls ubuntu
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516697042412.png/wm)

### 查看镜像的详细信息

查看镜像的详细信息使用如下命令：

```
docker image inspect ubuntu
```

### 拉取镜像

上面的内容中描述了仓库和注册表的内容，这里，我们学习从注册表中获得镜像或者仓库的命令，使用如下命令：

```
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```

比较常用的配置参数为 `-a`，代表下载仓库中的所有镜像，即下载整个存储库。

如下所示，我们下载 `ubuntu:14.04` 镜像，使用如下命令：

```
$ docker image pull ubuntu:14.04
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516697216997.png/wm)

对于 `pull` 下来的镜像来说，其具体的保存路径为 `/var/lib/docker`。因为这里的存储驱动为 `aufs`，所以具体路径为 `/var/lib/docker/aufs`

### 构建镜像

#### commit

此时，对于我们 `pull` 的新镜像 `ubuntu:14.04` 来说，如果我们需要对其进行更新，可以创建一个容器，在容器中进行修改，然后将修改提交到一个新的镜像中。

提交修改使用如下命令：

```
docker container commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```

该命令的解释为从一个容器的修改中创建一个新的镜像。例如，我们运行一个容器，然后在其中创建一个文件，最后使用 `commit` 命令：

```
# 使用 run 创建运行一个新命令
$ docker container run -it --name shiyanlou001 busybox /bin/sh

# 在运行的容器中创建两个文件，test1 和 test2
touch test1 test2

# 使用 ctrl + p  及  ctrl+q 键退出

# 使用提交命令，提交容器 shiyanlou001 的修改到镜像 busybox:test 中
$ docker container commit shiyanlou001 busybox:test

# 查看通过提交创建的镜像
$ docker image ls busybox
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516697682918.png/wm)

> 通过上述操作我们创建了一个新的镜像，但是本方法不推荐用在生产系统中，未来会很难维护镜像。最好的创建镜像的方法是 `Dockerfile`，修改镜像的方法是修改 `Dockerfile`，然后重新从 `Dockerfile` 中构建新的镜像。

#### BUILD

`docker` 可以从一个 `Dockerfile` 文件中自动读取指令构建一个新的镜像。 `Dockerfile` 是一个包含用户构建镜像命令的文本文件。在 创建该文件后，我们可以使用如下命令构建镜像：

```
docker image build [OPTIONS] PATH | URL
```

> 构建镜像时，该过程的第一件事是将 `Dockerfile`文件所在目录下的所有内容递归的发送到守护进程。所以在大多数情况下，最好是创建一个新的目录，在其中保存 `Dockerfile`，并在其中添加构建 `Dockerfile` 所需的文件。

对于一个 `Dockerfile` 文件内容来说，基本语法格式如下所示：

```
# Comment
INSTRUCTION arguments
```

使用 `#` 号作为注释，指令（`INSTRUCTION`）不区分大小写，但是为了可读性，一般将其大写。而 `Dockerfile` 的指令一般包含下面几个部分：

1. 基础镜像：以哪个镜像为基础进行制作，使用 `FROM` 指令来指定基础镜像，一个 `Dockerfile` 必须以 `FROM` 指令启动。
2. 维护者信息：可以指定该 `Dockerfile` 编写人的姓名及邮箱，使用 `MAINTAINER` 指令。
3. 镜像操作命令：对基础镜像要进行的改造命令，比如安装新的软件，进行哪些特殊配置等，常见的是 `RUN` 命令。
4. 容器启动命令：基于该镜像的容器启动时需要执行哪些命令，常见的是 `CMD` 命令或 `ENTRYPOINT`

例如一个最基本的 `Dockerfile`：

```
# 指定基础镜像
FROM ubuntu:14.04

# 维护者信息
MAINTAINER shiyanlou/shiyanlou001@simplecloud.cn

# 镜像操作命令
RUN apt-get -yqq update && apt-get install -yqq apache2

# 容器启动命令
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

通过阅读上述内容中我们熟悉的一些 `linux` 指令，可以很容易的得出该命令创建了一个 `apache` 的镜像。包含了最基本的四项信息。

其中 `FROM` 指定基础镜像。`RUN` 命令默认使用 `/bin/sh`，并使用 `root` 权限执行。`CMD` 命令也是默认在 `/bin/sh` 中执行，但是只能有一条 `CMD` 指令，如果有多条则只有最后一条会被执行。

下面我们创建一个空目录，并在其中编辑 `Dockerfile` 文件，并基于此构建一个新的镜像，使用如下操作：

```
# 首先创建目录并切换目录
$ mkdir /home/shiyanlou/test1 && cd /home/shiyanlou/test1

# 编辑 Dockerfile 文件，默认文件名为 `Dockerfile`，也可以使用其它值，使用其它值需要在构建时通过 `-f` 参数指定，这里我们使用默认值。并在其中添加上述示例的内容
$ vim Dockerfile

# 使用 build 命令，`-t` 参数指定新的镜像
$ docker image build -t shiyanlou:1.0 .
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516700296627.png/wm)

在执行构建命令后，需要花费一些时间来完成构建。在运行结束后，最后查看新创建的镜像：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516700483506.png/wm)

在构建完成后，我们可以使用该镜像启动一个容器来运行 `apache` 服务，运行如下命令：

```
# 使用 -p 参数将本机的 8000 端口映射到容器中的 80 端口上。
$ docker container run -d -p 8000:80 --name shiyanlou002 shiyanlou:1.0
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516700663701.png/wm)

此时，容器启动成功后，并且配置了端口映射，我们就可以通过本机的 `8000` 端口访问容器 `shiyanlou002` 中的 `apache` 服务了。我们打开浏览器，输入 `localhost:8000`，显示结果如下图：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516700754471.png/wm)

> 更多有关于 Dockerfile 文件格式的信息可以参考官方文档 <https://docs.docker.com/engine/reference/builder/>

### 删除

我们删除 `ubuntu:latest` 镜像就可以使用如下命令：

```
# 删除镜像
$ docker image rm ubuntu
```

需要注意的是，如果该镜像正在被一个容器所使用，需要将容器删除才能成功的删除镜像。

# 网络管理

## 网络

在我们安装 `Docker` 后，会自动创建三个网络。我们可以使用下面的命令来查看这些网络：

```
$ docker network ls
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516712155589.png/wm)

如上图所示，三种默认的网络，分别为 `bridge`，`host`，`none`。

### bridge

`bridge`，即桥接网络，在安装 `docker` 后会创建一个桥接网络，该桥接网络的名称为 `docker0`。我们可以通过下面两条命令去查看该值。

```
# 查看 bridge 网络的详细信息，并通过 grep 获取名称项
$ docker network inspect bridge | grep name

# 使用 ifconfig 查看 docker0 网络
ifconfig
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516775691321.png/wm)

在上图中，我们可以查看到对应的值。默认情况下，我们创建一个新的容器都会自动连接到 `bridge` 网络。其详细信息如下所示：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516778041875.png/wm)

我们可以尝试创建一个容器，该容器会自动连接到 `bridge`网络，例如我们创建一个名为 `shiyanlou001` 的容器：

```
$ docker container run -itd --name shiyanlou001 ubuntu /bin/bash

上述命令中默认使用 --network bridge ，即指定 bridge 网络，与下面的命令等同
$ docker container run -itd --name shiyanlou001 --network bridge ubuntu /bin/bash
```

创建后，再次查看 `bridge` 的信息：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516778620559.png/wm)

这时可以查看到相应的容器的网络信息，该容器在连接到 `bridge` 网络后，会从子网的地址池中获得一个 IP 地址，即上图中的 `192.168.0.2`。

使用 `docker container attach shiyanlou001` 命令，也可查看相应的地址信息：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516778879129.png/wm)

> 如果提示没有找到 `ifconifg` 命令，可以通过如下命令安装：
>
> ```
> $ sudo apt update
> $ sudo apt install net-tools
> ```

并且对于连接到默认的 `bridge` 之间的容器可以通过 IP 地址互相通信。例如我们启动一个 `shiyanlou002` 的容器，它可以与 `shiyanlou001` 通过 IP 地址进行通信。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516877067391.png/wm)

> 如果提示没有找到 `ping` 命令，可使用如下命令安装：
>
> ```
> $ sudo apt update 
> $ sudo apt install iputils-ping
> ```
>
> 其具体的实现原理可以参考链接 [Linux 上的基础网络设备](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1516877067391.png/wm)，以及涉及到[网桥的工作原理](https://segmentfault.com/a/1190000009491002/wm)

上述的操作我们通过 `ping` 命令演示了 `IP` 相关的内容。但是对于应用程序来讲，如果需要在外部进行访问，我们还会涉及到端口的使用，而 `Docker` 对于 `bridge` 网络使用端口的方式为设置端口映射，通过 `iptables` 实现。

下面我们通过 `iptables` 来为大家演示 docker 实现端口映射的方式，主要针对 `nat` 表和 `filter` 表：

1. 首先删除掉上面创建的两个容器。这里不再给出具体的命令
2. 这时，我们查看 `nat` 表的转发规则，使用如下命令：

```
$ sudo iptables -t nat -nvL
```

1. 由于此时并未创建 docker 容器，nat 表中没有什么特殊的规则。接下来，我们使用上一节构建的 `shiyanlou:1.0` 镜像创建一个容器 `shiyanlou001`，并将本机的端口 `10001` 映射到容器中的 `80` 端口上，在浏览器中可以通过 `localhost:10001` 访问容器 `shiyanlou001` 的 `apache` 服务，命令如下：

```
$ docker run -d -p 10001:80 --name shiyanlou001 shiyanlou:1.0
```

> `docker run` 命令的 `-p` 参数是通过端口映射的方式，将容器的端口发布到主机的端口上。其使用格式为 `-p ip:hostPort:containerPort`。并且还可以指定范围，例如 `-p 10001-10100:1-100`，代表将容器 `1-100` 的端口映射到主机上的 `10001-10100`端口上，两者一一对应。

1. 创建成功后，我们可以在浏览器中输入 `localhost:10001` 访问到容器 `shiyanlou001` 的 `apache` 服务，并查看此时 `iptables` 中 `nat` 表和 `filter` 表的规则，其中分别新增了一条比较重要的内容，如下图所示：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517189337316.png/wm)

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517189420438.png/wm)

1. 接下来，再次使用镜像 `shiyanlou:1.0` 来启动一个容器 `shiyanlou002`，这次我们不指定端口映射，通过手动修改 `nat` 表的方式来模拟实现：

```
$ docker run -d --name shiyanlou002 shiyanlou:1.0
```

1. 获取容器 `shiyanlou002` 的 ip 地址，如果按步骤操作此 ip 为 `192.168.0.3`。此时我们想通过主机的 `10002`端口访问容器 `shiyanlou002` 的 `80` 端口，就可以添加一条规则：

```
# 添加一条规则，大致解释为将从非 docker0 接口上，目的端口为 10002 的 tcp 报文，修改其目的地址为 192.168.0.3:80

$ sudo iptables -t nat -A DOCKER ! -i docker0 -p tcp --dport 10002 -j DNAT --to-destination 192.168.0.3:80
```

1. 添加成功后我们在主机发出的本地公网或内网 ip 加端口号 10002 的请求会被定位到 `192.168.0.3:80` 上，但是在将请求转发到 `docker0` 网桥上时，对于默认的 `filter` 表中的 `FOEWARD` 链的规则是 `DROP`，因此我们还需要在 `filter` 表中设置相应的规则：

```
$ sudo iptables -t filter -A FORWARD ! -i docker0 -o docker0 -p tcp -d 192.168.0.3 -j ACCEPT --dport 80

或者你也可以选择将其加到由 docker 定义的 DOCKER 链中，上面的命令和下面的命令选择其中的一个即可

$ sudo iptables -t filter -A DOCKER ! -i docker0 -o docker0 -p tcp -d 192.168.0.3 -j ACCEPT --dport 80
```

1. 此时我们就能够通过 `192.168.0.3:80` 访问容器 `shiyanlou002` 中的 `apache` 服务了。 即通过 `iptables` 的方式实现了容器 `shiyanlou002` 上 `80` 端口到主机 `10002` 端口的映射。

2. 最后，为了不影响后面实验的进行，这里我们删除掉手动添加的规则，并删除容器。

   删除手动添加的规则可使用如下方法：

   ```
   #查看 nat 规则
   $ sudo iptables -t nat -nvL --line-numbers
   #比如删除 DOCKER 链的第 2 条规则
   $ sudo iptables -t nat -D DOCKER 2
   
   #查看 filter 规则
   $ sudo iptables -nvL --line-numbers
   #比如删除 DOCKER 链第 1 条规则
   $ sudo iptables -D DOCKER 1
   ```

### 自定义网络

对于默认的 `bridge` 网络来说，使用端口可以通过端口映射的方式来实现，并且在上面的内容中我们也演示了容器之间通过 `IP` 地址互相进行通信。但是对于默认的 `bridge` 网络来说，每次重启容器，容器的 `IP` 地址都是会发生变化的，因为对于默认的 `bridge` 网络来说，并不能在启动容器的时候指定 ip 地址，在启动单个容器时并不容易看到这一区别。

#### 旧版的容器互联

容器间都是通过在 `/etc/hosts` 文件中添加相应的解析，通过容器名，别名，服务名等来识别需要通信的容器。

这里，我们启动两个容器，来演示旧的容器互联：

1. 首先启动一个名为 `shiyanlou001` 的容器，使用镜像 `busybox`：

```
$ docker run -it --rm --name shiyanlou001 busybox /bin/sh
```

1. 这时打开一个新的终端，启动一个名为 `shiyanlou002`的容器，并使用 `--link` 参数与容器 `shiyanlou001`互联。

```
$ docker run -it --rm --name shiyanlou002 --link shiyanlou001 busybox /bin/sh
```

> docker run 命令的 `--link` 参数的格式为 `--link <name or id>:alias`。格式中的 `name` 为容器名，`alias` 为别名。即可以通过 `alias` 访问到该容器。

如下图所示，左侧为 `shiyanlou001`，右侧为 `shiyanlou002`：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517193383367.png/wm)

1. 如果此时 `shiyanlou001` 容器退出，这时我们启动一个 `shiyanlou003`，再次启动一个 `shiyanlou001`：

```
$ docker run -itd --name shiyanlou003 --rm busybox /bin/sh

$ docker run -it --name shiyanlou001 --rm busybox /bin/sh
```

按照顺序分配的原则，此时 `shiyanlou003` 的 IP 地址为 `192.168.0.2`，容器 `shiyanlou001` 的 IP 地址为 `192.168.0.4`。并且此时容器 `shiyanlou002` 中 `/etc/hosts` 文件的解析依旧不变，所以不能获取到正确的解析：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517194194401.png/wm)

如上所示，旧的容器 `shiyanlou002` 通过 `--link` 连接到 `shiyanlou001`。而在 `shiyanlou001` 重启后，由于 IP 地址的变化，此时 `shiyanlou002` 并不能正确的访问到 `shiyanlou001`。

除了使用 `--link` 链接的方式来达到容器间互联的效果，在 `docker` 中，容器间的通信更应该使用的是自定义网络。

#### 自定义网络

docker 在安装时会默认创建一个桥接网络，除了使用默认网络之外，我们还可以创建自己的 `bridge` 或 `overlay` 网络。

如下所示，我们创建一个名为 `network1` 的桥接网络，简单命令如下：

```
$ docker network create network1

$ docker network ls
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517195281317.png/wm)

创建成功后，可以使用 `ifconfig` 或者 `ip addr show` 命令查看该桥接网络的网络接口信息，如下所示：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517195572337.png/wm)

而对于该网络的详细信息可以通过 `docker network inspect network1` 命令来查看，如下图所示：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517195691681.png/wm)

其相应的网络接口名称和子网都是由 docker 随机生成，当然，我们也可以手动指定：

```
# 首先删除掉刚刚创建的 network1 
$ docker network rm network1

# 再次创建 network1，指定子网
$ docker network create -d bridge --subnet=192.168.16.0/24 --gateway=192.168.16.1 network1
```

此时，我们可以运行一个容器 `shiyanlou001`，指定其网络为 `network1`，使用 `--network network1`：

```
$ docker run -it --name shiyanlou001 --network network1 --rm busybox /bin/sh
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517196736819.png/wm)

使用 `exit` 退出该容器使其自动删除，这时我们再次创建该容器，但是不指定其 `--network`：

```
$ docker run -it --name shiyanlou001 --rm busybox /bin/sh
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517196983288.png/wm)

此时，该容器连接到默认的 `bridge` 网络，这时，可以新打开一个终端，在其中运行如下命令，将 `shiyanlou001` 连接到 `network1` 网络中：

```
# 在新打开的终端中运行，将容器 shiyanlou001 连接到 network1 网络中
$ docker network connect network1 shiyanlou001

# 这时再次在容器 `shiyanlou001` 中使用 `ifconfig` 命令
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517197247336.png/wm)

如上图中所示，出现了一个 `eth1` 接口，此时，`eth0` 连接到默认的 `bridge` 网络，`eth1` 连接到 `network1` 网络。

对于自定义的网络来说，docker 嵌入的 `DNS` 服务支持连接到该网络的容器名的解析。这意味着连接到同一个网络的容器都可以通过容器名去 `ping` 另一个容器。

如下所示，启动两个容器，连接到 `network1`：

```
$ docker run -itd --name shiyanlou_1 --network network1 --rm busybox /bin/sh

$ docker run -it --name shiyanlou_2 --network network1 --rm busybox /bin/sh
```

启动之后，由于上述的两个容器都是连接到 `network1` 网络，所以可以通过容器名 `ping` 通：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517198037471.png/wm)

除此之外，在用户自定义的网络中，是可以通过 `--ip` 指定 IP 地址的，而在默认的 `bridge` 网络不能指定 IP 地址：

```
# 连接到 network1 网络，运行成功
$ docker run -it --network network1 --ip 192.168.16.100 --rm busybox /bin/sh

# 连接到默认的 bridge 网络，下面的命令运行失败
$ docker run -it --rm busybox --ip 192.168.0.100 --rm busybox /bin/sh
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517198404575.png/wm)

### host 和 none

`host` 网络，容器可以直接访问主机上的网络。

例如，我们启动一个容器，指定网络为 `host`：

```
$ docker run -it --network host --rm busybox /bin/sh
```

如下所示，该容器可以直接访问主机上的网络：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517214736450.png/wm)

`none` 网络，容器中不提供其它网络接口。

```
$ docker run -it --nerwork none --rm busybox /bin/sh
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid377240labid4104timestamp1517214875513.png/wm)

# 编写Dockerfile

Dockerfile 是一个文本文件，其中包含我们为了构建 Docker 镜像而手动执行的所有命令。Docker 可以从 Dockerfile 中读取指令来自动构建镜像。我们可以使用 `docker build` 命令来创建一个自动构建。

## Dockerfile 基本语法

### 上下文

在 Docker 容器及镜像管理一节中我们有提到构建镜像的一些知识。

构建镜像时，该过程的第一件事是将 `Dockerfile` 文件所在目录下的所有内容递归的发送到守护进程。所以在大多数情况下，最好是创建一个新的目录，在其中保存 `Dockerfile`，并在其中添加构建 `Dockerfile` 所需的文件。而 Dockerfile 文件所在的路径也被称为上下文（context）。

首先创建一个目录，以便开始后面的实验过程：

```
$ mkdir dir1 && cd dir1
```

下面我们简单介绍 Dockerfile 中常用的指令。

### FROM

使用 FROM 指令指定一个基础镜像，后续指令将在此镜像的基础上运行：

```
FROM ubuntu:14.04
```

### USER

在 Dockerfile 中可以指定一个用户，后续的 `RUN`，`CMD` 以及 `ENTRYPOINT` 指令都会使用该用户去执行，但是该用户必须提前存在。

```
USER shiyanlou
```

### WORKDIR

除了指定用户之外，还可以使用 `WORKDIR` 指定工作目录，对于 `RUN`，`CMD`，`COPY`，`ADD` 指令将会在指定的工作目录中去执行。也可以理解为命令执行时的当前目录。

```
WORKDIR /
```

### RUN，CMD，ENTRYPOINT

RUN 指令用于执行命令，该指令有两种形式：

- `RUN <command>`，使用 shell 去执行指定的命令 `command`，一般默认的 `shell` 为 `/bin/sh -c`。
- `RUN ["executable", "param1", "param2", ...]`，使用可执行的文件或程序 `executable`，给予相应的参数 `param`。

例如我们执行更新命令：

```
RUN apt-get update
```

CMD 的使用方式跟 RUN 类似，不过在一个 Dockerfile 文件中只能有一个 CMD 指令，如果有多个 CMD 指令，则只有最后一个会生效。该指令为我们运行容器时提供默认的命令，例如：

```
CMD echo "hello shiyanlou"
```

在构建镜像时使用了上面的 `CMD` 指令，则可以直接使用 `docker run image`，该命令等同于 `docker run image echo "hello shiyanlou"`。即作为默认执行容器时默认使用的命令，也可在 `docker run` 中指定需要运行的命令来覆盖默认的 `CMD` 指令。

除此之外，该指令还有一种特殊的用法，在 Dockerfile 中，如果使用了 ENTRYPOINT 指令，则 CMD 指令的值会作为 ENTRYPOINT 指令的参数：

```
CMD ["param1", "param2"]
```

ENTRYPOINT 指令会覆盖 CMD 指令作为容器运行时的默认指令，并且不会在 `docker run` 时被覆盖，如下示例：

```
FROM ubuntu:latest
ENTRYPOINT ["ls", "-a"]
CMD ["-l"]
```

上述构建的镜像，在我们使用 `docker run image` 时等同于 `docker run image ls -a -l` 命令。使用 `docker run image -i -s` 命令等同于 `docker run image ls -a -i -s` 指令。即 CMD 指令的值会被当作 ENTRYPOINT 指令的参数附加到 ENTRYPOINT 指令的后面。

### COPY 和 ADD

COPY 和 ADD 都用于将文件，目录等复制到镜像中。使用方式如下：

```
ADD <src>... <dest>
ADD ["<SRC>",... "<dest>"]

COPY <src>... <dest>
COPY ["<src>",... "<dest>"]
```

`<src>` 可以指定多个，但是其路径不能超出上下文的路径，即必须在跟 Dockerfile 同级或子目录中。

`<dest>` 不需要预先存在，不存在路径时会自动创建，如果没有使用绝对路径，则 `<dest>` 为相对于工作目录的相对路径。

COPY 和 ADD 的不同之处在于，ADD 可以添加远程路径的文件，并且 `<src>` 为可识别的压缩格式，如 gzip 或 tar 归档文件等，ADD 会自动将其解压缩为目录。

### ENV

ENV 指令用于设置环境变量：

```
ENV <key> <value>
ENV <key>=<value> <key>=<value>...
```

### VOLUME

VOLUME 指令将会创建指定的挂载目录，在容器运行时，将创建相应的匿名卷：

```
VOLUME /data1 /data2
```

上述指令将会在容器运行时，创建两个匿名卷，并挂载到容器中的 /data1 和 /data2 目录上。

### EXPOSE

EXPOSE 指定在容器运行时监听指定的网络端口，它与 `docker run`命令的 `-p` 参数不一样，并不实际映射端口，只是将该端口暴露出来，允许外部或其它的容器进行访问。

```
EXPOSE port
```

## 从 Dockerfile 创建镜像

了解了上面一些常用于构建 Dockerfile 的指令之后，可以通过这些指令来构建一个镜像，如下所示，搭建一个 ssh 服务:

```
# 指定基础镜像
FROM ubuntu:14.04

# 安装软件
RUN apt-get update && apt-get install -y openssh-server && mkdir /var/run/sshd

# 添加用户 shiyanlou 及设定密码
RUN useradd -g root -G sudo shiyanlou && echo "shiyanlou:123456" | chpasswd shiyanlou

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
```

首先，我们在之前创建的一个空目录 `dir1` 中编辑 `Dockerfile` 文件，并将上面的内容复制到该文件中，相关的命令如下所示：

```
# 创建目录
$ mkdir dir1 && cd dir1

# 编辑 Dockerfile，将上面的内容写入
$ vim Dockerfile

# 最后执行构建命令
$ docker build -t sshd:test .
```

在上面的命令执行完成之后，该镜像就构建成功了，直接使用该镜像启动一个容器就可以运行一个 ssh 的服务，如下所示：

```
$ docker run -itd -p 10001:22 --rm sshd:test
```

这时就可以通过公网的 IP 地址，以及端口 10001，并且使用用户 `shiyanlou`，密码 `123456`，远程通过 `ssh` 连接到该容器中了。

# 使用 Docker 运行 MongoDB 和 Redis

## Dockerfile 基本框架

### 基本框架

按照上一节学习的内容，我们先完成 Dockerfile 基本框架。

依次输入下面的基本框架内容：

```
# Version 0.1

# 基础镜像
FROM ubuntu:latest

# 维护者信息
MAINTAINER shiyanlou@shiyanlou.com

# 镜像操作命令
RUN echo "deb http://mirrors.cloud.aliyuncs.com/ubuntu/ trusty main universe" > /etc/apt/sources.list
RUN apt-get update && apt-get install -yqq supervisor && apt-get clean

# 容器启动命令
CMD ["supervisord"]
```

上面的 Dockerfile 创建了一个简单的镜像，并使用 `Supervisord` 启动服务。

### 安装SSH服务

首先安装所需要的软件包：

```
RUN apt-get install -yqq openssh-server openssh-client
```

创建运行目录：

```
RUN mkdir /var/run/sshd
```

设置root密码及允许root通过ssh登陆：

```
RUN echo 'root:shiyanlou' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config
```

## 完成 MongoDB Dockerfile

### 安装最新的MongoDB

在 Ubuntu 最新版本下安装 MongoDB 非常简单，参考 [MongoDB安装文档](https://docs.mongodb.org/manual/tutorial/install-mongodb-on-ubuntu/) 。有两种方法：

方法一是添加mongodb的源，执行 `apt-get install mongodb-org` 就可以安装下面的所有软件包：

1.  mongodb-org-server：mongod 服务和配置文件
2.  mongodb-org-mongos：mongos 服务
3.  mongodb-org-shell：mongo shell工具
4.  mongodb-org-tools：mongodump，mongoexport等工具

方法二是下载二进制包，然后解压出来就可以。

由于 MongoDB 的官网连接网速问题，我们使用第二种方案，并把最新的 MongoDB 的包放到阿里云上。

MongoDB 的下载链接如下：

```
http://labfile.oss-cn-hangzhou-internal.aliyuncs.com/courses/498/mongodb-linux-x86_64-ubuntu1404-3.2.3.tgz
```

我们完善 Dockerfile，使用 ADD 命令添加压缩包到镜像：

```
RUN mkdir -p /opt
ADD http://labfile.oss-cn-hangzhou-internal.aliyuncs.com/courses/498/mongodb-linux-x86_64-ubuntu1404-3.2.3.tgz /opt/mongodb.tar.gz
RUN cd /opt && tar zxvf mongodb.tar.gz && rm -rf mongodb.tar.gz
RUN mv /opt/mongodb-linux-x86_64-ubuntu1404-3.2.3 /opt/mongodb
```

创建 MongoDB 的数据存储目录：

```
RUN mkdir -p /data/db
```

将 MongoDB 的执行路径添加到环境变量里：

```
ENV PATH=/opt/mongodb/bin:$PATH
```

MongoDB 和 SSH 对外的端口：

```
EXPOSE 27017 22
```

### 编写`Supervisord`配置文件

添加 `Supervisord` 配置文件来启动mongodb和ssh，创建文件`/home/shiyanlou/shiyanloumongodb/supervisord.conf`，添加以下内容：

```
[supervisord]
nodaemon=true

[program:mongodb]
command=/opt/mongodb/bin/mongod

[program:ssh]
command=/usr/sbin/sshd -D
```

Dockerfile中增加向镜像内拷贝该文件的命令：

```
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
```

### 完整的 Dockerfile

```
# Version 0.1

# 基础镜像
FROM ubuntu:latest

# 维护者信息
MAINTAINER shiyanlou@shiyanlou.com

# 镜像操作命令
RUN echo "deb http://mirrors.cloud.aliyuncs.com/ubuntu/ trusty main universe" > /etc/apt/sources.list
RUN apt-get -yqq update && apt-get install -yqq supervisor
RUN apt-get install -yqq openssh-server openssh-client

RUN mkdir /var/run/sshd
RUN echo 'root:shiyanlou' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

RUN mkdir -p /opt
ADD http://labfile.oss-cn-hangzhou-internal.aliyuncs.com/courses/498/mongodb-linux-x86_64-ubuntu1404-3.2.3.tgz /opt/mongodb.tar.gz
RUN cd /opt && tar zxvf mongodb.tar.gz && rm -rf mongodb.tar.gz
RUN mv /opt/mongodb-linux-x86_64-ubuntu1404-3.2.3 /opt/mongodb

RUN mkdir -p /data/db

ENV PATH=/opt/mongodb/bin:$PATH

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

EXPOSE 27017 22

# 容器启动命令
CMD ["supervisord"]
```

## 完成 Redis Dockerfile

在上述基本的架构下，我们根据需求可以增加新的内容到Dockerfile中，完成 Redis Dockerfile。

进入到 shiyanlouredis 的目录编辑 Dockerfile：

```
cd /home/shiyanlou/shiyanlouredis/
vim Dockerfile
```

### 安装 Redis

由于 MongoDB 中我们已经学习了如何通过二进制压缩包安装最新版本MongoDB的过程，在此安装 Redis 我们直接使用 Ubuntu 源中默认的 Redis 版本。

安装方法非常简单：

```
RUN apt-get install redis-server
```

添加对外的端口号：

```
EXPOSE 6379 22
```

### 编写`Supervisord`配置文件

添加`Supervisord`配置文件来启动 redis-server 和 ssh，创建文件`/home/shiyanlou/shiyanlouredis/supervisord.conf`，添加以下内容：

```
[supervisord]
nodaemon=true

[program:redis]
command=/usr/bin/redis-server

[program:ssh]
command=/usr/sbin/sshd -D
```

Dockerfile中增加向镜像内拷贝该文件的命令：

```
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
```

### 完整的 Dockerfile

```
# Version 0.1

# 基础镜像
FROM ubuntu:latest

# 维护者信息
MAINTAINER shiyanlou@shiyanlou.com

# 镜像操作命令
RUN echo "deb http://mirrors.cloud.aliyuncs.com/ubuntu/ trusty main universe" > /etc/apt/sources.list
RUN apt-get -yqq update && apt-get install -yqq supervisor redis-server
RUN apt-get install -yqq openssh-server openssh-client

RUN mkdir /var/run/sshd
RUN echo 'root:shiyanlou' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

EXPOSE 6379 22

# 容器启动命令
CMD ["supervisord"]
```

## 从 Dockerfile 创建镜像

### 创建 MongoDB 镜像

进入到`/home/shiyanlou/shiyanloumongodb/`目录，执行创建命令。

`docker build` 执行创建，`-t`参数指定镜像名称：

```
docker build -t shiyanloumongodb:0.1 /home/shiyanlou/shiyanloumongodb/
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid13labid1709timestamp1457511284792.png/wm)

`docker images` 查看创建的新镜像已经出现在了镜像列表中：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid13labid1709timestamp1457511294074.png/wm)

由该镜像创建新的容器 mongodb：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid13labid1709timestamp1457511328107.png/wm)

上述 `docker ps` 命令的输出可以看到 MongoDB 的端口号已经被自动映射到了本地的 32768 端口，后续步骤我们对 MongoDB 是否启动进行测试。

打开 Xfce 终端中输入下面的命令连接 mongodb 容器中的服务：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid13labid1709timestamp1457511456998.png/wm)

> 如果提示 `command not found mongo` ，可使用 `sudo apt-get install -y mongdb` 安装。

### 创建 Redis 镜像

进入到`/home/shiyanlou/shiyanlouredis/`目录，执行创建命令。

`docker build` 执行创建，`-t`参数指定镜像名称：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid13labid1709timestamp1457511491952.png/wm)

`docker images` 查看创建的新镜像已经出现在了镜像列表中：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid13labid1709timestamp1457511499237.png/wm)

由该镜像创建新的容器redis：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid13labid1709timestamp1457511508152.png/wm)

上述`docker ps`命令的输出可以看到 redis 的端口号已经被自动映射到了本地的 32769 端口，SSH服务的端口号也映射到了 32770 端口。

打开 Xfce 终端中输入下面的命令连接 redis 容器中的 ssh 和 redis 服务：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid13labid1709timestamp1457511585507.png/wm)

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid13labid1709timestamp1457511592966.png/wm)

> 如果提示 `command not found redis-cli` ，就使用 `sudo apt-get install -y redis-server`安装。