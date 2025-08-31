# Anaconda-用conda创建python虚拟环境



## 安装使用

conda可以理解为一个工具，也是一个可执行命令，其核心功能是包管理和[环境管理](https://zhida.zhihu.com/search?content_id=109184060&content_type=Article&match_order=1&q=环境管理&zhida_source=entity)。包管理与[pip](https://zhida.zhihu.com/search?content_id=109184060&content_type=Article&match_order=1&q=pip&zhida_source=entity)的使用方法类似，环境管理则是允许用户方便滴安装不同版本的python环境并在不同环境之间快速地切换。

conda的设计理念

conda将几乎所有的工具、第三方包都当作package进行管理，甚至包括python 和conda自身。Anaconda是一个打包的集合，里面预装好了conda、某个版本的python、各种packages等。

1.安装Anaconda。

打开命令行输入conda -V检验是否安装及当前conda的版本。

通过Anaconda安装默认版本的Python，3.6的对应的是 Anaconda3-5.2，5.3以后的都是python 3.7。

[Index of / (anaconda.com)](https://link.zhihu.com/?target=https%3A//repo.anaconda.com/archive/)

2.conda常用的命令

**查看安装了哪些包**

```text
conda list
```

**查看当前存在哪些[虚拟环境](https://zhida.zhihu.com/search?content_id=109184060&content_type=Article&match_order=1&q=虚拟环境&zhida_source=entity)**

```text
conda env list 
conda info -e
```

**检查更新当前conda**

```text
conda update conda
```

**Python创建虚拟环境**

```text
conda create -n your_env_name python=x.x
```

anaconda命令创建python版本为x.x，名字为your_env_name的虚拟环境。**your_env_name文件可以在Anaconda安装目录envs文件下找到**。

**激活或者切换虚拟环境**

打开命令行，输入python --version检查当前 python 版本。

```text
#Linux:  source activate your_env_nam
Windows: conda activate your_env_name
```

**退出当前环境**

要退出当前激活的环境（例如 `.venv` 或 `base`），使用以下命令：

```
conda deactivate
```

这将使你退出当前环境，返回到 `base` 环境或完全退出到系统默认环境。

**对虚拟环境中安装额外的包**

```text
conda install -n your_env_name [package]
```

**关闭虚拟环境(即从当前环境退出返回使用PATH环境中的默认python版本)**

```text
deactivate env_name
或者`activate root`切回root环境
Linux下：source deactivate 
```

**删除虚拟环境**

```text
conda remove -n your_env_name --all
```

**删除环境钟的某个包**

```text
conda remove --name $your_env_name  $package_name 
```

8、设置国内镜像

[http://Anaconda.org](https://link.zhihu.com/?target=http%3A//Anaconda.org)的服务器在国外，安装多个packages时，conda下载的速度经常很慢。清华[TUNA镜像源](https://zhida.zhihu.com/search?content_id=109184060&content_type=Article&match_order=1&q=TUNA镜像源&zhida_source=entity)有Anaconda仓库的镜像，将其加入conda的配置即可：

\# 添加Anaconda的TUNA镜像

conda config --add channels [https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/](https://link.zhihu.com/?target=https%3A//mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/)

\# TUNA的help中镜像地址加有引号，需要去掉

\# 设置搜索时显示通道地址

conda config --set show_channel_urls yes

9、恢复默认镜像

conda config --remove-key channels



## 问题

### conda info 显示 active environment : None

在运行 `conda activate bdp` 后，`conda info` 显示 `active environment : None`，这表明环境没有成功激活。可能是因为以下几个原因：

1. **环境激活命令不正确**

你可能使用了 `activate` 命令，而不是 `conda activate`。确保你使用的是 `conda activate` 命令：

```
conda activate bdp
```

2. **Conda 环境初始化问题**

如果 Conda 没有正确初始化，可能会导致无法激活环境。你可以尝试使用以下命令重新初始化 Conda：

```
conda init powershell
```

然后，关闭当前 PowerShell 窗口并重新打开一个新的 PowerShell 窗口，再尝试激活环境。











