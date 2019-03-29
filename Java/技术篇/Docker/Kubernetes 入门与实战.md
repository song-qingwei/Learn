### `Kubernetes`入门与实战

---

#### 一、了解应用部署运行模式变迁

![应用部署运行模式变迁](F:\总结\截图\技术篇\Docker\应用部署运行模式变迁.jpg)

早在2000年的时候，IBM与SUN公司风靡的是小型机和大型机，所以是物理机奉行的时代；

随着时代的发展，物理机的资源耗尽太厉害了，在2001~2009年时候，就开始了虚拟化的时代，出现的虚拟化技术有：`Vmware`、`KVM`、`OpenStack`;

直到2013年进入了容器化技术时代，出现的容器化技术代表有：`Docker`、`Lxc`、`cgroup`、`Union FS`;

从2015年至今，进入云原生初期阶段，有：`微服务`、`持续集成`、`编排服务`……

#### 二、了解容器编排技术

所谓的容器编排技术就是怎么样调度这些容器，按照一定的规则去编排这些容器；

![Swarm](F:\总结\截图\技术篇\Docker\Swarm.jpg)

#### 三、什么是`Kubernete`？

1. 面向云原生应用的新平台；

2. `kubernetes`是以`google`内部容器编排管理平台Borg为原型的开源实现；

   - 容器编排管理平台

     以容器组为基本的编排和调度单元；资源配额与分配管理、健康检查、自愈、伸缩与滚动升级；

   - 微服务支撑平台

     服务发现、服务编排与路由支持；服务快速部署和自动负载均衡；

   - 可移植的云平台

     新一代应用云化的事实标准，称为面向云原生的新的可移植层，为用户提供简单且一致的容器应用部署、伸缩管理机制；支持跨云迁移；

#### 四、`Kubernetes`环境搭建

1. 首先安装Docker；

2. `kubectc`安装，[minikube](https://github.com/kubernetes/minikube)、[最难的安装](https://github.com/kelseyhightower)，此次采用`minikube`安装；

   因为墙的原因，需要修改yum源，建议使用阿里源的仓库，添加`kubernetes.repo`仓库：

   ```
   cat <<EOF > /etc/yum.repos.d/kubernetes.repo
   [kubernetes]
   name=Kubernetes
   baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
   enabled=1
   gpgcheck=0
   repo_gpgcheck=0
   gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
   EOF
   ```

   ```
   yum -y install kubelet kubeadm kubectl kubernetes-cni socat
   ```

   

3. 

#### 五、深入理解`Kubernetes`架构和核心组件

#### 六、了解`Kubernetes`基础概念