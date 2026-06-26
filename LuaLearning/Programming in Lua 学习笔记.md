# Programming in Lua 学习笔记

> 参考：Roberto Ierusalimschy《Programming in Lua》
> 🔙 [[Lua知识点索引|返回索引]]

---

## 第一篇  语言核心

### 第0章  序言

Lua 设计哲学：**可扩展性**、**简洁**、**高效**、**平台无关**。完全用 ANSI C 编写，动态类型 + 自动内存管理 + 匿名函数。三大用户群体：嵌入应用、独立使用、与C混合。

### 第1章  起点

#### 1.1 Chunks

Lua 的代码块（chunk）是一系列可执行语句，可以是文件，也可以是交互模式下键入的每一行。

```lua
dofile("lib1.lua")     -- 加载并执行文件
dofile("lib2.lua")
```

#### 1.2 全局变量

**全局变量不需声明**，未初始化值为 `nil`，赋值 `nil` 即删除变量。

```lua
b = 10     -- 创建
b = nil   -- 删除
```

#### 1.3 词法约定

- 标识符：字母/下划线开头，大小写敏感
- 保留字：`and break do else elseif end false for function if in local nil not or repeat return then true until while`
- 注释：`--` 单行，`--[[ ]]--` 多行
- 分号可选，同一行多个语句建议加分号

#### 1.4 命令行

```bash
lua -e "sin=math.sin" script a b       # -e 传命令，-l 加载库，-i 交互模式
```

全局 `arg` 表：`arg[0]=脚本名`，`arg[1..n]=脚本参数`，`arg[-1..]=前面的选项`

---

### 第2章  类型和值  ——★★★ 核心基础

Lua 共有 **8 种基础类型**：`nil`、`boolean`、`number`、`string`、`function`、`userdata`、`thread`、`table`。

> ★★★ **判断真假规则**：Lua 中除 `false` 和 `nil` 外全部为真，**`0` 和空字符串 `""` 都是 `true`**。

| 类型 | 要点 |
|------|------|
| `nil` | 唯一值，表示"无值"；赋 `nil` 即删除全局变量 |
| `boolean` | `false` 和 `true`；`0` 和 `""` 都是 `true`（与其他语言不同） |
| `number` | 双精度浮点（Lua 5.1），无整数；Lua 5.3+ 区分 integer/float |
| `string` | **不可变**，8-bit clean（可含 `\0`）；`..` 连接，`#` 求长 |
| `function` | **一等公民**：可赋值给变量、作参数、作返回值 |
| `table` | **唯一的数据结构**：实现数组、字典、对象、模块等 |
| `userdata` | C 指针的 Lua 封装；`light userdata` = `void*` |
| `thread` | 协程（coroutine）的类型 |

```lua
print(type("Hello"))        --> string
print(type(10.4*3))         --> number
print(type(print))          --> function

-- 自动类型转换
print("10" + 1)        --> 11   (string→number)
print(10 .. 20)        --> 1020  (number→string)
print(10 == "10")      --> false (不同类型永远不等)

-- 显式转换
n = tonumber(line)
s = tostring(10)

-- 多行字符串：[[ ]]
page = [[
<html>...</html>
]]
```

---

### 第3章  表达式

#### 3.1~3.5 运算符

```lua
-- 算术：+ - * / ^ (^ 是右结合)
-- 关系：< > <= >= == ~=  （不同类型比较报错）
-- 逻辑：and or not  返回操作数而非布尔值
-- 连接：..

-- 逻辑运算符的"短路"返回值
print(4 and 5)        --> 5
print(nil and 13)     --> nil
print(4 or 5)         --> 4
print(false or 5)     --> 5

-- 三元运算符模拟
x = (a and b) or c    -- 等价于 C 的 a ? b : c

-- 默认值惯用法
x = x or v
```

> ★★★ **只有 `false` 和 `nil` 为假**。这意味着 C 语言中 `0` 和 `""` 在 Lua 中都是 `true`。

#### 3.6 表的构造 ——★★★ 核心

```lua
-- 列表式（数组）
days = {"Sun","Mon","Tue","Wed","Thu","Fri","Sat"}  -- days[1] = "Sun"

-- 记录式（字典）
a = {x=0, y=0}          -- a.x = 0, a.y = 0

-- 通用式（key 可以是任意表达式）
opnames = {["+"] = "add", ["-"] = "sub"}

-- 混合式
polyline = {color="blue", thickness=2, {x=0,y=0}, {x=-10,y=0}}
```

> ★ 数组下标索引**从 1 开始**，不是 0！

---

### 第4章  基本语法

#### 4.1 赋值语句

```lua
-- 多重赋值：先计算右边所有值，再依次赋值
x, y = y, x                     -- 交换变量
a, b, c = 0, 1                  -- c = nil（多余的被丢弃）
a, b = 1, 2, 3                  -- a=1, b=2（多余的被丢弃）
```

#### 4.2 局部变量 ——★★★ 重要

`local` 关键字声明，仅在其代码块内有效。**尽量使用局部变量**：避免命名污染，访问更快。

```lua
do
  local a2 = 2*a
  local d = sqrt(b^2 - 4*a*c)
  x1 = (-b + d)/a2
end
-- a2 和 d 在此处已不可见
```

#### 4.3 控制结构

```lua
-- if
if a < 0 then
  a = 0
elseif a == 0 then
  a = 1
else
  a = a + 1
end

-- while
while a < 10 do
  a = a + 1
end

-- repeat until（类似 do-while）
repeat
  line = io.read()
until line ~= ""

-- 数值 for
for i=1, 10 do print(i) end
for i=10, 1, -1 do print(i) end     -- 递减

-- 泛型 for
for k, v in pairs(t) do print(k, v) end
for i, v in ipairs(a) do print(i, v) end
```

#### 4.4 break 和 return ——★★ 限制

`break` 和 `return` **必须是块的最后一句**（`end` / `else` / `until` 之前）。可用 `do..end` 绕过此限制。

```lua
-- 错误写法
function f()
  return   -- 语法错误
  x
end

-- 正确写法（用 do end 包装）
do return end
```

---

### 第5章  函数 ——★★★ 核心

#### 5.1 多返回值

```lua
function maximum(a)
  local mi = 1; local m = a[mi]
  for i, val in ipairs(a) do
    if val > m then mi = i; m = val end
  end
  return m, mi       -- 返回最大值和它的位置
end

-- 多返回值在表达式末尾位置接收全部，其他位置只取第一个
x, y = foo2()        -- 接收全部
x = foo2()           -- 只取第一个
print((foo2()))      -- 括号强制只取第一个

-- unpack：展开数组为参数列表
f(unpack(a))
```

#### 5.2 可变参数

```lua
-- ... 表示可变参数，自动在 arg 表中
function print(...)
  for i, v in ipairs(arg) do
    io.write(tostring(v) .. "\t")
  end
end

function g(a, b, ...) end
-- g(3)     → a=3, b=nil, arg={n=0}
-- g(3,4,5,8) → a=3, b=4, arg={5,8; n=2}
```

#### 5.3 命名参数

Lua 不支持语法级命名参数，用**表构造器**模拟：

```lua
rename{old="temp.lua", new="temp1.lua"}

function rename(arg)
  return os.rename(arg.old, arg.new)
end
```

> `rename{old="x",new="y"}` 等价于 `rename({old="x",new="y"})`—当实参是表构造器或字符串时，可省略括号。

---

### 第6章  再论函数 ——★★★ 高阶重点

#### 6.1 闭包（Closure）

闭包 = **函数 + 它捕获的外部变量（upvalue）**。每次调用闭包工厂产生独立的 upvalue 实例。

```lua
function newCounter()
  local i = 0
  return function ()
    i = i + 1
    return i
  end
end

c1 = newCounter()    -- c1 有自己的 i
c2 = newCounter()    -- c2 有自己的 i
print(c1())    --> 1
print(c1())    --> 2
print(c2())    --> 1
```

#### 6.2 非全局函数

```lua
-- 局部匿名函数
local f = function(x) return x^2 end

-- 递归局部函数需前向声明
local fact
fact = function(n)
  if n == 0 then return 1
  else return n * fact(n-1) end
end
```

#### 6.3 尾调用（Proper Tail Call） ——★★★ 重要

> 当函数**最后唯一动作**是调用另一个函数时，Lua 做"尾调用消除"——不创建新栈帧，类似 `goto`。

```lua
-- 是尾调用
return f(x)                    -- 只有 f(x)，无额外操作
return g(f(x), y)              -- g 是尾调用
return x[i].foo(x[j] + a*b, i+j)  -- foo 是尾调用

-- 不是尾调用
return g(x) + 1                -- 需要做加法
return x or g(x)               -- 需调整结果数
return (g(x))                  -- 括号强制单返回值
```

---

### 第7章  迭代器与泛型 for

#### 7.1 迭代器与闭包

```lua
function list_iter(t)
  local i = 0; local n = table.getn(t)
  return function()            -- 闭包保存 i 和 n
    i = i + 1
    if i <= n then return t[i] end
  end
end

for element in list_iter(t) do print(element) end
```

#### 7.2 泛型 for 语义

```lua
for var_1, ..., var_n in <explist> do <block> end

-- 等价于
do
  local _f, _s, _var = <explist>
  while true do
    local var_1, ..., var_n = _f(_s, _var)
    _var = var_1
    if _var == nil then break end
    <block>
  end
end
```

#### 7.3~7.4 无状态与多状态迭代器

```lua
-- 无状态迭代器（ipairs 的原理）
function iter(a, i)
  i = i + 1
  local v = a[i]
  if v then return i, v end
end
function ipairs(a) return iter, a, 0 end

-- pairs 即 next
function pairs(t) return next, t, nil end
```

---

### 第8章  编译·运行·错误处理

#### 8.1 loadfile / loadstring / dofile

| 函数 | 功能 |
|------|------|
| `loadfile("f.lua")` | 编译文件为函数，不执行，错误返 nil+err |
| `loadstring("return 2+2")` | 编译字符串为函数，不执行 |
| `dofile("f.lua")` | `loadfile` + 执行 |

```lua
function dofile(filename)
  local f = assert(loadfile(filename))
  return f()
end
```

#### 8.2 require

`require` 按路径搜索文件，加载一次后缓存在 `package.loaded` 中。

```lua
-- 路径示例：?;?.lua;/usr/local/lua/?/?.lua
-- require "lili" 依次尝试: lili, lili.lua, /usr/local/lua/lili/lili.lua
```

#### 8.3~8.4 错误与异常

```lua
-- assert：条件不满足则报错
n = assert(io.read("*number"), "invalid input")

-- pcall：保护模式调用，捕获所有异常
if pcall(foo) then
  -- 无错误
else
  -- 处理错误
end

-- xpcall：类似 pcall 但指定错误处理函数（可在栈释放前获取 traceback）
local ok, err = xpcall(f, debug.traceback)
```

---

### 第9章  协同程序（Coroutine） ——★★★ 重点

> 协同是**协作式多线程**：有独立栈、共享全局变量、显式切换。非抢占——由程序员而非调度器控制切换时机。

| 状态 | 说明 |
|------|------|
| `suspended` | 初始或 yield 后 |
| `running` | 正在执行 |
| `dead` | 执行完毕或异常终止 |

```lua
co = coroutine.create(function()
  for i = 1, 10 do
    print("co", i)
    coroutine.yield()
  end
end)
coroutine.resume(co)   --> co 1
coroutine.resume(co)   --> co 2

-- resume-yield 之间传递数据
co = coroutine.create(function(a, b)
  coroutine.yield(a + b, a - b)
end)
print(coroutine.resume(co, 20, 10))   --> true  30  10
```

**生产者-消费者模式**：

```lua
function receive()
  local status, value = coroutine.resume(producer)
  return value
end
function send(x) coroutine.yield(x) end
```

**协程作迭代器**（颠倒调用关系）：

```lua
function perm(a)
  local n = table.getn(a)
  return coroutine.wrap(function() permgen(a, n) end)
end
for p in perm{"a", "b", "c"} do print(p) end
```

---

### 第10章  完整示例

**Lua 作为数据描述语言**——数据文件即合法的 Lua 代码：

```lua
entry{
  title = "Tecgraf",
  org = "Computer Graphics Technology Group, PUC-Rio",
  url = "http://www.tecgraf.puc-rio.br/",
}

-- 处理数据
count = 0
function entry(o) count = count + 1 end
dofile("db.lua")
```

**马尔可夫链文本生成**——用 table 存储 n-gram 统计算法，是闭包、迭代器和数据结构的综合应用。

---

## 第二篇  表与对象

### 第11章  数据结构 ——★ 实践重点

> Lua 的 table 是实现**所有数据结构**的唯一工具。

| 数据结构 | 实现方式 |
|---------|---------|
| **数组** | `t = {10, 20, 30}` 用正整数索引 |
| **矩阵** | 数组的数组 `{ {1,2}, {3,4} }` 或单表 `t[i*M+j]` |
| **链表** | `{next = list, value = v}` |
| **双端队列** | 用 `first`/`last` 索引，O(1) 头尾操作 |
| **集合** | `{["while"] = true}` 键做元素，值设 `true` |
| **包（Bag）** | `{["apple"] = 3}` 值为计数器 |

> ★★★ **字符串缓冲**：循环内用 `s = s .. x` 是 O(n²)，应用 `table.concat(t, "")` 或分段合并。读取 350KB 文件：naive 方式 ~1 分钟，`table.concat` ~0.5s，`io.read("*all")` ~0.02s。

---

### 第12章  数据文件与持久化

**序列化**：将 Lua 值转为 Lua 代码文本，`dofile` 即可恢复。

```lua
-- 安全字符串序列化
string.format("%q", s)    -- "%q" 是 Lua 提供的安全格式化

-- 带循环引用检测（用 saved 表追踪已保存对象）
function save(name, value, saved)
  saved = saved or {}
  if type(value) == "table" then
    if saved[value] then
      io.write(saved[value], "\n")    -- 循环引用
    else
      saved[value] = name
      io.write("{}\n")
      for k, v in pairs(value) do
        save(name .. "[" .. basicSerialize(k) .. "]", v, saved)
      end
    end
  end
end
```

---

### 第13章  Metatables 与 Metamethods ——★★★ 核心

元表（metatable）改变表/对象的行为，是 Lua 对象系统的基石。

```lua
-- 核心方法
setmetatable(t, mt)     -- 设置
getmetatable(t)         -- 获取
rawget(t, k)            -- 绕过 __index
rawset(t, k, v)         -- 绕过 __newindex
```

| Metamethod | 触发场景 | 用途 |
|-----------|---------|------|
| `__add` `__sub` `__mul` `__div` | 算术运算 | 运算符重载 |
| `__eq` `__lt` `__le` | 关系运算 | 自定义比较 |
| `__concat` | `..` 连接 | 自定义连接 |
| `__index` | 读取不存在的 key | **继承、默认值的核心** |
| `__newindex` | 给不存在的 key 赋值 | 只读表、表代理 |
| `__call` | 把表当函数调用 | 函数对象 |
| `__tostring` | `print` / `tostring` | 自定义输出 |
| `__gc` | userdata 回收时 | 析构/资源释放 |

> ★★★ **`__index` 的核心角色**：当 `t[k]` 不存在时触发。值可以是**表**（简单继承）或**函数**（灵活控制）。实现 OOP 继承就用 `__index` 指向原型表。

```lua
-- 只读表（通过 __newindex）
function readOnly(t)
  local proxy = {}
  local mt = {__index = t, __newindex = function() error("read-only", 2) end}
  setmetatable(proxy, mt)
  return proxy
end

-- 默认值表（通过 __index 函数）
function setDefault(t, d)
  local mt = {__index = function() return d end}
  setmetatable(t, mt)
end

-- 表代理（监控所有访问）
local t = {}
local mt = {
  __index = function(_, k) print("*access " .. k); return _t[k] end,
  __newindex = function(_, k, v) print("*update " .. k); _t[k] = v end,
}
setmetatable(t, mt)
```

---

### 第14章  环境

全局变量存储在 `_G` 表中（`_G._G == _G`）。

```lua
value = _G[varname]         -- 动态名字访问
_G[varname] = value

-- 强制声明全局变量（设置 _G 的 __newindex）
function declare(name, initval)
  rawset(_G, name, initval or false)
end

-- setfenv(fn, env) 改变函数环境
setfenv(1, {g = _G})       -- 1 = 当前函数
local newgt = {}            -- 创建独立沙箱
setmetatable(newgt, {__index = _G})  -- 继承全局环境
setfenv(1, newgt)
```

---

### 第15章  Packages ——★★ 工程实践

Lua 没有显式的 package 机制，用 **table + local** 实现。

```lua
-- 基本包结构
complex = {}
function complex.new(r, i) return {r=r, i=i} end
function complex.add(c1, c2)
  return complex.new(c1.r+c2.r, c1.i+c2.i)
end

-- 私有成员用 local
local function checkComplex(c)
  if not (type(c)=="table" and tonumber(c.r) and tonumber(c.i)) then
    error("bad complex number", 3)
  end
end

-- 用 setfenv 让成员自动成为表域
local P = {}; complex = P
setfenv(1, P)
function add(c1, c2) ... end     -- 自动成为 P.add
```

---

### 第16章  面向对象程序设计 ——★★★ 核心

> Lua 的 OOP 基于 table + metatable + `: ` 语法糖。

**冒号语法**：`obj:foo(x)` 等价于 `obj.foo(obj, x)`——隐式传递 `self`。

```lua
-- 类定义模板（核心模式）
Account = {balance = 0}

function Account:new(o)
  o = o or {}
  setmetatable(o, self)
  self.__index = self         -- ★ 关键：类本身即 __index
  return o
end

function Account:deposit(v)
  self.balance = self.balance + v
end

-- 继承
SpecialAccount = Account:new()
function SpecialAccount:withdraw(v)
  if v - self.balance >= self:getLimit() then
    error"insufficient funds"
  end
  self.balance = self.balance - v
end

s = SpecialAccount:new{limit = 1000}
s:deposit(100)
```

**多重继承**：`__index` 为函数，在多个父类列表中搜索：

```lua
function createClass(...)
  local c = {}
  setmetatable(c, {__index = function(t, k)
    return search(k, arg)    -- 在 ... 代表的父类列表中查找
  end})
  c.__index = c
  function c:new(o) ... end
  return c
end
```

**私有性**：用闭包封装，不暴露内部状态：

```lua
function newAccount(initialBalance)
  local self = {balance = initialBalance}
  local withdraw = function(v) self.balance = self.balance - v end
  local getBalance = function() return self.balance end
  return {withdraw = withdraw, getBalance = getBalance}
end
```

---

### 第17章  Weak 表

弱引用（weak reference）：不阻止垃圾回收的引用。

```lua
setmetatable(a, {__mode = "k"})   -- 弱键：键被回收入口即删除
setmetatable(b, {__mode = "v"})   -- 弱值：值被回收入口即删除
setmetatable(c, {__mode = "kv"})  -- 全弱

-- 记忆函数（带自动回收）
local results = {}
setmetatable(results, {__mode = "v"})  -- 结果可被回收
function mem_loadstring(s)
  local res = results[s]
  if res == nil then
    res = assert(loadstring(s))
    results[s] = res
  end
  return res
end
```

> 只有对象（table/function/userdata/thread）会从 weak table 中被收集；数字和字符串不会。

---

## 第三篇  标准库

### 第18章  数学库

```lua
math.pi, math.sin, math.cos, math.tan
math.floor, math.ceil
math.max, math.min
math.random(), math.random(n), math.random(a, b)    -- 三种调用方式
math.randomseed(os.time())
```

### 第19章  Table 库

```lua
table.getn(t)              -- 数组大小
table.insert(t, x)         -- 插入末尾（push）
table.remove(t)            -- 删除末尾（pop）
table.insert(t, 1, x)      -- 头部插入（队列入队）
x = table.remove(t, 1)     -- 头部删除（队列出队）
table.sort(t, comp)        -- 排序
table.concat(t, sep)       -- 连接为字符串
```

> `table.sort` 只能排数组，要按键排序需先提取 keys 为数组再排。

### 第20章  String 库 ——★★ 常用

```lua
string.len(s)              -- #s
string.sub(s, 1, 3)        -- 截取（支持负索引 s:-1 倒数第一个字符）
string.lower(s); string.upper(s)
string.format("%s=%d", k, v)
string.char(97)             --> 'a'
string.byte("hello", 2)     --> 101

-- 模式匹配函数
string.find(s, pattern)              -- 查找
string.gsub(s, pattern, repl)        -- 替换（返回新串+替换次数）
string.gmatch(s, pattern)             -- 迭代匹配

-- Lua 模式（非正则，约500行代码实现）
-- 字符类：%d数字 %a字母 %s空白 %w字母数字 %p标点 %c控制符
-- 修饰符：+ (1+) * (0+) - (0+最短) ? (0或1)
-- 捕获：(pattern) 提取子串
-- 定位：^行首 $行尾
```

```lua
-- 去除首尾空白
s = string.gsub(s, "^%s*(.-)%s*$", "%1")

-- 模板展开
s = string.gsub(s, "$(%w+)", function(n) return _G[n] end)

-- 算式求值
s = string.gsub(s, "$%[(.-)%]", function(x)
  return loadstring("return " .. x)()
end)

-- 日期格式转换
s = string.gsub(s, "(%d+)/(%d+)/(%d+)", "%3-%1-%2")
```

### 第21章  I/O 库

```lua
-- 简单模式
io.input(filename); t = io.read("*all")    -- 读全部
for line in io.lines() do ... end          -- 逐行

-- 完全模式
local f = assert(io.open(filename, "r"))
local t = f:read("*all")
f:close()

-- 性能：用 '*all' 最快，table.concat 次之，逐行 .. 拼接最慢
```

### 第22章  操作系统库

```lua
os.time()                              -- 时间戳
os.time{year=2000, month=1, day=1}
os.date("%Y-%m-%d %H:%M:%S", t)       -- 格式化
os.clock()                             -- CPU 耗时（基准测试）
os.execute("mkdir test")               -- 执行系统命令
os.rename(old, new); os.remove(file)
```

### 第23章  Debug 库

```lua
debug.getinfo(1, "Sl")                 -- 动函信息
debug.getlocal(1, i)                   -- 局部变量
debug.sethook(print, "l")              -- 每行打行号
debug.traceback()                      -- 获取调用栈
```

---

## 第四篇  C API ——★ 进阶

> Lua 与 C 之间的交互全通过**虚拟栈**（lua_State）。

### 第24章  C API 纵览

```c
lua_State *L = lua_open();
// 加载标准库
luaopen_base(L); luaopen_table(L); luaopen_io(L);
luaopen_string(L); luaopen_math(L);

// 执行 Lua 代码
if (luaL_loadbuffer(L, buff, len, "line") || lua_pcall(L, 0, 0, 0))
    fprintf(stderr, "%s", lua_tostring(L, -1));
lua_close(L);
```

| 栈操作 | 函数 | 说明 |
|-------|------|------|
| 压入 | `lua_pushnumber/string/boolean/nil` | 从 C 向栈压值 |
| 查询 | `lua_isnumber/isstring/...` `lua_type` | 检查栈中类型 |
| 取出 | `lua_tonumber/tostring/toboolean` | 获取值（类型不符返回 0/NULL） |
| 获取 | `lua_gettop` `lua_settop` | 栈高度管理 |
| 复制 | `lua_pushvalue` `lua_remove` `lua_insert` `lua_replace` | 栈内操作 |

### 第25章  扩展你的程序

```c
// 读取 Lua 变量
lua_getglobal(L, "width");
*width = (int)lua_tonumber(L, -1);

// 调用 Lua 函数
lua_getglobal(L, "f");
lua_pushnumber(L, x);           // 压参数
lua_pushnumber(L, y);
if (lua_pcall(L, 2, 1, 0) != 0) // 2 个参数，1 个返回值
    error(L, "%s", lua_tostring(L, -1));
z = lua_tonumber(L, -1);
lua_pop(L, 1);
```

### 第26章  调用 C 函数

```c
// C 函数原型
static int l_sin(lua_State *L) {
    double d = luaL_checknumber(L, 1);  // 获取第一个参数
    lua_pushnumber(L, sin(d));          // 返回结果
    return 1;                           // 返回值数量
}

// 注册到 Lua
lua_pushcfunction(L, l_sin);
lua_setglobal(L, "mysin");

// C 函数库注册
static const struct luaL_reg mylib[] = {
    {"dir", l_dir},
    {NULL, NULL}
};
int luaopen_mylib(lua_State *L) {
    luaL_openlib(L, "mylib", mylib, 0);
    return 1;
}
```

### 第27章  撰写 C 函数的技巧

```c
// 高效数组操作
lua_rawgeti(L, tbl, i);    // 避免 metamethod
lua_rawseti(L, tbl, i);

// 构建字符串
lua_concat(L, n);          // 连接栈顶 n 个值
lua_pushfstring(L, "%.2f", x);  // 类似 sprintf，自动管理内存

// Registry（C 代码专用隐藏表）
lua_pushlightuserdata(L, (void*)&Key);
lua_pushnumber(L, val);
lua_settable(L, LUA_REGISTRYINDEX);

// References（在 registry 中存储/取回值）
int r = luaL_ref(L, LUA_REGISTRYINDEX);    // 保存
lua_rawgeti(L, LUA_REGISTRYINDEX, r);      // 取回
luaL_unref(L, LUA_REGISTRYINDEX, r);       // 释放

// C 闭包：带回 upvalue
lua_pushcclosure(L, &counter, 1);           // 1 个 upvalue
```

### 第28章  C 中的自定义类型（Userdata）

```c
// 创建 userdata
NumArray *a = (NumArray *)lua_newuserdata(L, sizeof(NumArray));
luaL_getmetatable(L, "LuaBook.array");
lua_setmetatable(L, -2);

// 类型检查
void *ud = luaL_checkudata(L, 1, "LuaBook.array");

// __gc 析构函数
static int dir_gc(lua_State *L) {
    DIR *d = *(DIR **)lua_touserdata(L, 1);
    if (d) closedir(d);
    return 0;
}
```

### 第29章  资源管理

用 `__gc` metamethod 实现自动资源释放（仅对 userdata 有效）：

```c
// 目录迭代器：DIR* 存在 userdata 中，__gc 自动 closedir
// XML 解析器：XML_Parser 存在 userdata 中，__gc 自动释放
```

---

## 附录：核心速查

### 常见坑合集

| 坑 | 正确做法 |
|----|---------|
| 索引从 1 开始 | `t[1]` 是第一个元素 |
| `0` 和 `""` 为真 | 只有 `false` 和 `nil` 为假 |
| 不加 `local` 就是全局 | 养成 `local` 习惯 |
| `return` 必须在块末尾 | 用 `do return end` 绕过 |
| 循环字符串 `..` 拼接 | 用 `table.concat` |
| 数字比较用 `==` 与字符串比较不同 | `10 == "10"` 为 `false` |
| `loadstring` 总是全局环境 | 注意环境隔离 |
| \# 只计算连续整数索引 | 有洞的数组用 `table.maxn` |

### 语法速查

| 语法 | 示例 |
|------|------|
| 注释 | `-- 单行` 或 `--[[ 多行 ]]--` |
| 变量 | `local x = 10` |
| 函数 | `function f(a, b) return a + b end` |
| 表 | `t = {1, 2, key = "v"}` |
| 索f | `t[1]` 或 `t.key` |
| 条件 | `if a > b then ... elseif ... else ... end` |
| 遍历 | `for i=1,10` / `while` / `repeat until` / `for k,v in pairs(t)` |
| 异常 | `pcall(f)` `xpcall(f, handler)` `error(msg)` `assert(cond, msg)` |
| 协程 | `local co = coroutine.create(f)` `coroutine.resume(co)` `coroutine.yield()` |
| 元表 | `setmetatable(t, {__index = proto, __add = func})` |
