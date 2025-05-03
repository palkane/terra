### Terra噪声采样器配置指南

#### 基础配置
配置地形包时，多数参数需要定义噪声采样器(NoiseSampler)。最简单的配置只需声明采样器类型：

```yaml
type: WHITE_NOISE  # 白噪声采样器
```

#### 参数体系
不同采样器类型会激活特定的参数选项：
- **必填参数**：必须显式声明
- **可选参数**：如白噪声的`salt`参数，未指定时使用默认值

示例（带盐值参数）：
```yaml
type: WHITE_NOISE
salt: 2321  # 未指定时默认为0
```

#### 噪声可视化工具
推荐使用官方**Noise Tool**工具预览噪声效果：
- 功能：实时编辑/预览采样器配置
- 获取方式：[GitHub仓库](https://github.com/PolyhedralDev/NoiseTool/releases)
- 注意：需从Terra安装目录复制`bootstrap`和`config-noise-function`插件到工具的`addons`文件夹

![噪声工具界面](noise_picture/noise_tool.png)

#### 核心参数详解
**频率(Frequency)**
控制噪声的缩放比例：
```yaml
type: OPEN_SIMPLEX_2
frequency: 0.05  # 值越大细节越密集
```
对比示例：
| 2倍频率 | 基准频率 | 0.5倍频率 |
|---------|----------|-----------|
| ![高频](noise_picture/opensimplex2_64x64_freq_double.png) | ![基准](noise_picture/opensimplex2_64x64_freq_half.png) | ![低频](noise_picture/opensimplex2_64x64.png) |

> 注意：频率调整主要适用于相干噪声（如Simplex），随机噪声修改频率等效于改变种子值

#### 实际应用案例
调色板配置中的噪声应用：
```yaml
id: DIRTY_GRASS
layers:
  - layers: 1
    materials:
      - "minecraft:grass_block": 1
      - "minecraft:coarse_dirt": 1
    sampler:  # 控制两种方块的分布模式
      type: OPEN_SIMPLEX_2
      frequency: 0.05
```

![调色板效果](noise_picture/palette_example.png)

#### 高级技术
**分形化(Fractalization)**
通过多层级噪声叠加增加细节：
```yaml
type: FBM  # 分形布朗运动
sampler: 
  type: OPEN_SIMPLEX_2
octaves: 3    # 迭代层数
lacunarity: 2 # 频率倍增系数
gain: 0.75    # 振幅衰减系数
```
分形效果演进：
| 无分形 | 2层 | 3层 | 4层 |
|--------|-----|-----|-----|
| ![1层](noise_picture/opensimplex2_1_octave.png) | ![2层](noise_picture/opensimplex2_2_octaves.png) | ![3层](noise_picture/opensimplex2_3_octaves.png) | ![4层](noise_picture/opensimplex2_4_octaves.png) |

[**域扭曲(Domain Warping)**](noise_picture/overwarped.png)
用噪声扭曲原始噪声：
```yaml
type: DOMAIN_WARP
amplitude: 20  # 扭曲强度
sampler:  # 被扭曲的噪声
  type: OPEN_SIMPLEX_2
  frequency: 0.005  
warp:  # 扭曲源噪声
  type: OPEN_SIMPLEX_2
```
效果演示：
| 原始棋盘格 | 扭曲噪声源 | 扭曲效果 |
|------------|------------|----------|
| ![棋盘格](noise_picture/checkerboard.png) | ![Simplex](noise_picture/simplex_warp.png) | ![扭曲结果](noise_picture/checkerboard_warped.png) |

#### 复合采样器
支持嵌套结构实现复杂效果：
```yaml
type: DOMAIN_WARP
sampler:
  type: FBM  # 分形噪声
  sampler: 
    type: OPEN_SIMPLEX_2
warp: 
  type: DOMAIN_WARP  # 二级扭曲
  warp: 
    type: FBM        # 三级分形
    sampler: ...     # 可无限嵌套
```

> 提示：建议在Noise Tool中实时调试参数组合，所有采样器类型详见官方文档