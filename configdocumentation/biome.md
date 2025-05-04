### 生物群系 (BIOME)  
**（需安装 'config-biome' 扩展插件）**  

此配置类型用于创建生物群系实例。  

#### 继承参数  
- **`extends` List\<String\>**  
  从其他生物群系配置中继承参数的列表。  
  - 仅当当前配置未定义某参数时，才会使用继承配置中的参数  
  - 列表中的配置按顺序优先生效  
  - 该机制支持跨配置文件复用参数  

#### 核心参数  
- **`id` String**  
  用于在其他配置中引用此生物群系的唯一标识符  

- **`color` Integer**  
  生物群系颜色值  

- **`tags` Set\<Tag\>**  
  生物群系标签集合  

- **`vanilla` PlatformBiome**  
  关联的原版生物群系  

---

### **3D噪声地形生成专用参数**  
**（需安装 'chunk-generator-noise-3d' 扩展插件，且 pack.yml 中 generator 设为 NOISE_3D 时生效）**  

- **`palette` List\<Map\<Palette, Integer\>\>**  
  地形调色板层级（权重映射）  

- **`terrain.sampler` NoiseSampler**  
  地形噪声采样器  

- **`carving.update-palette` Boolean**  
  是否根据侵蚀更新调色板  
  *默认值: false*  

- **`ocean.level` Integer**  
  海洋基准高度  
  *默认值: 0*  

- **`ocean.palette` Palette**  
  海洋调色板  
  *默认值: 空气调色板*  

- **`slant` List\<SlantLayer\>**  
  斜面层配置  
  *默认值: []*  

- **`slant-depth` Integer**  
  斜面最大影响深度  
  *默认值: 无限*  

- **`terrain.blend.distance` Integer**  
  地形混合距离  
  *默认值: 3*  

- **`terrain.blend.step` Integer**  
  地形混合步长  
  *默认值: 4*  

- **`terrain.blend.weight` Float**  
  地形混合权重  
  *默认值: 1.0*  

- **`terrain.blend.weight-2d` Float**  
  二维地形混合权重  
  *默认值: 1.0*  

- **`terrain.sampler-2d` NoiseSampler**  
  二维地形噪声采样器  
  *默认值: 输出恒为0的'CONSTANT'采样器*  

---

### **地物生成阶段专用参数**  
**（需安装 'generation-stage-feature' 扩展插件）**  

- **`features.<stage id>` List\<Feature\>**  
  在指定地物生成阶段（`<stage id>`）中，该生物群系内生成的地物列表  
  - 可用生成阶段在 pack.yml 中定义  
  - 示例：  
    ```yaml
    features.SURFACE: # 地表生成阶段
      - "forest_rocks" 
      - "fallen_logs"
    ```