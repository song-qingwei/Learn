[TOC]

### Docker入门与实战

---

#### 一、Docker是什么？产生的背景

> Docker 使用 Google 公司推出的 `Go` 语言进行开发的，基于 Linux 内核的 `cgroup`、`namespace`，以及 AUFS 类的 `Union FS` 等技术，对进程进行封装隔离，属于操作系统层面的虚拟化技术。由于隔离的进程独立于宿主与其他隔离的进程，因此也称其为容器。 
>
> Docker 的思想来自于`集装箱`，集装箱解决了什么问题？在一艘大船上，可以把货物规整的摆放起来。并且各种各样的货物被集装箱标准化了，集装箱和集装箱之间不会互相影响。那么我就不需要专门运送水果的船和专门运送化学品的船了。只要这些货物在集装箱里封装的好好的，那我就可以用一艘大船把他们都运走。
>
> Docker 就是类似的理念。现在都流行云计算了，云计算就好比大货轮。Docker 就是集装箱。

#### 二、Docker工作原理

> ![Docker原理](F:\总结\截图\技术篇\Docker\Docker原理.png)

#### 三、Docker能干什么？

> 因为现在物理服务器是很强大的，我们如果在一台物理服务器上只跑一个服务就浪费了，而同时跑很多服务他们又互相影响，比如说一个服务出了内存泄漏把整个服务器的内存都占满了，其他服务都跟着倒霉。所以要把每个服务都隔离起来，让它们只使用自己那部分有限的cpu，内存和磁盘，以及自己依赖的软件包。这个早先是用虚拟机来实现隔离的，但是每个虚拟机都要装自己的操作系统核心，这是对资源有点浪费。于是就有了Docker, 一个机器上可以装十几个到几十个docker，他们共享操作系统核心，占用资源少，启动速度快。但又能提供了资源（cpu, 内存，磁盘等）的一定程度的隔离。

#### 四、Docker带来哪些好处？

> 1. **更高效的利用系统资源**
>
>    > 由于容器不需要进行硬件虚拟以及运行完整操作系统等额外开销，Docker 对系统资源的利用率更高。无论是应用执行速度、内存损耗或者文件存储速度，都要比传统虚拟机技术更高效。因此，相比虚拟机技术，一个相同配置的主机，往往可以运行更多数量的应用。
>
> 2. **更快速的启动时间**
>
>    > 传统的虚拟机技术启动应用服务往往需要数分钟，而 Docker 容器应用，由于直接运行于宿主内核，无需启动完整的操作系统，因此可以做到秒级、甚至毫秒级的启动时间。大大的节约了开发、测试、部署的时间。
>
> 3. **一致的运行环境**
>
>    > 开发过程中一个常见的问题是环境一致性问题。由于开发环境、测试环境、生产环境不一致，导致有些 bug 并未在开发过程中被发现。而 Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性，从而不会再出现 *「这段代码在我机器上没问题啊」* 这类问题。
>
> 4. **持续交互与部署**
>
>    > 对开发和运维（DevOps）人员来说，最希望的就是一次创建或配置，可以在任意地方正常运行。
>    >
>    > 使用 Docker 可以通过定制应用镜像来实现持续集成、持续交付、部署。开发人员可以通过 Dockerfile  来进行镜像构建，并结合 持续集成(Continuous Integration) 系统进行集成测试，而运维人员则可以直接在生产环境中快速部署该镜像，甚至结合 持续部署(Continuous Delivery/Deployment) 系统进行自动部署。
>
> 5. **更轻松的迁移**
>
>    > 由于 Docker 确保了执行环境的一致性，使得应用的迁移更加容易。Docker 可以在很多平台上运行，无论是物理机、虚拟机、公有云、私有云，甚至是笔记本，其运行结果是一致的。因此用户可以很轻易的将在一个平台上运行的应用，迁移到另一个平台上，而不用担心运行环境的变化导致应用无法正常运行的情况。
>
> 6. **更轻松的维护和扩展**
>
>    > Docker 使用的分层存储以及镜像的技术，使得应用重复部分的复用更为容易，也使得应用的维护更新更加简单，基于基础镜像进一步扩展镜像也变得非常简单。此外，Docker 团队同各个开源项目团队一起维护了一大批高质量的 [官方镜像](https://hub.docker.com/search/?type=image&image_filter=official)，既可以直接在生产环境使用，又可以作为基础进一步定制，大大的降低了应用服务的镜像制作成本。
>
>    **与传统虚拟机对比总结**
>
>    |    特性    |        容器        |   虚拟机   |
>    | :--------: | :----------------: | :--------: |
>    |    启动    |        秒级        |   分钟级   |
>    |  硬盘使用  |     一般为 MB      | 一般为 GB  |
>    |    性能    |      接近原生      |    弱于    |
>    | 系统支持量 | 单机支持上千个容器 | 一般几十个 |

#### 五、传统虚拟机与Docker比较

![传统虚拟机](F:\总结\截图\技术篇\Docker\传统虚拟化.png)

![Docker](F:\总结\截图\技术篇\Docker\Docker.png)

> 传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便。

#### 六、Docker 安装

[Docker英文官网](https://www.docker.com/)	[Docker中文官网](https://www.docker-cn.com/)	[Docker文档](https://docs.docker.com/)	[Docker镜像](https://hub.docker.com)

**Docker 版本：** DockerCE(社区版)和DockerEE(企业版)。

**Docker 安装**

> 1. **卸载旧版本**
>
>    ```CQL
>    sudo yum remove docker \
>                      docker-client \
>                      docker-client-latest \
>                      docker-common \
>                      docker-latest \
>                      docker-latest-logrotate \
>                      docker-logrotate \
>                      docker-selinux \
>                      docker-engine-selinux \
>                      docker-engine
>    ```
>
> 2. **安装社区版**
>
>    > 1. **设置存储库**
>    >
>    >    ```
>    >    sudo yum install -y yum-utils \
>    >      device-mapper-persistent-data \
>    >      lvm2
>    >    ```
>    >
>    >    ```
>    >    sudo yum-config-manager \
>    >        --add-repo \
>    >        https://download.docker.com/linux/centos/docker-ce.repo
>    >    ```
>    >
>    > 2. **安装 Docker CE**
>    >
>    >    默认安装最新版本
>    >
>    >    ```un
>    >    sudo yum -y install docker-ce
>    >    ```
>    >
>    >    安装指定版本
>    >
>    >    ```
>    >    yum list docker-ce --showduplicates | sort -r
>    >    sudo yum install docker-ce-<VERSION STRING>
>    >    ```
>    >
>    > 3. **启动 Docker**
>    >
>    >    ```
>    >    sudo systemctl start docker && sudo systemctl enable docker
>    >    ```
>    >
>    > 4. **查看是否启动成功**
>    >
>    >    ```Linu
>    >    docker version
>    >    ```
>    >
>    > 5. **建立 Docker 用户组**
>    >
>    >    默认情况下，Docker命令会使用 Unix socket 与 Docker 引擎通讯。而只有 root 与 docker 用户组的用户才可以访问 Docker 引擎的 Unix socket。处于安全考虑，一般Linux系统不会直接使用 root 用户。因此，更好的做法是将需要使用 docker 的用户加入 docker 用户组。
>    >
>    >    建立 docker 组：
>    >
>    >    ```Linux
>    >    sudo groupadd docker
>    >    ```
>    >
>    >    将当前用户加入 docker 组：
>    >
>    >    ```Linux
>    >    sudo usermod -aG docker $USER
>    >    ```
>    >
>    >    退出当前终端并重新登录，并进行如下测试：
>    >
>    >    ```Linux
>    >    docker run hello-world
>    >    ```

#### 七、Docker常用命令

> 1. **帮助命令**
>
>    > ```
>    > docker version
>    > ```
>    >
>    > ```
>    > docker info
>    > ```
>    >
>    > ```
>    > docker --help
>    > ```
>
> 2. **镜像**
>
>    >**查看镜像列表**
>    >
>    > ```
>    > docker images [options]
>    > 	-a ：列出所有的镜像
>    > 	-q ：只列出镜像ID
>    > 	--digests ：显示摘要信息
>    > 	--no-trunc ：显示完整的镜像描述；
>    > ```
>    >
>    >**搜索镜像列表**
>    >
>    > ```
>    > docker search [镜像名]
>    > 	--filter=stars=30 tomcat ：查询指定镜像名的收藏数大于30的镜像
>    > ```
>    >
>    >**拉取镜像**
>    >
>    > ```
>    > docker pull [镜像名<:TAG>] ：获取一个指定镜像名的镜像(可获取指定版本号的镜像)
>    > ```
>    >
>    >**删除镜像**
>    >
>    > ```
>    > docker rmi -f [镜像ID] ：强制删除单个镜像名
>    > docker rmi -f [镜像名1:TAG1 ... 镜像名2:TAG2] ：强制删除多个镜像名
>    > docker rmi -f $(docker images -q) ：删除全部镜像
>    > ```
>    >
>    >**设置镜像标签**
>    >
>    > ```
>    > docker tag [镜像ID] [镜像名]:[TAG] ：设置镜像标签
>    > ```
>
> 3. **容器**
>
>    >**新建并启动容器**
>    >
>    > ```
>    > docker run [options] IMAGE
>    > 	--name ：为容器指定一个新名称
>    > 	-d ：后台运行容器
>    > 	-i ：交互方式运行
>    > 	-t ：启动一个伪终端
>    > 	-p ：指定端口映射
>    > 	-P ：随机端口映射
>    > 	-v ：将宿主机容器挂载到容器卷中，格式：-v [宿主机容器]:[容器卷容器]
>    > 	--volumes-from ：数据卷容器，格式：docker run -it --name o2 --volumes-from o1 		centos
>    > 如：docker run -it -d -p 8080:8888 -v /mydata:/datacontainer --name t1 tomcat
>    > ```
>    >
>    >**列出所有的容器**
>    >
>    >```
>    >docker ps [options] ：默认显示运行的容器
>    >-a ：显示所有的容器
>    >-l ：显示最近创建的容器
>    >-n ：显示最近n个创建的容器
>    >-q ：只显示容器ID
>    >```
>    >
>    >**退出容器**
>    >
>    >```
>    >exit ：退出并停止容器
>    >ctrl + p + q ：退出不停止容器
>    >```
>    >
>    >**启动容器**
>    >
>    >```
>    >docker start [容器ID或容器名称]
>    >```
>    >
>    >**重启容器**
>    >
>    >```
>    >docker restart [容器ID或容器名称]
>    >```
>    >
>    >**停止容器**
>    >
>    >```
>    >docker stop [容器ID或容器名称]
>    >```
>    >
>    >**删除容器**
>    >
>    >```
>    >docker rm [容器ID]
>    >```
>    >
>    >**强制停止容器**
>    >
>    >```
>    >docker kill [容器ID或容器名称]
>    >```
>    >
>    >**删除多个容器**
>    >
>    >```
>    >docker rm -f $(docker ps -a  -q)
>    >docker ps -a -q | xargs docker rm
>    >```
>    >
>    >**查看日志**
>    >
>    >```
>    >docker logs -f -t --tail [容器ID]
>    >```
>    >
>    >**查看容器的进程**
>    >
>    >```
>    >docker top [容器ID]
>    >```
>    >
>    >**查看容器详细信息**
>    >
>    >```
>    >docker inspect [容器ID]
>    >```
>    >
>    >**进入正在运行的容器，并以前台方式运行**
>    >
>    >```dockerfile
>    >docker exec -i -t [容器ID] ：bashshell 产生新的进程
>    >docker attach [容器ID] ：进入容器，不产生新的进程
>    >## 生产环境通过nsenter进入(推荐)
>    >## 首先查看pid
>    >docker inspect [镜像ID]
>    >## 进入容器
>    >nsenter -t [pid] -m -u -i -n -p
>    >```
>    >
>    >**从容器内拷贝文档到主机**
>    >
>    >```
>    >docker cp [容器名]:[容器中要拷贝的文件名及其路径] [要拷贝到宿主机里面对应的路径]
>    >```
>    >

#### 八、数据卷(volume)和数据卷容器

> ##### 1. 什么是数据卷？为什么需要数据卷？
>
> > 这得从Docker容器的文件系统说起。出于效率等一系列原因，Docker容器的文件系统在宿主机上的存在方式很复杂，这会带来很多问题：
> >
> > 1. 不能在宿主机上很方便的访问容器中的文件；
> > 2. 无法再多个容器之间共享数据；
> > 3. 当容器删除时，容器中产生的数据将丢失；
> >
> > 为了解决这些问题，Docker引入了 **容器卷** 机制。数据卷是存在于一个或多个容器中的特定文件或文件夹，这个文件或文件夹以独立于Docker文件系统的形式存在于宿主机中。数据卷的最大特点是：其生命周期独立于容器的生存周期。
>
> ##### 2. 使用数据卷的最佳场景
>
> > 1. 在多个容器之间共享数据，多个容器可以同时只读或者读写的方式挂载同一个数据卷，从而共享数据卷中的数据；
> > 2. 当宿主机不能保证一定存在某个目录或固定路径的文件时，使用数据卷可以规避这个问题；
> > 3. 当你想把容器中的数据存储在宿主机之外的地方时，比如远程主机上或云存储上；
> > 4. 当你需要把容器数据在不同的宿主机之间备份、恢复或迁移时，数据卷是很好的选择；

#### 九、Dockerfile

> ##### 1. 什么是Dockerfile？
>
> > Dockerfile是用来构建Docker镜像的构建文件；
>
> ##### 2. 执行Dockerfile流程
>
> ##### 3. Dockerfile关键字
>
> > ```dockerfile
> > FROM：基础镜像，也就是说当前镜像是基于FROM的镜像；
> > MAINTAINER：当前维护镜像的组织或者人；
> > RUN：镜像构建时，需要运行的命令；
> > WORKDIR：容器创建后默认所在目录；
> > EXPOSE：当前容器对外暴露的端口；
> > ENV：在构建镜像时设置的环境变量；
> > ADD：将宿主机目录下的文件copy进镜像，且ADD命令会解压压缩包；
> > COPY：拷贝；
> > VOLUME：容器数据卷，用于保存数据持久化；
> > CMD：指定容器启动过程中需要运行的命令；多条CMD命令，只有最后一条生效，CMD命令会被 docker run 	后面的参数替换；
> > ENTRYPOINT：指定容器启动过程中需要运行的命令；会把 docker run 后面的参数追加到后面；
> > ONBUILD：
> > ```
>
> ##### 4. 手动构建一个 tomcat9 的镜像
>
> > Dockerfile
> >
> > > ```dockerfile
> > > FROM            centos
> > > MAINTAINER      song_qingwei@sina.com
> > > 
> > > ADD apache-tomcat-9.0.14.tar.gz /usr/local/
> > > ADD jdk-8u192-linux-x64.tar.gz /usr/local/
> > > ADD crmv.war /usr/local/apache-tomcat-9.0.14/webapps
> > > 
> > > RUN yum -y install vim
> > > 
> > > ENV MYPATH /usr/local/
> > > 
> > > WORKDIR $MYPATH
> > > 
> > > ENV JAVA_HOME /usr/local/jdk1.8.0_192
> > > ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
> > > ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.14
> > > ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.14
> > > ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
> > > 
> > > EXPOSE 8080
> > > 
> > > CMD /usr/local/apache-tomcat-9.0.14/bin/startup.sh && tail -f /usr/local/apache-tomcat-9.0.14/logs/catalina.out
> > > ```
> >
> > 构建镜像
> >
> > > ```dockerfile
> > > docker build -f [Dockerfile路径] -t [构建后的镜像名称] [执行路径（当前路径可以为'.'）]
> > > 如：docker build -f /opt/mydockerfile/Dockerfile -t tomcat9 /opt/mydockerfile
> > > ```
> >
> > 启动镜像
> >
> > > ```
> > > docker run -d --name myTomcat9 -p 8080:8080 \
> > > -v /mydata/tomcat9/test:/usr/local/apache-tomcat-9.0.14/test \
> > > -v /mydata/tomcat9/logs:/usr/local/apache-tomcat-9.0.14/logs \
> > > --privileged=true tomcat9
> > > ```
> >
> > 检测是否启动成功
> >
> > > http://127.0.0.1:8080
> >
> >

#### 十、Docker网络介绍

> ##### 1. 网络分层
>
> ##### 2. 公网IP和私网IP
>
> > **公网IP：** 互联网唯一标识，可以访问Internet；
> >
> > **私网IP：** 不可以在互联网上使用，只能用于内部网络；
> >
> > > A类：10.0.0.0 ～10.255.255.255（子网掩码：10.0.0.0/8）
> > >
> > > B类：172.16.0.0 ～172.31.255.255（子网掩码：172.16.0.0/12）
> > >
> > > C类：192.168.0.0.0 ～192.168.255.255（子网掩码：192.168.0.0/16）
>
> ##### 3. Docker网络分类
>
> > **单机**
> >
> > > Bridge Network：桥接网络
> > >
> > > Host Network：共享宿主机网络
> > >
> > > None Network：无网络
> >
> > **多机**
> >
> > > Overlay Network

#### 十一、Docker体系架构

> ![Docker架构体系](F:\总结\截图\技术篇\Docker\Docker架构体系.png)

