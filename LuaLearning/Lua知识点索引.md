# Lua 知识点索引

> 参考：Roberto Ierusalimschy《Programming in Lua》
> 📄 [[Programming in Lua 学习笔记|详细学习笔记 →]]
> 🔙 [返回项目索引 →](../计算机网络知识索引.md)

---

## 第一篇  语言核心

### 第0章  序言
- Lua 的设计哲学：简洁、可嵌入、可扩展
- Lua 的应用场景：游戏脚本、嵌入式、Web、配置
- Lua 与其他语言的对比（Python、JavaScript）
- 本书体例与学习建议

### 第1章  起点
- [[Programming in Lua 学习笔记#1.1 Chunks|Chunk（代码块）]]
- [[Programming in Lua 学习笔记#1.2 全局变量|全局变量]]：无需声明，默认全局
- [[Programming in Lua 学习笔记#1.3 词法约定|词法约定]]：标识符、保留字、注释
- [[Programming in Lua 学习笔记#1.4 命令行|命令行]]运行方式

### 第2章  类型和值 ——★★★ 核心基础
- [[Programming in Lua 学习笔记#第2章 类型和值 ——★★★ 核心基础|8 种基础类型]]
- **`false` 和 `nil` 外皆为真**（`0` 和 `""` 也是 `true`）
- `number` 双精度浮点，`string` 不可变
- `function` 是一等公民，`table` 是唯一的数据结构
- 自动类型转换规则

### 第3章  表达式
- [[Programming in Lua 学习笔记#3.1~3.5 运算符|运算符]]：`+ - * / ^` `and or not` `..`
- `and`/`or` 返回操作数而非布尔值
- [[Programming in Lua 学习笔记#3.6 表的构造 ——★★★ 核心|表的构造]]：列表式、记录式、混合式、通用式

### 第4章  基本语法
- [[Programming in Lua 学习笔记#4.1 赋值语句|多重赋值]]：`a, b = 1, 2`
- [[Programming in Lua 学习笔记#4.2 局部变量 ——★★★ 重要|局部变量]]：`local`，块级作用域
- [[Programming in Lua 学习笔记#4.3 控制结构|控制结构]]：`if` `while` `repeat` `for`
- [[Programming in Lua 学习笔记#4.4 break 和 return ——★★ 限制|break/return 的块尾限制]]

### 第5章  函数 ——★★★ 核心
- [[Programming in Lua 学习笔记#5.1 多返回值|多返回值]]：`return a, b`
- [[Programming in Lua 学习笔记#5.2 可变参数|可变参数]]：`...`
- [[Programming in Lua 学习笔记#5.3 命名参数|命名参数]]：用表模拟

### 第6章  再论函数 ——★★★ 高阶
- [[Programming in Lua 学习笔记#6.1 闭包（Closure）|闭包]]：函数 + 捕获的外部变量
- [[Programming in Lua 学习笔记#6.2 非全局函数|非全局函数]]
- [[Programming in Lua 学习笔记#6.3 尾调用（Proper Tail Call）|尾调用消除]]

### 第7章  迭代器与泛型 for
- [[Programming in Lua 学习笔记#7.1 迭代器与闭包|迭代器与闭包]]
- [[Programming in Lua 学习笔记#7.2 泛型 for 语义|泛型 for 语义]]（`_f, _s, _var`）
- [[Programming in Lua 学习笔记#7.3~7.4 无状态与多状态迭代器|无状态/多状态迭代器]]

### 第8章  编译·运行·代码管理
- [[Programming in Lua 学习笔记#8.1 loadfile / loadstring / dofile|loadfile/dofile/loadstring]] 区别
- [[Programming in Lua 学习笔记#8.2 require|require]] 的加载与缓存
- [[Programming in Lua 学习笔记#8.3~8.4 错误与异常|错误处理]]：`pcall` / `xpcall` / `error`

### 第9章  协同程序（Coroutine） ——★★★
- [[Programming in Lua 学习笔记#第9章 协同程序（Coroutine）|协同 vs 线程]]（协作式 vs 抢占式）
- `coroutine.create` / `resume` / `yield`
- [[Programming in Lua 学习笔记#第9章 协同程序（Coroutine）|生产者-消费者模式]]
- [[Programming in Lua 学习笔记#第9章 协同程序（Coroutine）|协程作迭代器]]

### 第10章  完整示例
- [[Programming in Lua 学习笔记#第10章 完整示例|Lua 作为数据描述语言]]
- 马尔可夫链文本生成

---

## 第二篇  表与对象

### 第11章  数据结构 ——★ 实践重点
- [[Programming in Lua 学习笔记#第11章 数据结构 ——★ 实践重点|数组]]：索引从 1 开始
- [[Programming in Lua 学习笔记#第11章 数据结构 ——★ 实践重点|矩阵/链表/队列/集合]]实现
- [[Programming in Lua 学习笔记#第11章 数据结构 ——★ 实践重点|字符串缓冲]]：`table.concat` 避免 O(n²)

### 第12章  数据文件与持久化
- [[Programming in Lua 学习笔记#第12章 数据文件与持久化|序列化]]：`string.format("%q", s)`
- 带循环引用的序列化

### 第13章  Metatables 与 Metamethods ——★★★ 核心
- [[Programming in Lua 学习笔记#第13章 Metatables 与 Metamethods|元表]]：`setmetatable` / `getmetatable`
- `__index`（读不存在的 key）`__newindex`（写不存在的 key）
- 运算符重载：`__add` `__eq` `__call` `__tostring`
- [[Programming in Lua 学习笔记#第13章 Metatables 与 Metamethods|只读表 / 默认值表 / 表代理]]

### 第14章  环境
- 全局变量在 `_G` 表中
- [[Programming in Lua 学习笔记#第14章 环境|`setfenv`]]：改变函数环境

### 第15章  Packages ——★★ 工程实践
- [[Programming in Lua 学习笔记#第15章 Packages|table + local 实现包]]
- 私有成员、`setfenv` 简化包

### 第16章  面向对象 ——★★★ 核心
- [[Programming in Lua 学习笔记#第16章 面向对象程序设计|类模板]]：`self.__index = self`
- 冒号语法：`obj:method()` ≡ `obj.method(obj)`
- [[Programming in Lua 学习笔记#第16章 面向对象程序设计|单继承 / 多重继承 / 私有性]]

### 第17章  Weak 表
- [[Programming in Lua 学习笔记#第17章 Weak 表|弱引用]]：`__mode = "k"/"v"/"kv"`
- 记忆函数、关联对象属性、默认值表

---

## 第三篇  标准库

### 第18章  数学库
- `math.abs`、`math.sin`、`math.max`、`math.min`
- `math.random` / `math.randomseed`
- `math.pi`、`math.huge`

### 第19章  Table 库
- `table.getn` / `#t`：数组大小
- `table.insert` / `table.remove`
- `table.sort`（可自定义比较函数）
- `table.concat`

### 第20章  String 库 ——★★ 常用
- `string.len` / `#s`
- `string.sub`、`string.lower`、`string.upper`
- **模式匹配**：`string.find`、`string.match`、`string.gsub`、`string.gmatch`
- Lua 模式语法（类似正则但不同）：`.` `%a` `%d` `*` `+` `?` `-`
- 捕获（Captures）：`()` 提取子串

### 第21章  I/O 库
- 简单 I/O：`io.input` / `io.output` / `io.read` / `io.write`
- 完全 I/O：`file:read` / `file:write` / `file:lines`
- 文件打开模式：`"r"` `"w"` `"a"` `"r+"` `"w+"`

### 第22章  操作系统库
- `os.date` / `os.time`：日期时间
- `os.execute`：执行系统命令
- `os.getenv`：环境变量
- `os.rename` / `os.remove`：文件操作

### 第23章  Debug 库
- 自省（Introspective）：`debug.getinfo`、`debug.getlocal`
- Hooks：`debug.sethook`
- Profiling 性能分析

---

## 第四篇  C API ——★ 进阶

### 第24章  C API 纵览
- Lua 与 C 交互的基本原理
- **虚拟栈**：Lua 和 C 之间交换数据的核心机制
- 第一个 C 示例程序
- C API 错误处理

### 第25章  用 Lua 扩展你的程序
- 从 C 中操作 Lua 表
- 从 C 中调用 Lua 函数
- 通用的函数调用方法

### 第26章  从Lua中调用 C 函数
- 在 C 中编写可被 Lua 调用的函数
- 注册 C 函数库：`luaL_register`

### 第27章  撰写 C 函数的技巧
- 数组操作（传递 Lua 表到 C）
- 字符串处理
- 在 C 函数中保存状态（closures、注册表）

### 第28章  C 中的自定义类型（Userdata）
- Userdata 的创建与使用
- 为 Userdata 设置 Metatables
- 面向对象式访问
- Light Userdata

### 第29章  资源管理
- 目录迭代器
- XML 解析示例

---

## 附录：Lua 核心语法速查

### 关键语法

| 语法 | 示例 |
|------|------|
| 注释 | `-- 单行` 或 `--[[ 多行 ]]--` |
| 变量 | `local x = 10` |
| 函数 | `function f(a, b) return a + b end` |
| 表 | `t = {1, 2, key = "v"}` |
| 索引 | `t[1]` 或 `t.key` |
| if | `if a > b then ... elseif ... else ... end` |
| for | `for i=1,10 do ... end` |
| while | `while cond do ... end` |
| repeat | `repeat ... until cond` |
| 泛型for | `for k,v in pairs(t) do ... end` |

### 核心设计哲学

| 特性 | 说明 |
|------|------|
| 类型系统 | 动态类型，8 种基础类型 |
| 数据结构 | 表（table）是唯一的数据结构，实现数组/字典/对象 |
| 一等函数 | 函数是变量，支持闭包、匿名函数、高阶函数 |
| 元表 | 用 metatable 实现运算符重载、面向对象 |
| 协程 | 协作式多任务，用于异步编程 |
| 可嵌入 | C API 实现 Lua↔C 双向调用 |

### 常见坑

| 坑 | 说明 |
|----|------|
| 索引从 1 开始 | `t[1]` 是第一个元素，`#t` 不算非连续索引 |
| 只有 false/nil 为假 | `0` 和 `""` 都是 `true` |
| 全局变量默认 | 不加 `local` 就是全局，这是常见 bug |
| `..` 性能差 | 大量字符串连接用 `table.concat` |
| `return` 限制 | 必须在块的最后一句，不可在 `do` 的中间 |

---

> **注**：本书为 Lua 5.1 版本，部分特性在 Lua 5.2+ 有所变化（环境操作、module 等），学习时请注意版本差异。
