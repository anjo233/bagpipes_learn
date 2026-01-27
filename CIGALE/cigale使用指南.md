____
**CIGALE** (Code Investigating GALaxy Emission) 是一个基于贝叶斯方法的 SED（光谱能量分布）拟合软件。

- **核心功能：** 通过将观测到的光度数据（Spectrophotometric data）与物理模型网格进行对比，估算星系的物理参数（如恒星形成率 SFR、恒星质量 $M_*$、尘埃衰减、AGN 贡献等）。
    
- **原理：** 能量平衡原理（Energy Balance）。即尘埃在紫外/可见光波段吸收的能量，必须等于其在红外波段辐射出的能量。
    
- **适用范围：** 从 X 射线到射电波段，支持宽带测光数据和光谱数据 。

## 第一步 准备数据
需要准备你的包含观测数据列的星表ASCII 文本文件（CIGALE 也支持 FITS 表格）

## 第二步 初始化配置

### 数据准备
```Bash
pcigale init
```
会在当前目录下生成一个默认的 `pcigale.ini` 文件。

在`pcigale.ini`中配置好相应的参数
```ini
# 输入数据文件名
data_file = sdss_qso.txt

# 需要拟合的波段列表（名称必须与 txt 文件头完全一致）
# 注意：即使文件中列出了波段，如果不在这里写出来，CIGALE 将会忽略它。
bands = xray_boxcar_2to12keV, sdss.up, sdss.gp, sdss.rp, sdss.ip, sdss.zp
```
注意：与bagpipes不同cigale已经内置了庞大的滤光片数据库
```Bash
# 在滤光片数据库中寻找jwst字段的滤光片
pcigale-filters list | grep jwst

# 查看数据库中的f115w滤光片信息，返回透射率曲线
pcigale-filters plot jwst.nircam.f115w
```
如果你使用的是自制的滤光片，需要将你的滤光片数据传入道数据库中
```Bash
pcigale-filters add my_filter.dat --name my_custom_filter --type photon
```

- `--name`: 给它起个名字，以后在 `pcigale.ini` 里就用这个名字。
- `--type`: CCD/光子计数探测器通常用 `photon`，热波段计（Bolometer）用 `energy`。

### 物理模块选择 (SED Modules)
```Bash
sed_modules = sfhdelayed, bc03, nebular, dustatt_modified_starburst,dale2014, skirtor2016, xray, redshifting
```
- **sfhdelayed:** 延迟型恒星形成历史（适合大多数星系）。
- **bc03:** Bruzual & Charlot (2003) 恒星布居合成模型。
- **nebular:** 星云发射线模型（必须有，否则光度计算不准）。
- **dustatt_modified_starburst:** 尘埃衰减律（如 Calzetti 律）。
- **dale2014:** 尘埃红外再辐射模型。
- **skirtor2016:** **关键！** AGN（活动星系核）环面模型。
- **xray:** **关键！** 拟合 X 射线数据专用模块，它依赖于 AGN 模块 。
- **redshifting:** 将静止系光谱红移到观测系。

注意：`parameters_file` 可以让cigale改为从参数生成光谱的模式，大多数时候默认为空即可，
### 生成参数配置
```Bash
pcigale genconf
```
该命令会读取您选择的模块，并在 `pcigale.ini` 下方生成每个模块的具体参数列表。
### 设定物理参数网格

再次打开 `pcigale.ini`，您会看到新生成的模块部分（如 `[sfhdelayed]`, `[bc03]` 等）。

- **核心逻辑：** CIGALE 不是“拟合”出一个最佳值，而是计算您给定的**参数网格**中每一个组合的可能性(穷举)。
    
- **操作：** 您需要为每个参数提供一个值或一个列表。
    
    - 例如 `tau_main = 1000, 3000, 5000` (单位通常是 Myr)。
        
    - **AGN设置 (skirtor2016):** 对于 QSO 数据，重点关注 `fracAGN` (AGN贡献比例)，设置范围应较宽，如 `0.1, 0.5, 0.9`。
        
    - **X-ray设置:** 设置 X 射线的光子指数 `gam` 和光度比 `alpha_ox`。
        
#### 针对红移值不确定
修改 `pcigale.ini` 中的 `[redshifting]` 模块
在默认情况下，`pcigale.ini` 是空的（或者未指定），软件会直接读取数据文件（`sdss_qso.txt`）中 `redshift` 列的确切数值。
```Bash
[redshifting] 
# 例如如果你的目标的红移值大概为 1.5 到 2.5之间，你需要写入的一系列数值，步长必须一致
# 数值越密，精度越高，但计算时间越长
redshift = 1.5, 1.6, 1.7, 1.8, 1.9, 2.0, 2.1, 2.2, 2.3, 2.4, 2.5
```
- **先粗后细：** 先用较粗的步长（例如 0.1 或 0.2）跑一遍，大致确定每个天体的最佳红移位置。
- **然后再细化：** 如果某个天体在 2.1 附近拟合最好，再针对它单独设置 `2.05, 2.1, 2.15` 进行精细拟合。
### 第六步：运行拟合

参数设置完毕后，依次运行：

**检查：** `pcigale check` (检查参数是否有逻辑冲突，例如红移范围是否覆盖数据)。
**运行：** `pcigale run` (开始计算)。

	- 如果是多核 CPU，可以在 `pcigale.ini` 开头设置 `cores = N` 来加速 。N为CPU核心数目
## 结果分析

运行完成后，结果会保存在 `out/` 文件夹中：

1. **`results.fits` / `results.txt`:** 包含每个天体的最佳拟合参数（Best fit）和贝叶斯估计值（Bayesian estimates）。
    - 推荐使用贝叶斯估计值（列名通常带 `_bayes`），因为它是概率加权平均值，比单一的最佳拟合值更稳健。
2. **`out/paramname_best_model.fits`:** 最佳拟合光谱文件。
3. **可视化:** 使用 `pcigale-plots` 命令可以快速画出 SED 图。
```Bash
pcigale-plots sed
```
	这会生成 PDF 文件，展示观测数据点（红点）与最佳拟合模型线（黑线）的对比，以及各成分（恒星、尘埃、AGN）的贡献。