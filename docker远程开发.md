# Docker远程开发笔记

## Dockerfile使用和制作

**在宿主机上编辑Dockerfile**：在Dockerfile中添加安装SSH服务器和必要配置的步骤。示例Dockerfile可能如下所示：

```dockerfile
# 使用基础镜像
FROM nvidia/cuda:12.0.0-cudnn8-devel-ubuntu20.04

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    echo "Asia/Shanghai" > /etc/timezone

# 更新系统并安装 OpenSSH 服务
RUN apt-get update && apt-get install -y openssh-server
RUN mkdir /var/run/sshd

# 设置 root 用户的密码为 "password"，请在实际应用中更改为更安全的密码
RUN echo 'root:xiaozhi..' | chpasswd

# 允许 root 用户远程登录
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# 配置 SSH 登录环境变量
ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

# 开放 SSH 服务端口
EXPOSE 22

# 启动 SSH 服务
CMD ["/usr/sbin/sshd", "-D"]

```

1. 请将`your_password`替换为你想要设置的root用户的密码。

2. **构建新的Docker镜像**：在包含Dockerfile的目录中运行以下命令构建新的Docker镜像：

   ```
   docker build -t my_ssh_image .
   ```

3. **运行新的Docker容器**：使用新构建的镜像运行Docker容器，并映射SSH端口到宿主机的端口：

   ```
   docker run -d -p 2222:22 --name my_ssh_container my_ssh_image
   ```

   这里假设将容器的SSH端口映射到宿主机的2222端口，你可以根据需要修改端口映射。

4. **连接到Docker容器**：现在，你可以使用SSH客户端连接到Docker容器。例如，使用以下命令连接到端口2222：

   ```
   ssh root@localhost -p 2222
   ```

   输入之前设置的密码，应该可以成功连接到Docker容器中的Ubuntu并进行远程开发了。

   

## 远程开发流程

1. **创建Docker容器**：使用以下命令在本地创建一个新的Docker容器（以Ubuntu为例）：

   ```bash
   docker run -it --name my_ubuntu_container ubuntu:latest /bin/bash
   ```
   这将创建一个名为`my_ubuntu_container`的新容器，并进入到该容器的bash终端。

2. **配置SSH服务器**：在容器内部安装和配置SSH服务器：
   
   ```bash
   apt update
   apt install -y openssh-server
   mkdir /var/run/sshd
   echo 'root:your_password' | chpasswd
   sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
   systemctl restart ssh
   ```
   
3. **映射SSH端口**：退出容器并重新以映射SSH端口的方式运行容器：

   ```bash
   docker run -d -p 2222:22 --name my_ssh_container my_ubuntu_container
   ```

4. **远程连接**：使用SSH客户端连接到Docker容器：
   ```bash
   ssh root@localhost -p 2222
   ```
   输入之前设置的密码，即可远程连接到Docker容器进行开发。

## 将容器打包为镜像

1. **确认容器运行状态：**

   首先，确保你的容器处于运行状态。可以使用以下命令查看当前正在运行的容器：

   ```bash
   docker ps
   ```

   如果你的容器没有在运行，请先使用 `docker start` 命令启动容器。

2. **进入容器并进行操作：**

   使用以下命令进入你想要打包的容器：

   ```bash
   docker exec -it container_name /bin/bash
   ```

   替换 `container_name` 为你的容器名称。

3. **在容器内进行配置或操作：**

   在进入容器后，你可以进行需要的配置、安装软件或修改文件等操作。确保容器内的状态符合你要创建的镜像需求。

4. **退出容器并保存更改：**

   完成容器内的操作后，使用以下命令退出容器：

   ```bash
   exit
   ```

   容器会自动停止。

5. **提交容器为镜像：**

   使用以下命令将容器提交为镜像：

   ```bash
   docker commit container_name image_name:tag
   ```

   替换 `container_name` 为你的容器名称，`image_name` 为你要创建的镜像名称，`tag` 为镜像的版本号或标签。

6. **查看新创建的镜像：**

   使用以下命令查看刚刚创建的镜像：

   ```bash
   docker images
   ```

   确认镜像是否成功创建，并且可以看到新的镜像信息。



## Docker Push & Pull 用法

### Docker Hub 登录

在执行 `docker push` 或 `docker pull` 前，需要先登录 Docker Hub。

```bash
docker login
```

如果没有账号，请先在 [Docker Hub](https://hub.docker.com/) 注册。

### Docker Push

将本地镜像推送到 Docker Hub，需要先给镜像打标签，然后执行推送命令。

#### 步骤

1. **给本地镜像打标签**：

   ```bash
   docker tag local_image:tagname username/repository:tagname
   ```

2. **推送镜像到 Docker Hub**：

   ```bash
   docker push username/repository:tagname
   ```

### Docker Pull

从 Docker Hub 拉取镜像到本地，可以使用 `docker pull` 命令。

#### 用法

```bash
docker pull username/repository:tagname
```

#### 注意事项

- 登录 Docker Hub 后才能执行推送和拉取操作。
- 推送前需为本地镜像打标签，标签应与远程仓库路径一致。
- 使用 `docker tag` 命令将本地镜像打标签。
- 执行 `docker push` 将标记后的镜像推送到 Docker Hub。
- 执行 `docker pull` 可以从 Docker Hub 拉取镜像到本地。

# Docker命令操作

## docker run的命令行说明

```dockerfile
docker run --name yolov9 --gpus all -it -v /home/mems/xzj/yolov9/datasets/:/coco/ -v /home/mems/xzj/yolov9/yolov9/:/yolov9 --shm-size=64g nvcr.io/nvidia/pytorch:21.11-py3
```

|                   参数                    |                             说明                             |
| :---------------------------------------: | :----------------------------------------------------------: |
|               --name yolov9               |                   指定容器的名称为 yolov9                    |
|                --gpus all                 |              允许 Docker 容器访问所有可用的 GPU              |
|                  -it/-d                   | -it：这个选项组合使得容器以交互模式运行，并分配一个伪终端；-d：以后台模式运行容器 |
| -v /home/mems/xzj/yolov9/datasets/:/coco/ | 将宿主机的 /home/mems/xzj/yolov9/datasets/ 目录挂载到容器的 /coco/ 目录 |
| -v /home/mems/xzj/yolov9/yolov9/:/yolov9  | 将宿主机的 /home/mems/xzj/yolov9/yolov9/ 目录挂载到容器的 /yolov9 目录 |
|              --shm-size=64g               | 设置容器的共享内存大小为 64 GB，这对大规模数据处理或深度学习模型训练是有帮助的 |
|     nvcr.io/nvidia/pytorch:21.11-py3      |                    指定使用的 Docker 镜像                    |



# 常见问题及解决方法

### 问题1: 密码错误

**解决方法**：

- 确保输入的密码是准确的，注意密码的大小写。
- 如果密码中包含特殊字符，尝试进行转义或者用引号括起来。
- 检查SSH服务器配置，确保用户名和密码设置正确。
- 尝试使用其他验证方式，如SSH密钥认证。

### 问题2: 主机密钥变化

**解决方法**：
- 删除旧的主机密钥，允许SSH客户端接受新的主机密钥。
- 联系系统管理员确认主机密钥变化的原因，并进行必要的安全检查。

### 问题3: SSH连接被关闭

**解决方法**：

- 检查远程主机的SSH服务是否启动，确保网络连接和防火墙设置允许SSH连接。
- 检查SSH配置文件，确保SSH服务已正确配置并允许连接。
- 确保使用正确的用户名、密码或SSH密钥进行连接。

### 问题4: Docker build构建镜像时卡住

**解决方法**：

- 设置时区

在Dockerfile文件中添加以下命令来设置时区，这样在构建Docker镜像时就会自动设置时区。

```dockerfile
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    echo "Asia/Shanghai" > /etc/timezone
```

### 问题5: ssh 两个ip端口冲突，导致没办法连接

无法重新远程连接，有可能Add correct host key in /root/.ssh/known_hosts to get rid of this message

**解决方法**：

进入到文件夹`C:\Users\Xiudi\.ssh`进去将冲突的IP进行删除即可



# Docker内网穿透

### 中国镜像

#### Linux 系统下
1. 下载并安装 cpolar:
   ```bash
   curl -L https://www.cpolar.com/static/downloads/install-release-cpolar.sh | sudo bash
   ```

2. Docker 操作：
   - 搜索 Docker 镜像:
     ```bash
     docker search cpolar
     ```
   - 下载 Docker 镜像:
     ```bash
     docker pull probezy/cpolar
     ```
   - 查看 Docker 镜像:
     ```bash
     docker image
     ```
   - 运行 cpolar 服务:
     ```bash
     docker run -id --network host --name cpolar probezy/cpolar
     ```
   - 进入 Docker 容器的 Bash 环境:
     ```bash
     docker exec -it cpolar /bin/bash
     ```

3. 设置 cpolar 授权 token（替换 `[您的token]` 为您的实际 token）:
   ```bash
   cpolar authtoken [您的token]
   ```

4. 启动 cpolar 服务监听 8081 端口:
   ```bash
   cpolar http 8081
   ```

#### 访问 Web 界面
- 直接访问：
  - `http://本机ip:9200`
  - `http://127.0.0.1:9200`

#### 高级TCP（通过 Http://127.0.0.1:9200进行访问）
- 高级 TCP 设置：
  - 类型：自定义
  - 协议：TCP
  - 本地地址：22 (假定是服务器的 SSH 端口)
  - 域名类型：域名or固定
  - 地区：China vip
  - 开放端口列表
  - 访问地址：`tcp://6.tcp.vip.cpolar.cn:10725`

#### 远程连接（外网映射）
- 通过 SSH 连接：
  ```bash
  ssh root@6.tcp.vip.cpolar.cn -p 10725
  ```



