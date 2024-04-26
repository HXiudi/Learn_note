# 安装 nimiconda（类似于 Anaconda）

1. **在线下载 nimiconda（或者 Anaconda）：**

```bash
sudo wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

下载完成后，使用 `ls` 命令查看当前目录，确认是否有下载的安装包 `Miniconda3-latest-Linux-x86_64.sh`。

1. **安装 nimiconda：**

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```

安装过程中一路输入 `yes` 即可，默认安装路径为 `/home/your_username/miniconda3`，你也可以选择修改安装路径。

1. **重启系统：**

完成安装后，重启系统。重启后会出现命令行提示符，表示安装成功：

```bash
(base) your_username@XXXXXX:~$
```