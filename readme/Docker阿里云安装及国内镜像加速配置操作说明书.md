# Docker阿里云安装及国内镜像加速配置操作说明书

## 1\. 概述

本文档用于指导用户在Ubuntu系统中，通过阿里云镜像源完成Docker完整安装，同时配置国内镜像加速仓库，解决官方镜像下载速度慢、连接超时等问题，最后通过官方测试镜像验证Docker部署环境是否正常可用。本操作适用于Linux系统Docker初学部署、服务器Docker环境初始化等场景，操作简单、可重复性强。

## 2\. 运行环境

1\. 操作系统：Ubuntu 系列系统
2. 操作工具：FinalShell、Xshell等远程终端工具
3. 网络环境：可正常访问外网，支持阿里云镜像源、国内加速镜像源访问

## 3\. 功能介绍

1\. 基于阿里云国内镜像源安装Docker，替代国外官方源，提升安装速度、避免安装失败
2. 配置多组国内Docker镜像加速仓库，优化镜像拉取速度
3. 完整实现Docker安装、环境配置、功能测试全流程，搭建可用的Docker容器运行环境

## 4\. 操作步骤（说明书核心）

### 4.1 更新系统依赖环境

执行命令更新系统软件源，并安装Docker安装所需的依赖组件：

```Plain Text
sudo apt update \\\&\\\& sudo apt install ca-certificates curl gnupg lsb-release -y
```

### 4.2 获取阿里云Docker官方密钥

通过curl工具拉取阿里云Docker镜像源密钥，用于校验安装包合法性，保证安装安全：

```Plain Text
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

### 4.3 添加阿里云Docker软件源

写入阿里云Docker镜像源配置，适配当前系统架构和系统版本，添加至系统软件源列表：

```Plain Text
echo "deb \\\[arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb\\\_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

### 4.4 安装Docker核心组件

再次更新软件源，安装Docker引擎、命令行工具、容器运行环境及容器编排插件：

```Plain Text
sudo apt update \\\&\\\& sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
```

### 4.5 普通用户Docker授权

将当前用户加入docker用户组，免sudo权限执行Docker命令，提升操作便捷性：

```Plain Text
sudo usermod -aG docker $USER
```

### 4.6 配置国内镜像加速仓库

1\. 通过vi编辑器打开Docker配置文件：

```Plain Text
sudo vi /etc/docker/daemon.json
```

2\. 操作vi编辑器：按ESC切换至命令模式，输入`gg`跳转文件头部、`dG`清空全部原有内容；按`i`进入插入模式，粘贴国内镜像加速配置：

```Plain Text
{
  "registry-mirrors": \\\[
    "https://docker.m.daocloud.io",
    "https://docker.1panel.live",
    "https://hub.rat.dev"
  ]
}
```

3\. 保存退出：按ESC切回命令模式，输入`:wq`回车，保存配置并退出编辑器。

4\. 重载系统配置并重启Docker，使镜像加速配置生效：

```Plain Text
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 4.7 Docker环境部署测试

1\. 拉取官方测试镜像hello-world，验证镜像拉取功能：

```Plain Text
sudo docker pull hello-world
```

2\. 运行测试容器，验证Docker服务可正常创建、运行容器：

```Plain Text
docker run hello-world
```

## 5\. 关键命令/按键说明

### 5.1 Docker安装核心命令

* `apt update`：更新系统软件源索引
* `curl -fsSL`：静默远程拉取网络文件/密钥
* `usermod -aG`：将用户添加至指定用户组，授权权限
* `daemon-reload`：重载系统服务配置
* `systemctl restart docker`：重启Docker服务，加载新配置
* `docker pull`：拉取远程镜像至本地
* `docker run`：基于镜像创建并运行容器

### 5.2 Vi编辑器核心按键

* `ESC`：切换至命令模式
* `gg`：光标跳转至文件首行
* `dG`：清空文件全部内容
* `i`：进入插入编辑模式
* `:wq`：保存文件并退出编辑器

## 6\. 使用效果/运行结果

1\. Docker安装完成后，无报错提示，系统可识别docker命令；
2. 镜像加速配置生效后，执行`docker info`可查看到配置的国内镜像仓库地址；
3. `docker pull hello-world`可快速拉取测试镜像，无超时、下载失败问题；
4. `docker run hello-world`执行后，终端输出`Hello from Docker!`相关英文提示，说明Docker服务安装、配置、运行全部正常。

## 7\. 常见问题与注意事项

* 配置`daemon.json`文件时，必须使用**英文半角符号**，括号、引号、逗号需规范，末尾元素禁止多余逗号，否则Docker重启报错。
* Vi编辑器操作需区分模式，编辑内容必须进入插入模式，保存退出需切回命令模式。
* 用户授权命令执行后，需重新登录终端，权限方可完全生效。
* 部分镜像加速节点存在网络波动，若单个镜像源失效，可保留其余镜像源，不影响基础使用。
* 安装过程中需保证网络通畅，禁止中断命令执行，避免安装不完整导致环境异常。

## 8\. 总结

本次操作完成了基于阿里云镜像源的Docker完整安装，通过配置多组国内镜像加速仓库，解决了原生Docker镜像下载缓慢的问题。同时通过hello-world官方镜像完成全功能测试，验证了Docker服务安装成功、配置生效、运行正常。整套流程简洁高效，适配Ubuntu系统，可快速完成Docker基础环境搭建，为后续容器部署、项目运维提供基础环境支撑，是Linux服务器Docker初始化的标准流程。



