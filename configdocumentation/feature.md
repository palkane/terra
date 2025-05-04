### 地物配置（FEATURE）  
**（需安装 'config-feature' 扩展插件）**  

此配置类型用于创建地物实例。

#### 继承参数  
- **`extends` List\<String\>**  
  从其他地物配置中继承参数的列表。  
  - 仅当当前配置未定义某参数时才会使用继承配置中的参数  
  - 列表中的配置按顺序优先生效  
  - 该机制支持跨配置文件复用参数  

#### 核心参数  
- **`id` String**  
  用于在其他配置中引用此地物的唯一标识符  

- **`distributor` Distributor**  
  控制地物在生物群系中的分布方式  

- **`locator` Locator**  
  确定地物在区块中的具体生成位置  

#### 结构体生成参数  
- **`structures.distribution` NoiseSampler**  
  控制结构体分布密度的噪声采样器  

- **`structures.structures` WeightedList\<Structure\>**  
  可生成的结构体列表及其权重分布  

> **注**：  
> 1. 所有地物配置必须包含`distributor`和`locator`参数  
> 2. 结构体相关参数用于控制如废墟、洞穴等特殊地物的生成规则  
> 3. 权重列表中的数值决定不同结构体的生成概率比  

示例配置：
```yaml
id: "ancient_ruins"
extends: ["base_structures"]
distributor: 
  type: "uniform"
  density: 0.001
locator:
  type: "surface"
structures:
  distribution: "SIMPLEX@0.5"
  structures:
    - "small_ruin@5"
    - "large_ruin@1" 
```