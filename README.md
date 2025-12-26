# bagpipes_learn
This is a learning repository for the Bagpipes software package, primarily designed for Chinese-speaking users.
本仓库示例代码来源于官方示例库，已翻译成中文
后续将整理更多的示例代码完善仓库。

# 推荐环境
python <= 3.10
更高版本的python可能存在一些冲突
软件包：
```requirements.txt```
```rtd-environment.yml```

注：numpy最新版也可以使用

关于计算包 PyMultiNest 和 Nautilus
官方文档中详细可了解MultiNest的安装
URL：https://johannesbuchner.github.io/pymultinest-tutorial/install.html

这里推荐nautilus计算包
```pip install nautilus-sampler```

说明：MultiNest包安装较为麻烦且已经不再维护，nautilus安装较为方便且不存在与其他包版本冲突的情况。


# 安装bagpipes
- 使用你的包管理器（conda）
建议新建虚拟环境
```pip install requirements.tst```
```pip install -r requirements.tst```

- 使用UV包管理器（推荐）
 下载部署本仓库到本地
```python -m venv .venv```
```source .venv/bin/activate```
```uv pip install -r requirements.tst```
UV包管理器的python虚拟环境在项目文件夹（.venv）中


注：bagpipes源码有一些bug

    bagpipes的官方文档:https://bagpipes.readthedocs.io/en/latest/
    bagpipes的源码及案例教程库及:https://github.com/ACCarnall/bagpipes
