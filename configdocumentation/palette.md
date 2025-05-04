### 调色板配置 (PALETTE)  
**（需安装 'config-palette' 扩展插件）**  

此配置类型用于创建地形调色板实例。  

#### 继承参数  
- **`extends` List\<String\>**  
  从其他调色板配置继承参数的列表：  
  - 仅继承当前配置未定义的参数  
  - 配置列表中靠前的配置具有更高优先级  
  - 支持跨配置文件参数复用  

#### 核心参数  
- **`id` String**  
  在其他配置中引用此调色板的唯一标识符（需在配置中声明两次）  

- **`layers` List\<PaletteLayer\>**  
  调色板层级定义列表：  
  - 每个层级包含方块材质及其分布权重  
  - 层级按从上到下的顺序应用  

- **`sampler` NoiseSampler**  
  控制调色板材质分布的噪声采样器：  
  - 通过噪声值决定不同层级的混合程度  

> **配置示例**：  
> ```yaml  
> id: "grassland_palette"  
> layers:  
>   - blocks: {"minecraft:grass_block": 5, "minecraft:coarse_dirt": 1}  
>     depth: 1  
>   - blocks: {"minecraft:dirt": 3, "minecraft:gravel": 1}  
>     depth: 3  
>   - blocks: {"minecraft:stone": 1}  
> sampler: "SIMPLEX@0.2"  
> ```  
> 该调色板会生成：  
> 1. 顶层（1格深）：80%草方块 + 20%粗泥  
> 2. 中间层（3格深）：75%泥土 + 25%沙砾  
> 3. 底层：100%石头  
> 使用0.2缩放系数的Simplex噪声控制过渡效果