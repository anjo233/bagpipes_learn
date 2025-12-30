# bagpipes_learn
bagpipes是一款用于对星系光谱进行建模，并拟合光谱和测光观测数据的python开源工具。

本仓库示例代码来源于官方示例库，已翻译成中文，并在原文档的基础上加入了更多说明。
后续将整理更多的示例代码完善仓库。

# 推荐环境
python <= 3.10
更高版本的python可能存在一些冲突

软件包：

```requirements.txt```

```rtd-environment.yml```

注：numpy最新版也可以使用

## 关于计算包 PyMultiNest 和 Nautilus
官方文档中详细可了解MultiNest的安装

URL：https://johannesbuchner.github.io/pymultinest-tutorial/install.html

这里推荐nautilus计算包

```pip install nautilus-sampler```

说明：MultiNest包安装较为麻烦且已经不再维护，可能出现许多软件包版本的冲突，且官方文档中并未详细说明环境要求

因此这里推荐Nautilus，nautilus安装较为方便且不存在与其他包版本冲突的情况。


# 安装bagpipes
- 使用你的包管理器（conda）
建议新建虚拟环境

```pip install requirements.tst```

```pip install -r requirements.tst```

- 使用UV包管理器（推荐）

https://docs.astral.sh/uv/

 下载部署仓库到本地，在项目文件夹下使用以下命令

```uv venv .venv --python 3.10``` # 建立虚拟环境

linux&mac ```source .venv/bin/activate``` # 激活环境（Linux/mac）

windows ```.venv\Scripts\Activate.ps1``` # 激活环境(win)

```uv pip install -r requirements.txt```

UV包管理器的python虚拟环境在项目文件夹（.venv）中


# 说明
bagpipes源码有一些bug，如果你在使用过程中出现问题可以将下面仓库抛给ai再询问。

bagpipes的官方文档:https://bagpipes.readthedocs.io/en/latest/

bagpipes的源码及案例教程库及:https://github.com/ACCarnall/bagpipes

This is a learning repository for the Bagpipes software package, primarily designed for Chinese-speaking users.

感谢Claude ai提供翻译服务
