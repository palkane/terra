### 元配置系统  
元配置是Terra配置系统的可选功能，允许根据配置包中其他位置的定义来设置参数，类似于编程语言中的变量机制。该功能适用于以下场景：  
- 集中定义多处复用的值  
- 创建易修改的自定义参数  
- 提升配置组织性  

#### 引用机制  
通过`<文件路径>:<父级键链>`的格式引用值，其中：  
- 文件路径相对于配置包目录  
- 父级键链用点号分隔多级键名  

例如引用`config.yml`中的字符串`example`：  
```yaml
# config.yml
path:
  to:
    config:
      value: example
```  
引用格式为：`config.yml:path.to.config.value`  

> **注意**  
> 直接引用会被视为普通字符串，需配合以下元配置语法使用  

#### 元值（MetaValue）  
最基础的元配置形式，通过在引用前添加`$`符号声明：  
```yaml
thing: $config.yml:my.value
```  
此时`thing`将被赋值为`config.yml`中定义的`something`：  
```yaml
# config.yml
my:
  value: something
```  
适用于大多数支持元配置的参数。  

#### 元列表（MetaList）  
通过`<< <引用>`语法合并列表项。例如：  
```yaml
things:
  - ONE
  - << config.yml:another.list
  - FOUR
```  
会合并`config.yml`中定义的列表项：  
```yaml
# config.yml
another:
  list:
    - TWO
    - THREE
```  
最终`things`按顺序包含`[ONE, TWO, THREE, FOUR]`。集合（Set）类型也适用此语法（但不保留顺序）。  

支持多次合并：  
```yaml
things:
  - ONE
  - << config.yml:lists.first
  - FOUR
  - << config.yml:lists.second
  - NINE
```  

#### 元映射表（MetaMap）  
基于YAML的合并键特性（需引号包裹`<<`键），通过引用列表实现映射表合并：  
```yaml
things:
  one: ONE
  "<<":
    - config.yml:another.map
```  
合并`config.yml`中的映射表：  
```yaml
# config.yml
another:
  map:
    two: TWO
    three: THREE
```  
等效结果为：  
```yaml
things:
  one: ONE
  two: TWO
  three: THREE
```  

##### 合并优先级规则  
当多映射表存在相同键时：  
1. 最后被引用的映射表中的值优先  
2. 被合并的映射表（含`<<`的）优先级最低  

示例：  
```yaml
# 主配置
map:
  "<<":
    - config.yml:first
    - config.yml:second
    - config.yml:third
  key: Base
```  
```yaml
# config.yml
first:
  key: First
second:
  key: Second
  extra: Extra value
third:
  key: Third
```  
最终`map`等效于：  
```yaml
map:
  key: Third      # 最后引用的third覆盖前值
  extra: Extra value  # second中的唯一键被保留
```  

#### 元字符串（MetaString）  
通过`${<引用>}`语法在字符串中插入值：  
```yaml
thing: ${config.yml:thing1}, ${config.yml:thing2}!
```  
插入`config.yml`中的值：  
```yaml
# config.yml
thing1: Hello
thing2: World
```  
最终`thing`变为字符串`Hello, World!`。  

> **限制**  
> 被引用值必须是字符串/整数/浮点数/布尔值或可解析为这些类型的元配置  

#### 元数字（MetaNumber）  
当参数需要数值类型时，可直接提供表达式：  
```yaml
number: 3 * 2      # 解析为6
number: 2 * ${config.yml:multiplier}  # 支持引用值
```  
计算流程：  
1. 先应用元字符串替换  
2. 再解析表达式得出最终值  

#### 解析顺序  
当元配置与YAML语法共用时：  
1. 先进行YAML解析  
2. 再执行元预处理  

示例：  
```yaml
# foo.yml
to-merge: &anchor
  - bar.yml:map-a
  - bar.yml:map-b

parameter:
  key-a: alpha
  "<<": *anchor
```  
```yaml
# bar.yml
map-a:
  key-b: bravo
map-b:
  key-c: charlie
```  
**处理流程**：  
1. YAML锚点展开后：  
   ```yaml
   parameter:
     key-a: alpha
     "<<":
       - bar.yml:map-a
       - bar.yml:map-b
   ```  
2. 元映射表处理最终生成：  
   ```yaml
   parameter:
     key-a: alpha
     key-b: bravo
     key-c: charlie
   ```