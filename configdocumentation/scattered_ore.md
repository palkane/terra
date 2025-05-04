### 离散矿石配置 (SCATTERED_ORE)  
**（需安装 'config-ore' 扩展插件）**  

此配置类型用于创建离散分布的矿石结构实例。

#### 继承参数  
- **`extends` List\<String\>**  
  从其他离散矿石配置继承参数的列表：  
  - 仅继承当前配置未定义的参数  
  - 列表顺序决定优先级（先列出的优先）  
  - 支持多配置文件的参数复用  

#### 核心参数  
- **`id` String**  
  配置的唯一标识符（需在配置中声明两次）  
  - 用于其他配置引用此矿石配置  

- **`material` Block**  
  矿石的主材质方块（如"minecraft:iron_ore"）  

- **`replace` Set\<Block\>**  
  允许被替换的原生方块集合  
  - 例：["minecraft:stone", "minecraft:deepslate"]  

#### 可选参数  
- **`material-overrides` Map\<Block, Block\>**  
  特殊方块的材质覆写规则  
  - 格式：{原方块: 替换方块}  
  - 默认值：{}  

- **`physics` Boolean**  
  是否启用物理特性（如重力影响）  
  - 默认值：false  

- **`size` Integer**  
  单个矿簇的最大尺寸（方块数）  
  - 默认值：7  

> **典型配置示例**：  
> ```yaml
> id: "diamond_scattered"
> material: "minecraft:diamond_ore"
> replace: ["minecraft:stone"]
> material-overrides:
>   "minecraft:deepslate": "minecraft:deepslate_diamond_ore"
> size: 5
> ```
> 该配置将生成：  
> - 最大5方块大小的钻石矿簇  
> - 仅替换普通石头  
> - 遇到深板岩时自动转为深板岩钻石矿