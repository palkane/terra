### 矿石配置 (ORE)  
**（需安装 'config-ore' 扩展插件）**  

此配置类型用于创建矿石结构实例。  

#### 继承参数  
- **`extends` List\<String\>**  
  从其他矿石配置中继承参数的列表：  
  - 仅当当前配置未定义某参数时才会使用继承配置中的参数  
  - 配置列表中靠前的配置具有更高优先级  
  - 支持跨配置文件参数复用  

#### 核心参数  
- **`id` String**  
  在其他配置中引用此矿石配置的唯一标识符（需在配置中声明两次）  

- **`material` Block**  
  矿石的主材质方块  

- **`replace` Set\<Block\>**  
  可被矿石替换的方块集合  

- **`size` Float**  
  矿石矿脉的生成尺寸  

#### 高级参数  
- **`material-overrides` Map\<Block, Block\>**  
  特定方块的材质覆写规则（原方块 → 替换方块）  
  *默认值: {}*  

- **`physics` Boolean**  
  是否启用物理效果（受重力影响等）  
  *默认值: false*  

> **配置示例**：  
> ```yaml
> id: "gold_ore"
> material: "minecraft:gold_ore"
> replace: ["minecraft:stone", "minecraft:deepslate"]
> size: 4.5
> material-overrides:
>   "minecraft:deepslate": "minecraft:deepslate_gold_ore"
> ```  
> 该配置会生成尺寸4.5的金矿石矿脉，可替换石头与深板岩，并自动将深板岩中的金矿石转为深板岩金矿石变种。