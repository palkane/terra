# 从零开始创建地貌特征  
本指南将详细介绍如何从头创建新的地貌特征。  

在继续之前，请确保已阅读《配置开发入门》和《从零创建配置包》指南。如需更深入的技术文档，请参阅非官方开发指南《特征配置详解》。  

## 新建地貌特征流程  
### 步骤说明  

1. **添加生成阶段**  
   地貌生成分为多个阶段。您的配置包需要至少定义一个生成阶段才能生成任何特征。  

   在包清单中添加`generation-stage-feature`插件（版本`1.+`）：  
   ```yaml  
   addons:  
     generation-stage-feature: "1.+"  
   ```  

   添加生成阶段配置（本指南仅使用`trees`阶段）：  
   ```yaml  
   stages:  
     - id: trees  
       type: FEATURE  
     - id: landforms  
       type: FEATURE  
   ```  
   > **提示**  
   > 阶段ID可自定义，生成顺序遵循从上到下的定义顺序。  

2. **创建结构文件**  
   结构文件支持动态的TerraScript（`.tesf`）或静态的schematic（`.schem`）格式。  

   **TerraScript方案**  
   1. 添加`structure-terrascript-loader`插件（版本`1.+`）  
   2. 创建空白`.tesf`文件（示例使用`oak_tree.tesf`）  
   3. 编写TerraScript代码生成结构（示例代码见下文）  

   **示例橡树结构代码**  
   ```javascript  
   num height = 5+randomInt(3);  
   // ...（完整代码见原始文档）  
   for (num h = 0; h < height; h = h + 1) block(0,h,0,"minecraft:oak_log");  
   // ...（树叶生成逻辑）  
   ```  

   **Schematic方案**  
   （省略具体步骤）  

3. **创建特征配置文件**  
   添加`config-feature`插件（版本`1.+`）：  
   ```yaml  
   addons:  
     config-feature: "1.+"  
   ```  

   新建配置文件（示例`oak_trees.yml`）并设置基础参数：  
   ```yaml  
   id: OAK_TREES  
   type: FEATURE  
   ```  

4. **配置特征分布器**  
   分布器决定特征在X/Z轴的生成位置。  

   添加`config-distributors`插件（版本`1.+`），使用`PADDED_GRID`分布类型：  
   ```yaml  
   distributor:  
     type: PADDED_GRID  
     width: 12    # 单元格尺寸  
     padding: 4   # 单元格间距  
     salt: 5864   # 随机偏移种子  
   ```  
   > **说明**  
   > - `salt`用于防止特征重叠  
   > - 其他分布器类型详见[文档链接]  

5. **配置特征定位器**  
   定位器决定特征在Y轴的生成位置。  

   添加`config-locators`插件（版本`1.+`），使用`TOP`定位类型：  
   ```yaml  
   locator:  
     type: TOP  
     range:  
       min: 0  
       max: 319  
   ```  
   > **高级技巧**  
   > 可使用`AND`组合多个定位器（示例代码见原始文档）  

6. **添加结构配置**  
   在特征配置中添加结构定义：  
   ```yaml  
   structures:  
     distribution:  
       type: CONSTANT  # 结构选择方式  
     structures: oak_tree  # 结构文件ID  
   ```  
   > **进阶用法**  
   > 支持通过权重列表随机选择结构（配合噪声采样器）  

7. **将特征应用到生物群系**  
   在生物群系配置（如`FIRST_BIOME`）中添加特征引用：  
   ```yaml  
   features:  
     trees:  
       - OAK_TREES  
   ```  
   > **多阶段配置示例**  
   > ```yaml  
   > features:  
   >   trees:  
   >     - OAK_TREES  
   >   landforms:  
   >     - ROCKS  
   > ```  

8. **加载配置包**  
   启动开发客户端/服务器验证：  
   - 使用`/packs`命令确认包加载成功  
   - 检查控制台错误信息（如有）  

## 完成验证  
确认配置包加载后，即可在游戏中生成包含橡树特征的世界！  

> **故障排除**  
> 若遇到问题，请：  
> 1. 仔细阅读控制台报错  
> 2. 逐步复查配置步骤  
> 3. 提供错误日志联系技术支持