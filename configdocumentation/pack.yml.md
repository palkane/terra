### 核心配置文件 (pack.yml)  
**世界生成包的全局清单**  

#### 基础配置  
- **`biomes` BiomeProvider**  
  控制生物群系在世界中的分布方式  

- **`generator` ChunkGenerator**  
  指定使用的区块生成器类型  
  - 在应用任何生成阶段前，决定区块的基础地形  

- **`id` String**  
  世界生成包的唯一标识符  
  - 在游戏中选择世界生成器时使用  

- **`stages` List\<GenerationStage\>**  
  按顺序执行的生成阶段列表  
  - 在区块生成器完成基础地形后逐步应用  

- **`version` Version**  
  配置包的版本号  

- **`author` String**  
  作者信息（默认值：匿名）  

---

### **3D噪声地形专用参数**  
**（需安装 'chunk-generator-noise-3d' 扩展插件，且 generator=NOISE_3D 时生效）**  

- **`blend.terrain.elevation` Integer**  
  地形混合高度范围（单位：区块）  
  *默认值：4*  

- **`carving.resolution.horizontal` Integer**  
  洞穴/峡谷的水平生成精度  
  *默认值：4*  

- **`carving.resolution.vertical` Integer**  
  洞穴/峡谷的垂直生成精度  
  *默认值：2*  

- **`slant.calculation-method` SlantCalculationMethod**  
  斜面计算方法（如导数计算法）  
  *默认值：Derivative*  

---

### **数学函数库参数**  
**（需安装 'config-noise-function' 扩展插件）**  

- **`functions` Map\<String, MathFunction\>**  
  全局数学函数库（可在其他配置的EXPRESSION采样器中调用）  
  *特性：*  
  - 函数之间不可互相引用  
  - 仅支持在其他配置文件中调用  
  *默认值：{}*  

- **`samplers` Map\<String, DimensionApplicableSampler\>**  
  全局采样器库（可在其他配置的EXPRESSION采样器中调用）  
  *特性：*  
  - 采样器之间不可互相引用  
  *默认值：{}*  

---

### **图像缓存参数**  
**（需安装 'library-image' 扩展插件）**  

- **`images.cache.load-on-use` Boolean**  
  启用按需加载图像（而非启动时全部加载）  
  *默认值：False*  

- **`images.cache.timeout` Integer**  
  图像缓存保留时间（秒）  
  *默认值：0（永久缓存）*  
  *效果说明：*  
  - >0时：未使用的图像超时后释放内存  
  - =0时：图像常驻内存  
  *权衡：*  
  - 正值降低内存占用但增加缓存查询开销  
  - 零值提升读取速度但增加内存压力  

> **配置示例片段**：  
> ```yaml  
> generator: NOISE_3D  
> functions:  
>   mountain_shape: "clamp(0, 1, (x^2 + z^2)^0.5)"  
> images.cache:  
>   timeout: 300  # 5分钟未使用则释放图像内存  
> ```