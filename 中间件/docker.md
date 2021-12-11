##  虚拟机与容器

很明显可以看出两者在操作系统级别上的隔离和进程上的隔离的区别，VM因为隔离级别更高明显更重。

![2.png](http://dockone.io/uploads/article/20190701/71387961c24673bbc9ec8eda5361bae3.png)

#### linux容器主要技术特点：

文件系统隔离：每个容器都有自己的root文件系统
进程隔离：每个容器都运行在自己的进程环境中
网络隔离：容器件的虚拟网络接口和IP地址都是分开的
资源隔离和分组：使用cgroup将CPU和内存之类的资源独立分配给每个容器

#### windows容器主要特点：
（https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/deploy-containers/system-requirements）

|                    | 虚拟机                                                       | 容器                                                         |
| :----------------- | :----------------------------------------------------------- | ------------------------------------------------------------ |
| 隔离               | 提供与主机操作系统和其他 VM 的完全隔离。 当强安全边界很关键时（例如，在同一台服务器或群集上托管来自竞争性公司的应用时），这很有用。 | 通常提供与主机和其他容器的轻度隔离，但不提供与 VM 一样强的安全边界。 （可以使用 [Hyper-V 隔离模式](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/manage-containers/hyperv-container)隔离轻型 VM 中的每个容器，从而提高安全性。） |
| 操作系统           | 运行包含内核的完整操作系统，因此需要更多的系统资源（CPU、内存和存储）。 | 运行操作系统的用户模式部分，可以对其进行定制，使之只包含应用所需的服务，减少所使用的系统资源。 |
| 来宾兼容性         | 运行虚拟机内的几乎任何操作系统                               | 在[与主机相同的操作系统版本](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/deploy-containers/version-compatibility)上运行（Hyper-V 隔离使你能够在轻型 VM 环境中运行同一 OS 的早期版本） |
| 部署               | 使用 Windows Admin Center 或 Hyper-V 管理器部署单个 VM；使用 PowerShell 或 System Center Virtual Machine Manager 部署多个 VM。 | 通过命令行使用 Docker 部署单个容器；使用 Azure Kubernetes 服务等业务流程协调程序部署多个容器。 |
| 操作系统更新和升级 | 在每个 VM 上下载并安装操作系统更新。 安装新的操作系统版本需要升级；通常情况下，直接创建全新 VM。 这样可能很耗时，尤其是在有大量 VM 的情况下... | 在容器中更新或升级操作系统文件的操作是相同的： 编辑容器映像的生成文件（称为 Dockerfile），使之指向最新版 Windows 基础映像。用这个新的基础映像重新生成容器映像。将容器映像推送到容器注册表。使用业务流程协调程序重新进行部署。 业务流程协调程序提供的强大的自动化功能允许大规模这样做。 有关详细信息，请参阅[教程：在 Azure Kubernetes 服务中更新应用程序](https://docs.microsoft.com/zh-cn/azure/aks/tutorial-kubernetes-app-update)。 |
| 持久性存储         | 对单个 VM 使用进行本地存储的虚拟硬盘 (VHD)，或对多个服务器共享的存储使用 SMB 文件共享 | 使用 Azure 磁盘作为单个节点的本地存储，或将 Azure 文件存储（SMB 共享）用于由多个节点或服务器共享的存储。 |
| 负载平衡           | 虚拟机负载均衡将运行中的 VM 移动到故障转移群集中的其他服务器。 | 容器本身不移动，而是由业务流程协调程序在群集节点上自动启动或停止容器，以管理负载和可用性方面的更改。 |
| 容错               | VM 可以故障转移到群集中的另一台服务器，并在新服务器上重启 VM 的操作系统。 | 如果某个群集节点发生故障，则在该节点上运行的所有容器都将在另一个群集节点上由业务流程协调程序快速重新创建。 |
| 网络               | 使用虚拟网络适配器。                                         | 使用虚拟网络适配器的隔离视图，在减少使用资源的同时，稍微减少提供的虚拟化 – 主机的防火墙与容器共享。 有关详细信息，请参阅 [Windows 容器网络](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/container-networking/architecture)。 |

#### windows授权机制：

<img src="http://devopshub.cn/wp-content/uploads/2017/02/windows-container-4.png" alt="windows-container-4" style="zoom:200%;" />

---------------

## docker 入门：

这篇文章对于docker入门，在docker架构方面的了解有很大的帮助，此等级标题下均为该文章内容。

https://www.cnblogs.com/ECJTUACM-873284962/p/9789130.html

![img](https://img2018.cnblogs.com/blog/1100338/201810/1100338-20181010205425908-509725301.jpg)

![docker-framework](https://img2018.cnblogs.com/blog/1100338/201810/1100338-20181011200343656-1972949758.png)

![img](https://img2018.cnblogs.com/blog/1100338/201810/1100338-20181012214101184-1339527466.jpg)

![img](https://img2018.cnblogs.com/blog/1100338/201810/1100338-20181014202945937-1677031749.png)

Dockerfile是什么：

前面我们已经提到了 `Docker` 的一些基本概念。以 `CTF` 选手的角度来看，我们可以去使用 `Dockerfile` 定义镜像，依赖镜像来运行容器，可以去模拟出一个真实的漏洞场景。因此毫无疑问的说， `Dockerfile` 是镜像和容器的关键，并且 `Dockerfile` 还可以很轻易的去定义镜像内容，说了这么多，那么 `Dockerfile` 到底是个什么东西呢？

`Dockerfile` 是自动构建 `docker` 镜像的配置文件， 用户可以使用 `Dockerfile` 快速创建自定义的镜像。`Dockerfile` 中的命令非常类似于 `linux` 下的 `shell` 命令。

---------

## 关于不同版本的内核的对于镜像的影响

​	Docker镜像只是一个自定义文件/目录结构，通过一个或多个Dockerfiles的FROM和RUN指令以层的形式进行组装，并带有一些元数据（例如：打开哪个端口或在容器启动时执行哪个文件），如果内核可以运行守护程序（docker daemon），一般代表具有运行的一些通用API，这时**如果镜像中包含依赖内核最新的一些功能的软件将无法运行**，但因为通用的API足以支持程序的最初启动，Docker并不会阻止，因为它不在乎镜像中的内容和用哪个内核的版本来启动镜像，其实一般本机的操作系统都是较新的（若对旧的内核的一些功能的引用没用导致操作系统的BUG或被黑客攻击的风险，一般到新的操作系统也会保留），所以，问题并不常见。

**OS **    =内核+文件系统/库 

**镜像** =文件系统/库

------------------

## 关于docker for windows 10 和 docker for Linux 

   **先下结论，若是为了运行docker，由于docker引擎是在Linux环境上开发的，所以在Linux上肯定是效率更高的，但是在windows上的docker与Linux其实在hyper - v改善后难以看出明显的差距。容器是一项存在很久的技术了，docker的精髓在将所需要的文件系统、依赖打包成了统一的一层docker image,结合微服务的思想的出现，适合于快速的开发交付，才得以发光。
   docker = LXC（container）+docker image；
   hyper 技术主要是为了保护共享基于LXC和windows container而诞生的（Linux和windows均可采用相同原理的技术）：
   hyper = VM(轻型，没有完整的操作系统下文链接有相关讲述)+docker image;**

​	在Windows server 2016上，微软提供了两种容器，Windows Server Container 和Hyper -V Container ，两者执行相同的操作并执行相同的方式管理，但是它们的隔离级别不同， 区别在于，在运行 Hyper-V 容器的映像中创建容器的方式需要使用其他参数。Windows Server Container 和Linux Container（LIinux容器由来：https://developer.aliyun.com/article/745446） 一样，容器与底层操作系统共享内核，所以它们会很轻量而且运行迅速。当你在容器中启动一个进程的时候，**这个进程实际上运行在宿主机上**，你可以使用任务管理器或者Powershell 命令 Get-Process 获取到这个进程的信息。**这使得它们比VM小，因为它们每个都不需要操作系统的副本。**但是，安全性可能会成为一个问题，因为**如果一个容器遭到破坏，则OS和所有其他容器都将受到威胁**。Windows 10上虽然也提供了容器服务，但是只能运行Hyper-V Contianer。

​	Linux是依据其操作系统的CGroup（https://www.ibm.com/developerworks/cn/linux/1506_cgroup/index.html）进行进程级别的分离，即LXC(Linux Container)，而windows因为没有像Linux的开源性那么方便，但也提供了Windows Server Container 以提供跟LXC一样的功能，但都有共享同个内核所涉及的安全性问题，所以windows在win10上只Hyper-V容器所提供的进程隔离与虚拟机与物理机的隔离保证安全性，而且docker的Engine需要Linux，所以windows环境下运行docker本就需要Linux的虚拟环境。

​	**Windows 映像仅能在 Windows 主机上运行（可能是Linux本来就快，而且windows有电脑的人基本都有，所以没必要去Linux上测试，还不如在windows程序中运行，Linux镜像可以在windows上运行可能是为了能一起开发测试用吧），Linux 映像可以在 Linux 主机和 Windows 主机上运行（在 Hyper-V 上运行的基于 [LinuxKit](https://github.com/linuxkit/linuxkit) 的虚拟机在 Windows 桌面上运行 Linux 容器）（https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/deploy-containers/linux-containers）**，**其中主机是指服务器或 VM。**因为Windows用到docker daemon 时，其Engine是依据Linux启动的，所以我们运行Linux的镜像时仍是通过Hyper-V虚拟机实现了Linux 环境，并且提供了Hyper-V的专用容器而已。所以一般在windows进行开发与测试

​	当然，微软这么大的一家公司，不会这么将就，查资料时发现Hyper 是一项轻量级VM+docker image 的技术，可以用来解决LXC共享内核所带来的安全问题：其中涉及到进程隔离和Hyper -V隔离（下面三幅图为微软官网对于容器区别的展示），Linux可能也用Hyper技术实现隔离，但是若是在windows中运行Linux镜像，这时已经通过Hyper-V上的LinuxKit虚拟机上运行了host，就不需要在上面再加一层了，直接用LXC即可。

![Moby VM 作为容器主机](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/deploy-containers/media/mobyvm.png)

#### 进程隔离

这是容器的“传统”隔离模式，[Windows 容器概述](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/about/)中介绍了这种模式。 使用进程隔离时，可以通过命名空间、资源控制以及进程隔离技术进行隔离，这样多个容器实例就可以同时在给定主机上运行。 在此模式下运行时，容器与主机之间以及容器与容器之间会共享同一个内核。 这大致与 Linux 容器的运行方式相同。

![img](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/manage-containers/media/container-arch-process.png)

#### Hyper-V 隔离

此隔离模式在主机和容器版本之间提供增强的安全性和更广泛的兼容性。 使用 Hyper-V 隔离时，多个容器实例在主机上并发运行；但是，每个容器在高度优化的虚拟机中运行，并有效地获得自己的内核。 由于虚拟机的存在，因此可以在每个容器之间以及容器与容器主机之间进行硬件级别的隔离。

![img](https://docs.microsoft.com/zh-cn/virtualization/windowscontainers/manage-containers/media/container-arch-hyperv.png)

#### 两者的区别

![windows-container-1](http://devopshub.cn/wp-content/uploads/2017/02/windows-container-1.png)

* 至于两者的由来以及与Docker的区别，可以看下面两篇文章（包括Hyper在如何轻量级的思路：1加快容器启动速度（精简VM配置和内核、VM Cache功能） 2.降低内存开销）：

  > http://dockone.io/article/1570
  >
  > https://cloud.tencent.com/developer/article/1042662

--------



## 容器编排平台

Swarm是Docker提供的容器编排平台，从1.12版本开始，任何的服务器节点都可以加入Swarm集群，这同样适用于Windows服务器。因此，你可以在一个Swarm集群中混合部署Windows和Linux节点，虽然不同的操作系统节点上只能运行对应的容器，但是它们都可以通过**swarm network进行通讯**，构建一个完整的应用。

对于微服务拆分来说，可以构建跨平台的分布式应用非常具有吸引力。如果你的应用现在是一个传统的asp.net单体应用，你可以先采用microsoft/windowservercore镜像对整个单体应用进行容器化部署，然后逐步的将其中的某些组件进行拆分，使用microsoft/nanoserver上的.net core来运行这些微服务组件，你甚至可以引入nginx作为你的反向代理服务器，并将其运行在linux服务器节点上。

![windows-container-3](http://devopshub.cn/wp-content/uploads/2017/02/windows-container-3.png)

-------------

## 实用中的使用情况

两篇文章对一些其中的基本使用情况有了较好的概述：

https://www.cnblogs.com/daxnet/p/7719574.html

https://www.zhihu.com/question/51134842

## docker 基本命令

`docker主机(Host)`：安装了Docker程序的机器（Docker直接安装在操作系统之上）；

`docker客户端(Client)`：连接docker主机进行操作；

`docker仓库(Registry)`：用来保存各种打包好的软件镜像；

`docker镜像(Images)`：软件打包好的镜像；放在docker仓库中；

`docker容器(Container)`：镜像启动后的实例称为一个容器；容器是独立运行的一个或一组应用

![img](https://cdn.static.note.zzrfdsn.cn/images/springboot/assets/20180303165113.png)

使用Docker的步骤：

1. 确认要安装docker的系统的linux内核高于`3.10`，低于3.10使用`yum update`更新

   ```shell
   uname -r
   ```

2. 安装docker

   ```shell
   yum install docker
   ```

3. 查看docker版本

   ```shell
   docker -v
   ```

4. 查看docker状态

   ```shell
   service docker status
   ```

5. 启动docker

   ```shell
   service docker start
   ```

6. 停止docker

   ```shell
   service docker stop
   ```

7. 设置docker开机自启

   ```shell
   systemctl enable docker
   ```



#### 修改镜像源

修改 /etc/docker/daemon.json ，写入如下内容（如果文件不存在请新建该文件）

```
vim /etc/docker/daemon.json

#　内容：

{
"registry-mirrors":["http://hub-mirror.c.163.com"]
}
```

| 国内镜像源        | 地址                                                         |
| ----------------- | ------------------------------------------------------------ |
| Docker 官方中国区 | [https://registry.docker-cn.com](https://registry.docker-cn.com/) |
| 网易              | [http://hub-mirror.c.163.com](http://hub-mirror.c.163.com/)  |
| 中国科技大学      | [https://docker.mirrors.ustc.edu.cn](https://docker.mirrors.ustc.edu.cn/) |
| 阿里云            | [https://pee6w651.mirror.aliyuncs.com](https://pee6w651.mirror.aliyuncs.com/) |

#### 添加镜像

**以tomcat为例：**

1. 下载tomcat镜像

   ```shell
   docker pull tomcat
   ```

   如需选择具体版本，可以在https://hub.docker.com/搜索tomcat

   ```shell
   docker pull tomcat:7.0.96-jdk8-adoptopenjdk-hotspot
   ```

2. 根据镜像启动容器，不加TAG默认latest，如果没有下载latest会先去下载再启动

   ```shell
   docker run --name mytomcat -d tomcat:latest
   ```

   `--name`：给容器起个名字

   `-d`：后台启动，不加就是前端启动，然后你就只能开一个新的窗口连接，不然就望着黑乎乎的窗口，啥也干不了，`Ctrl+C`即可退出，当然，容器也会关闭

3. 查看运行中的容器

   ```shell
   docker ps
   ```

4. 停止运行中的容器

   ```shell
   docker stop  容器的id
   
   # 或者
   
   docker stop  容器的名称，就是--name给起的哪个名字
   ```

5. 查看所有的容器

   ```shell
   docker ps -a
   ```

6. 启动容器

   ```shell
   docker start 容器id/名字
   ```

7. 删除一个容器

   ```shell
   docker rm 容器id/名字
   ```

   删除所有容器

   ```bash
   docker rm $(docker ps -a -q)	
   ```

8. 启动一个做了端口映射的tomcat

   ```shell
    docker run -d -p 8888:8080 tomcat
   ```

   `-d`：后台运行 `-p`: 将主机的端口映射到容器的一个端口 `主机端口(8888)`:`容器内部的端口(8080)`

   外界通过主机的8888端口就可以访问到tomcat，前提是8888端口开放

9. 关闭防火墙

   ```shell
   # 查看防火墙状态
   service firewalld status
   
   # 关闭防火墙
   service firewalld stop
   ```

10. 查看容器的日志

    ```shell
    docker logs 容器id/名字
    ```

**以mysql为例：**

```shell
# 拉取镜像
docker pull mysql:5.7.28

# 运行mysql容器
 docker run --name mysql -e MYSQL_ROOT_PASSWORD=root -d mysql:5.7.28
```

`--name mysql`：容器的名字是mysql；

`MYSQL_ROOT_PASSWORD=root`：root用户的密码是root (必须指定)

连接容器内mysql

在使用 **-d** 参数时，容器启动后会进入后台。此时想要进入容器，可以通过以下指令进入：

- **docker attach**
- **docker exec**：推荐使用 docker exec 命令，因为此退出容器终端，不会导致容器的停止。

```shell
docker exec -it mysql bash
```

`-i`: 交互式操作。

`-t`: 终端。

`mysql`: 名为mysql的 镜像。

`bash`：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash，也可以用`/bin/bash`。

连接上以后就可以正常使用mysql命令操作了

```shell
mysql -uroot -proot
```

直接使用端口映射更加方便

```shell
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql:5.7.28
```

#### run

### 语法

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

OPTIONS说明：

- **-a stdin:** 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；

- **-d:** 后台运行容器，并返回容器ID；

- **-i:** 以交互模式运行容器，通常与 -t 同时使用；

- **-P:** 随机端口映射，容器内部端口**随机**映射到主机的端口

- **-p:** 指定端口映射，格式为：**主机(宿主)端口:容器端口**

- **-t:** 为容器重新分配一个伪输入终端，通常与 -i 同时使用；

- **--name="nginx-lb":** 为容器指定一个名称；

- **--dns 8.8.8.8:** 指定容器使用的DNS服务器，默认和宿主一致；

- **--dns-search example.com:** 指定容器DNS搜索域名，默认和宿主一致；

- **-h "mars":** 指定容器的hostname；

- **-e username="ritchie":** 设置环境变量；

- **--env-file=[]:** 从指定文件读入环境变量；

- **--cpuset="0-2" or --cpuset="0,1,2":** 绑定容器到指定CPU运行；

- **-m :**设置容器使用内存最大值；

- **--net="bridge":** 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；

- **--link=[]:** 添加链接到另一个容器；

- **--expose=[]:** 开放一个端口或一组端口；

- **--volume , -v:** 绑定一个卷

  > -v=[]:绑定挂载目录
  >
  > 宿主机绑定: -v<host>:<container>:[rw|ro]
  >
  > 在Docker中新建一个共享的卷: -v /<container>
  >
  > sudo docker run --rm-i -t -v /home/hyzhou/docker:/data:rw ubuntu:14.04 /bin/bash
  >
  > 将本机的/home/hyzhou/docker，挂载到镜像中的/data目录

## 杂症

#### docker 启动镜像，容器会自动退出的解决办法

**原因**

Docker容器后台运行,就必须有一个前台进程.容器运行的命令如果不是那些一直挂起的命令（比如运行top，tail），就是会自动退出的。

**解决办法**

在启动脚本里面增加一个执行进程：

tail -f /dev/null

```
tail -f /dev/null
```

如果是别人的镜像你不想修改，可以用-dit参数

```
docker run -dit --name ubuntu2 ubuntu
```

或 

```
docker run -d --name ubuntu ubuntu /bin/bash -c "tail -f /dev/null"
```

## 阿里云docker访问不了 

1. docker container 暴露端口到服务器上
2. 设置安全组
3. 添加安全组管理实例（不知道为啥我当时一直找不到实例，在概述点了一下服务器链接进去后突然刷了出来）

## dockerfile 与 docker compose

镜像构建：即创建一个镜像，它包含安装运行所需的环境、程序代码等。这个创建过程就是使用 dockerfile 来完成的。

> 写个脚本把安装过程全部记录下来，这样再次安装的时候，执行脚本就行了。 Dockerfile 就是这样的脚本，它记录了一个镜像的制作过程。 

容器启动：容器最终运行起来是通过拉取构建好的镜像，通过一系列运行指令（如端口映射、外部数据挂载、环境变量等）来启动服务的。针对单个容器，这可以通过 docker run 来运行。

而如果涉及多个容器的运行（如服务编排）就可以通过 docker-compose 来实现，它可以轻松的将多个容器作为 service 来运行（当然也可仅运行其中的某个），并且提供了 scale (服务扩容) 的功能。

有些教程用了 dockerfile+docker-compose, 是因为 docker-compose.yml 本身没有镜像构建的信息，如果镜像是从 docker registry 拉取下来的，那么 Dockerfile 就不需要；如果镜像是需要 build 的，那就需要提供 Dockerfile.

docker-compose是编排容器的。例如，你有一个php镜像，一个mysql镜像，一个nginx镜像。如果没有docker-compose，那么每次启动的时候，你需要敲各个容器的启动参数，环境变量，容器命名，指定不同容器的链接参数等等一系列的操作，相当繁琐。而用了docker-composer之后，你就可以把这些命令一次性写在docker-composer.yml文件中，以后每次启动这一整个环境（含3个容器）的时候，你只要敲一个docker-composer up命令就ok了。

dockerfile的作用是从无到有的构建镜像。它包含安装运行所需的环境、程序代码等。这个创建过程就是使用 dockerfile 来完成的。Dockerfile - 为 docker build 命令准备的，用于建立一个独立的 image ，在 docker-compose 里也可以用来实时 build 
docker-compose.yml - 为 docker-compose 准备的脚本，可以同时管理多个 container ，包括他们之间的关系、用官方 image 还是自己 build 、各种网络端口定义、储存空间定义等

简而言之， Dockerfile 记录单个镜像的构建过程， docker-compse.yml 记录一个项目(project, 一般是多个镜像)的构建过程。