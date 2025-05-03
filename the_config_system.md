配置系统  
本节将介绍以下内容：  

- Terra如何定义配置的结构规范  
- Terra如何解析并运用这些结构化配置  
- 如何编写符合规范结构的配置  

### 模板  
大多数配置遵循我们称为"模板"的规范。模板可视为特殊类型的映射表（map），它规定了内部键值对的结构规则。Terra定义了多种不同的模板，每种模板都可视为独立类型（就像字符串和整数那样）。  

### 参数  
模板的核心组成部分是其参数规范。参数决定了数据的实用性——Terra中几乎所有世界生成行为都由参数控制。  

模板中的参数规范包括：  
1. 参数名称（也是模板内引用该参数的方式）  
2. 参数是否可选（optional）或必需（required）  
3. 参数允许设置的类型  

在模板中设置参数时，键（key）表示要设置的参数名，值（value）表示参数值。例如下面是Terra配置包清单中定义的几个参数，提供了包的基本信息：  

```yaml
# pack.yml
id: COOL_CONFIG_PACK
version: 1.0.0
author: Anon Y. Mous
```

参数也可以嵌套在多级映射表中。例如下方配置中，顶层对象遵循某个模板，其嵌套参数值为"a parameter value"：  

```yaml
# config.yml
a:
  nested:
    parameter: a parameter value
```

在模板中引用参数时，我们使用父级键名加`.`分隔的组合来标识目标参数。以上述配置为例，该参数应表示为`a.nested.parameter`。  

需要记住的参数规范：  
- 参数的父级键名总是小写字符串，用连字符`-`替代空格  
- 值对象的类型即为参数类型  

### 使用模板  
假设我们创建了一个名为`AnimalTemplate`的新模板，该类型规定：  
- 必需参数`color`（字符串类型）  
- 必需参数`legs`（整数类型）  

若顶层对象是`AnimalTemplate`类型，可编写如下配置：  

```yaml
# koala.yml
color: grey
legs: 4
```

如果配置不符合模板规范，Terra将加载失败。例如下方配置会因两个原因报错：1. 缺少必需参数`color`；2. `legs`不是要求的整数类型：  

```yaml
# koala.yml
legs: two
```

`AnimalTemplate`的文档可能如下所示：  
```
AnimalTemplate  
定义动物属性  

必需参数：  
color  String  动物颜色  
legs   Integer 动物腿数  
```

现在让我们创建一个描述动物园的新模板：  

```
ZooTemplate  
定义动物集合  

必需参数：  
animals  Map[String:AnimalTemplate]  动物集合  

可选参数：  
description  String  动物园及其动物的描述  
```

`ZooTemplate`的特殊之处在于将`AnimalTemplate`作为`animals`参数的值类型。这种将模板作为类型使用的能力，是Terra配置系统的核心特性之一。  

现在可以在`ZooTemplate`中使用`AnimalTemplate`创建配置：  

```yaml
# australian_zoo.yml
description: A zoo of Australian animals.
animals:
  koala:
    color: grey
    legs: 4
  kangaroo:
    color: brown
    legs: 2
```

### 配置类型  
现在遇到新问题：Terra如何知道配置应该使用哪个模板？对于顶层对象，没有父模板来指定类型，那么是什么让Terra选择`ZooTemplate`而非其他模板？  

#### 选择模板  
这时`type`参数就派上用场了。`type`是标准化指定映射表所遵循模板的方式，适用于可能存在多个模板的场景。  

#### 注册表  
要让Terra使用`ZooTemplate`，需要将`type`设置为注册表键（registry key）。注册表可视为Terra内部用来存储同类项的映射表，注册表键的工作原理与映射表键相同。  

假设`ZooTemplate`已在Terra内部以`ZOO`为键名注册到配置注册表中。现在我们只需在配置中添加`type: ZOO`：  

```yaml
# australian_zoo.yml
type: ZOO
description: A zoo of Australian animals.
animals:
  koala:
   color: grey
   legs: 4
  kangaroo:
   color: brown
   legs: 2
```

这样我们就创建了Terra能识别的新配置类型。  

### 注册配置  
现在可以轻松创建多个动物园配置，但如何跟踪这些配置？如何在其他配置中引用特定动物园？  

解决方案是利用注册表。假设某个已安装插件创建了动物园注册表（zoo registry），我们只需创建`ZOO`类型配置，Terra就会自动注册它们。  

为了控制注册表键名，可以在`ZooTemplate`中添加`id`参数。当Terra自动注册配置时，`id`的值将作为注册表键名，这样就能通过动物园注册表全局访问所有`ZOO`配置。  

最终配置示例如下：  

```yaml
# australian_zoo.yml
id: AUSTRALIAN_ZOO
type: ZOO
description: A zoo of Australian animals.
animals:
  koala:
   color: grey
   legs: 4
  kangaroo:
   color: brown
   legs: 2
```

现在任何需要引用动物园的配置中，只需指定`AUSTRALIAN_ZOO`，Terra就会自动从动物园注册表中获取对应配置。