### 配置中的数据定义  
本节将介绍配置文件中存储和编写数据的基本方式。由于Terra配置支持多种语言编写，我们使用标准化术语来指代不同语言中可能命名不同的概念。  

> **提示**  
> 如果您已熟悉数据结构和数据序列化语言，可以快速浏览本节内容或直接跳转到下一节。  

### 数据类型  
核心概念是**数据类型**（简称"类型"）。类型告诉我们：  
- 数据应如何定义  
- 数据预期包含的内容  
- 数据的用途  

通常配置中的数据类型会自动推断，但在许多情况下需要显式告知Terra使用的类型。  

配置中定义的数据单元称为**对象**。所有对象都可通过类型分类，其类型由配置中的定义方式决定。每个配置文件始终定义单个对象。  

#### 整数（Integer）  
让我们创建一个YAML格式的新配置文件，使用**整数**类型。整数表示没有小数部分的数字，写法如下：  

```yaml
# config.yml
42
```  
现在创建了一个表示数字42的整数类型配置。  

#### 浮点数（Float）  
与整数略有不同的数值类型是**浮点数**。区别在于浮点数可以表示含小数位的数字：  

```yaml
# config.yml
3.14159
```  
许多情况下需要区分整数和浮点数，因为某些配置可能不允许使用小数。通常要求整数的参数不接受浮点数，但要求浮点数的参数会接受整数。  

#### 布尔值（Boolean）  
**布尔值**类型表示两种状态的数据，通常用于"开启/关闭"场景：  

```yaml
# config.yml
true

# 或
false
```  

#### 字符串（String）  
文本数据使用**字符串**类型表示：  

```yaml
# config.yml
This is a config of type string.
```  
字符串广泛用于命名场景，例如指定生物群系中使用的方块ID。  

当字符串可能被误判为其他类型（如布尔值）时，可以用引号显式声明：  

```yaml
# config.yml
"true"
```  

#### 映射表（Map）  
单独使用数字或字符串时实用性有限，通过**映射表**类型可以为数据添加标签。映射表是对象的集合，其中：  
- 每个对象称为**值**  
- 每个值通过**键**对象标识  

下面创建键值均为字符串类型的映射表配置：  

```yaml
# config.yml
this is a key: this is a value
```  
映射表可以包含多个键值对：  

```yaml
# config.yml
string: Here is some text.
pi: 3.14159
meaning-of-life: 42
```  
由于配置文件只能包含一个主对象，映射表使我们能在单个配置中定义多个可标识的对象。  

> **注意**  
> 同一映射表中的键必须唯一，以下写法无效：  
> ```yaml
> duplicated key: value A
> duplicated key: value B
> ```  

#### 顺序无关性  
映射表中键值对的顺序不影响解析，以下两个配置完全等效：  

```yaml
# 配置A
a: 1234
b: Some text
c: true

# 配置B
b: Some text
c: true
a: 1234
```  

#### 列表（List）  
**列表**是另一种集合类型，与映射表的区别在于：  
- 列表中的对象称为**项**  
- 项通过位置索引而非键标识  
- 项的顺序具有意义  

包含多个字符串的列表示例：  

```yaml
# config.yml
- A string
- Another string
- The final string
```  

#### 对象嵌套  
由于映射表的值和列表的项可以是任意类型，因此支持多级嵌套结构：  

> **提示**  
> 在对象内部定义对象通常称为"嵌套"。  

**YAML嵌套规则**  
- 单行值直接写在键后（如`key: 42`）  
- 多行结构（如映射表/列表）需要缩进  
- 推荐使用2个空格缩进  

映射表嵌套示例：  
```yaml
baz:
  foo: a
  bar: b
```  

列表嵌套示例：  
```yaml
my-list:
  - item 1
  - item 2
```  

多级嵌套示例：  
```yaml
qux:
  baz:
    foo: a
    bar: b
```  

**常见错误**  
以下写法尝试为同一键赋予两个值，属于无效YAML：  
```yaml
key: foo
  baz: bar  # 错误！与上一行冲突
```  
修正方式：  
```yaml
# 方案1：保留映射表
key:
  baz: bar

# 方案2：保留字符串
key: foo
```  
错误可能源于键遗漏或缩进错误，修正示例：  
```yaml
# 补全键名
key: foo
missing:
  baz: bar

# 或调整缩进
key: foo
baz: bar  # 取消缩进变为同级键
```  

#### 综合示例  
结合所有类型创建复杂数据结构，如购物清单和预约记录：  

```yaml
# config.yml
shopping-list:
  - item: 1L Milk
    amount: 2
    cost-per-item: 2.0
  - item: Carton of Eggs
    amount: 1
    cost-per-item: 4.5

appointments:
  - name: Haircut Appointment
    date: 24.04.22
    start-time: 9:45
    end-time: 10:15
  - name: Doctor Appointment
    date: 13.05.22
    start-time: 3:15
    end-time: 4:15
```  
此配置包含：  
1. 顶层是映射表类型  
2. 包含两个键`shopping-list`和`appointments`  
3. 两个键的值都是列表类型  
4. 每个列表项又是映射表类型  

#### 语言特性语法  
某些序列化语言支持替代语法，例如YAML中可用花括号`{}`表示映射表，方括号`[]`表示列表：  

```yaml
config.yml
curly-brace-map: {
  "key-1": "value-1",
  "key-2": "value-2"
}

square-bracket-list: [
  item-1,
  item-2,
  item-3
]

# 单行写法
single-line-map: { "key-1": "value-1", "key-2": "value-2" }
single-line-list: [ item-1, item-2, item-3 ]

# 空结构
empty-map: {}
empty-list: []
```  

#### YAML锚点  
YAML的锚点系统支持配置内数据复用：  

```yaml
config.yml
some-list-of-data: &the-data-anchor  # 定义锚点
  - item-1
  - item-2

somewhere-where-data-is-reused: *the-data-anchor  # 引用锚点
```  
解析后`somewhere-where-data-is-reused`的值将与锚点定义列表完全相同。