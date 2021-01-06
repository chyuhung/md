# Ubuntu18.04安装部署Devstack

## 准备工作

1、Vmware-workstation，用于部署devstack的虚拟机，隔离本地环境，也方便快照进行回退

​		Vmware-workstack下载地址：https://www.vmware.com/

2、Ubuntu18.04镜像，并在workstation中创建配置好虚拟机，保证连接互联网

​		国内下载速度较快的（清华大学）镜像站地址：https://mirrors.tuna.tsinghua.edu.cn

3、Devstack版本选定，项目地址：https://opendev.org/openstack/devstack.git

## 虚拟机配置

*为保证安装速度，建议配置网络代理，否则可能安装失败*

1、创建stack用户

`sudo useradd -s /bin/bash -d /opt/stack -m stack`

2、提升权限，配置sudo

`echo "stack ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/stack`
`sudo su - stack`

## 安装部署Devstack

1、克隆项目

`git clone https://opendev.org/openstack/devstack`
`cd devstack`

2、复制sample中的示例配置文件到项目下，修改配置文件

`[[local|localrc]]`
`ADMIN_PASSWORD=secret`
`DATABASE_PASSWORD=$ADMIN_PASSWORD`
`RABBIT_PASSWORD=$ADMIN_PASSWORD`
`SERVICE_PASSWORD=$ADMIN_PASSWORD`

3、运行stack.sh开始部署Devstack

`./stack.sh`

