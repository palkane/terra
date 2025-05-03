### 修改现有配置包指南

本指南将介绍如何复制现有的Terra配置包并进行个性化修改。本文仅涵盖自定义修改的基础准备工作，不涉及具体地形生成功能的修改。在继续操作前，请确保已阅读《配置开发入门指南》。

如需从零创建配置包而非修改现有包，请参阅《从零创建配置包指南》。

⚠️ 注意  
本指南仅适用于Terra 6.0及以上版本！

---

#### **新建配置包步骤**

**1. 选择适配版本的配置包**  
- 如需修改Terra默认包，可在`packs`目录中找到`default.zip`，或访问[Overworld仓库](https://github.com/...)获取源码  
- 其他社区配置包请查阅[社区包列表](https://...)，确保所选包与当前Terra版本兼容

**2. 创建配置包目录**  
在`Terra/packs/`下新建文件夹（例如`custom-pack`），结构应如下：  
```
Terra/
├── packs/
│   ├── default.zip
│   └── custom-pack/  ← 新建的文件夹
```

**3. 复制原包内容**  
将目标包的完整内容（如为.zip需解压）复制到新建目录中。  
✅ 正确示例：  
```
Terra/packs/custom-pack/pack.yml  ← 清单文件直接位于包根目录
```  
❌ 错误示例：  
- 嵌套文件夹结构（如`custom-pack/folder/pack.yml`）  
- 保留原压缩包（如`custom-pack/original-pack.zip`）  
- 清单文件放错位置（如`packs/pack.yml`）

⚠️ 警告  
`packs`目录下的所有文件夹/压缩文件都会被尝试加载为配置包，请勿存放无关文件。

**4. 设置自定义包ID**  
打开`pack.yml`文件：  
- 找到`id:`字段  
- 替换为仅包含大写字母、数字和下划线的ID（如`CUSTOM`）  
✅ 有效示例：  
```yaml
id: CUSTOM
```  
❌ 无效示例：  
```yaml
id: custom id  # 含空格或小写字母
```

**5. 验证加载**  
启动客户端/服务器，若控制台显示：  
```
[XX:XX:XX INFO]: [Terra] Loading config pack "CUSTOM"
[XX:XX:XX INFO]: [Terra] CUSTOM <版本号> by <作者> loaded in XXXXms.
```  
即表示配置包已就绪，可开始修改！

---

### 关键说明
- 🔧 修改前建议备份原包  
- 📂 保持文件结构扁平化  
- 🔠 包ID需严格遵守命名规范  
- ℹ️ 更详细的配置修改指南请参阅官方文档