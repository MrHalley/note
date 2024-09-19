# Docker入门



> 安装 [Install Docker Engine on CentOS | Docker Docs](https://docs.docker.com/engine/install/centos/)

```bash
# 卸载历史docker
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
#安装必须的依赖
sudo yum install -y yum-utils \
device-mapper-persistent-data \
lvm2

#设置 docker repo 的 yum 位置
sudo yum-config-manager \
--add-repo \
https://download.docker.com/linux/centos/docker-ce.repo

//国外镜像
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
//阿里镜像
https://mirrors.aliyun.com/docker-ce/linux/centos/gpg
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

#安装 docker，以及 docker-cli
sudo yum install docker-ce docker-ce-cli containerd.io
# docker安装问题(因为切换yum后没有执行yum clean all，原来的yum没对应软件包，也下不了)
https://blog.csdn.net/m0_67391270/article/details/126643616

# 查看源中可使用版本
yum list docker-ce --showduplicates | sort -r
 
# 配置开机启动项
systemctl start docker
systemctl enable docker
docker version

# 开机自启动
sudo systemctl enable docker
```

