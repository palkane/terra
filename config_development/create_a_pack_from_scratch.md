从零开始创建配置包  
本指南将概述从头开始创建全新Terra配置包的流程。在继续之前，请确保已阅读《配置开发入门指南》以获取更多信息。  

若您遇到困难或需要参考示例，可在GitHub仓库中找到本指南的示例配置包。  

如果您希望修改现有配置包而非从零创建，请参阅《修改现有配置包》指南。  

### 新建配置包流程  
**步骤说明**  

1. **创建配置包目录**  
   进入您的配置包根目录（packs目录），新建一个文件夹（名称可自定义）。该目录将存放所有配置文件。  

2. **创建包清单文件**  
   在配置包目录中新建名为`pack.yml`的配置文件。  

   包清单文件用于定义全局信息（如作者、版本号、依赖的Minecraft版本及插件），这些是配置包正常加载的必要条件。  

3. **设置包ID与版本号**  
   用文本编辑器打开`pack.yml`文件，添加以下参数：  
   ```yaml  
   id: YOUR_PACK_ID  
   version: 0.1.0  
   ```  
   （参数即"配置选项"，完整说明请参阅《配置系统》文档）  

   注意限制条件：  
   - 包ID仅允许字母、数字、连字符或下划线（不可含空格）  
   - 惯例使用全大写字母（非强制要求）  
   - 版本号必须符合SemVer规范的X.Y.Z格式  

   可选添加作者信息：  
   ```yaml  
   author: YOUR_USERNAME # 可选  
   ```  
   > **提示**  
   > 参数顺序不影响功能（如示例1/2/3等效），空行会被自动忽略。  

4. **指定配置文件格式**  
   Terra要求明确配置文件格式（参见《配置文件》文档）。本指南使用`language-yaml`插件提供的YAML格式（Terra标准配置语言），通过包清单的`addons`参数引入：  
   ```yaml  
   addons:  
     language-yaml: 1.+  
   ```  
   > **注意**  
   > 配置包内所有文件必须使用清单声明的格式。但包清单本身只需匹配任意已安装的语言插件格式（建议与其声明的格式一致）。  

5. **指定区块生成器**  
   区块生成器决定地形基础结构（装饰前），通过插件实现。本指南使用`NOISE_3D`生成器（需`chunk-generator-noise-3d`核心插件）：  
   ```yaml  
   addons:  
     chunk-generator-noise-3d: 1.+  
   generator: NOISE_3D  
   ```  
   > **提示**  
   > 后续添加插件均参照此流程操作。  

   `NOISE_3D`生成器还需定义采样器（sampler）和调色板（palette），需额外添加插件：  
   ```yaml  
   addons:  
     config-noise-function: 1.+  
     palette-block-shortcut: 1.+  
   ```  

6. **创建首个生物群系**  
   1. 添加`config-biome`插件（版本`1.+`），该插件提供`BIOME`配置类型  
   2. 新建配置文件（本指南命名为`first_biome.yml`）  
   3. 设置配置类型与ID：  
      ```yaml  
      id: FIRST_BIOME  
      type: BIOME  
      ```  
   4. 设置`vanilla`参数为原版生物群系ID（示例使用`minecraft:plains`，可替换为任意有效ID）：  
      ```yaml  
      vanilla: minecraft:plains  
      ```  
      > Terra通过此参数处理生物生成、草地颜色等（具体行为因运行平台而异）  

7. **配置生成器参数**  
   - **terrain.sampler**：地形形状控制器  
     当前使用以下配置生成Y=64的平坦地形：  
     ```yaml  
     terrain:  
       sampler:  
         type: LINEAR_HEIGHTMAP  
         base: 64  
     ```  
     （原理将在后续指南详解）  

   - **palette**：地形组成方块定义  
     参数接收键值对列表，键为调色板配置，值为应用该调色板的最高Y坐标。  
     示例：  
     ```yaml  
     palette:  
       - BLOCK:minecraft:stone: 319  
     ```  
     表示Y=319至世界底部均使用`minecraft:stone`方块（`palette-block-shortcut`插件支持快捷定义单方块调色板）。  

8. **定义生物群系分布器**  
   需通过`biomes`参数指定分布器。本指南使用`SINGLE`分布器（需`biome-provider-single`核心插件）：  
   ```yaml  
   addons:  
     biome-provider-single: 1.+  
   biomes:  
     type: SINGLE  
     biome: FIRST_BIOME  
   ```  
   此配置会使`FIRST_BIOME`在整个世界生成。  

9. **加载配置包**  
   此时您的配置包已具备生成世界的能力！启动包含该包的开发客户端/服务器，通过`/packs`命令或启动日志确认包是否加载成功。若加载失败，控制台将显示错误信息，请根据提示检查配置步骤。  

   若问题仍无法解决，请联系支持团队并提供相关错误日志。  

### 完成验证  
确认配置包加载成功后，即可使用该包生成新世界！  

本指南的完整参考配置可在[GitHub仓库](https://github.com/PolyhedralDev/TerraPackFromScratch/tree/master/1-setting-up)查看。