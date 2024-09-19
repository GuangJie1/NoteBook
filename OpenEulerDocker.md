# openEuler容器镜像使用指南

当前openEuler社区除过基础镜像之外，已经发布和上线了20+核心的开源应用镜像，本文着重分享openEuler基础镜像的安装和使用的初步实践，如果您对openEuler社区其他镜像感兴趣，欢迎大家使用和体验。

## 基础镜像简介

   1. 容器镜像仓库
openEuler官方容器镜像仓库，包含openEuler基础镜像、应用镜像。在这里，你可以找到相应镜像的使用和介绍。

       - [openeuler-docker-images](https://gitee.com/openeuler/openeuler-docker-images)
   
   2. 基础镜像地址
openEuler的基础镜像官方仓库
      - [repo.openeuler.org](https://repo.openeuler.org/)

   3. 基础镜像版本
         - [20.03-lts](https://repo.openeuler.org/openEuler-20.03-LTS/docker_img/)
         - [20.03-lts-sp1](https://repo.openeuler.org/openEuler-20.03-LTS-SP1/docker_img/)
	  - [20.03-lts-sp2](https://repo.openeuler.org/openEuler-20.03-LTS-SP2/docker_img/)
	  - [20.03-lts-sp3](https://repo.openeuler.org/openEuler-20.03-LTS-SP3/docker_img/)
	  - [20.03-lts-sp4, 20.03](https://repo.openeuler.org/openEuler-20.03-LTS-SP4/docker_img/)
	  - [20.09](https://archives.openeuler.openatom.cn/openEuler-20.09/docker_img/)
	  - [21.03](https://archives.openeuler.openatom.cn/openEuler-21.03/docker_img/)
	  - [21.09](https://archives.openeuler.openatom.cn/openEuler-21.09/docker_img/)
	  - [22.03-lts](https://repo.openeuler.org/openEuler-22.03-LTS/docker_img/)
	  - [22.09](https://archives.openeuler.openatom.cn/openEuler-22.09/docker_img/)
	  - [22.03-lts-sp1](https://repo.openeuler.org/openEuler-22.03-LTS-SP1/docker_img/)
	  - [22.03-lts-sp2](https://repo.openeuler.org/openEuler-22.03-LTS-SP2/docker_img/)
	  - [22.03-lts-sp3, 22.03, latest](https://repo.openeuler.org/openEuler-22.03-LTS-SP3/docker_img/)
	  - [23.03](https://repo.openeuler.org/openEuler-23.03/docker_img/)
	  - [23.09](https://repo.openeuler.org/openEuler-23.09/docker_img/)
	  - [24.03-lts](https://repo.openeuler.org/openEuler-24.03-LTS/)
## 镜像仓库
基础镜像和应用镜像支持的版本会发布到以下平台的镜像仓库，供开发者下载和使用。
   - [hub.docker.com](https://hub.docker.com/)
   - [quay.io](https://quay.io/)
   - [hub.oepkgs.net](https://hub.oepkgs.net/)

## 镜像部署流程
### 1.  准备环境
* windows系统需要准备一台虚拟机
* mac系统可以使用自带的shell终端

### 2.  部署docker

```bash
#1、执行docker安装命令，已安装docker或下载docker客户端，跳过
dnf -y install docker  # 虚拟机安装docker示例, mac系统需自行安装

# 2、docker安装成功后，如docker安装成功，可以看到安装的版本
docker version 
```
![5723f7ca5eb6c24d24bd3ad6a60cda8|608x354](upload://wapxus0KQEy7olD4hosPhGa7AM8.png)


### 3. 拉取镜像

> 镜像版本一般采用最新版本，如需其他版本替换**latest**为对应版本即可。拉取和运行都不建议以默认方式运行， 防止国外镜像容器网络问题导致网络不稳定。

```bash
# 拉取方式一: 默认方式，国内环境不建议使用
docker pull openeuler/openeuler:latest
# 拉取方式二：指定国内仓库，国内用户推荐使用
docker pull hub.oepkgs.net/openeuler/openeuler:latest
```
```bash
#镜像拉取完成后可以看到
docker images 
```
![9ee6e0cc8c8e3760d395c74ef40e8ce|690x196, 100%](upload://hCv0hdeEvIeNiIx6hICo5M2h4lV.png)

### 4. 运行容器

> 镜像版本一般采用最新版本，如需其他版本替换**latest**为对应版本即可。

```bash
# 运行方式一: 默认方式，国内环境不建议使用
docker run -it  openeuler/openeuler:latest
# 运行方式二：指定国内仓库，国内用户推荐使用
docker run -it  hub.oepkgs.net/openeuler/openeuler:latest
```
![image|690x221, 100%](upload://gtgMFwpJ26ZfkhiAdBmF4MCg7UW.png)

### 5. 容器运行测试

编写测试脚本，openeuler默认自带python3工具，可以编写一个简单的HelloWorld脚本测试。

参考如下:
```bash
# 打开文件编辑器
vi HelloWorld.py
```

```bash
# 按键insert或者i键开始编辑
```

```bash
# 输入测试程序
print("Hello, world!")
```
```bash
# 按键esc 退出编辑 按键 shift+:  输入wq! 保存文件
```
```bash
# 执行python脚本，测试程序
python3 HelloWorld.py
```
文件编辑示例
![1764949434bd5702c414beeb347aa6b|413x500](upload://qpoEzgVJu15FRgxiUPrijxF6Wzr.png)

程序运行示例
![a1c90f492615be15f6e4877a4185503|445x112](upload://boJxl2Hf3ul6orAUVD1XcACBB1Y.png)
