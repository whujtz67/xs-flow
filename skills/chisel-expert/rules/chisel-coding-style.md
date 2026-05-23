# Chisel代码规范与建议

## 目录概要
**排版建议**
1. 强制 2 空格缩进
2. 行宽 120 字符硬性截断
3. 符号与关键字间距
4. 模块内部结构化排版Q

**命名与注释**
5. 强制内部成员私有化封装
6. 强制使用驼峰命名
7. 单次使用 Bundle 内联摊平与复用豁免
8. 硬件级函数封装原则
9. 添加必要的注释,以及注释风格
10. 代码设计时加入必要的Assertion

**硬件设计**
11. 强制数据信号门控化与粒度控制
12. 强制单向连线符约束
13. 强制同一 Sink 集中连线与显式优先级
14. 强制现代三段式状态机规范
15. 强制限制 DontCare 使用场景
16. 强制时序与组合逻辑分离原则

**其他**
17. 强制重度缩写注释规范
18. 强制 Bundle 逻辑内聚封装
19. 强制单信号条件赋值表达式化
20. 强制禁用非标准中缀表达式
21. 严格区分 map 与 foreach 的语义
22. 复杂链式操作强制换行

**验证覆盖率**
23. 缺乏otherwise条件导致生成死代码

---

## 排版建议

### 1. 强制 2 空格缩进 (Strict 2-Space Indentation)
**Rule**: 强制使用 2 个空格进行代码层级缩进。绝对禁止使用 4 个空格或 Tab 键。所有换行对齐必须严格遵守 2 空格缩进原则。

```scala
// BAD: 使用 4 空格缩进
when(cond) {
    out := 1 // <- 违规：使用了 4 个空格
}

// GOOD: 严格 2 空格缩进
when(cond) {
  out := 1 // <- 合规：必须保留 2 个空格
}

// BAD: 函数换行参数未对齐或使用 4 空格
io.out := Mux(
    cond, // <- 违规：换行后缩进了 4 个空格
    a,
    b
)

// GOOD: 函数换行参数使用 2 空格对齐
io.out := Mux(
  cond, // <- 合规：换行后缩进 2 个空格
  a,
  b
)
```

### 2. 行宽 120 字符硬性截断 (120-Character Line Limit)
**Rule**: 每行代码的字符总数绝对禁止超过 120 个字符。当单行逻辑过长时，必须在合适的逻辑节点（如操作符、逗号后）手动换行，并配合 2 空格原则进行下一行缩进。

```scala
// BAD: 单行代码超长（假设此行超过 120 字符）
io.resp := Mux(io.req.denied || io.req.corrupt || io.req.timeout || io.req.invalid, ERR_CODE, OK_CODE) // <- 违规：未遵守 120 字符上限截断

// GOOD: 适时换行截断并配合 2 空格缩进
io.resp := Mux(
  io.req.denied || io.req.corrupt || io.req.timeout || io.req.invalid, // <- 合规：在逻辑节点处换行截断
  ERR_CODE,
  OK_CODE
)
```

### 3. 符号与关键字间距 (Whitespace Formatting)
**Rule**: 必须在关键字（如 `case`）、操作符（如 `=>`）、标点符号（逗号 `,` 之后）、以及大括号 `{ }` 的内侧保留一个空格，禁止代码粘连。

```scala
// BAD: 符号粘连 (No space)
seq.map{case(x,i)=>f(i)}

// GOOD: 规范间距 (Proper spacing)
seq.map { case (x, i) => f (i) }
```

### 4. 模块内部结构化排版 (Strict Module Layout with Banner Comments)
**Rule**: 按照功能区块从上到下的顺序组织 Module 内部代码，严禁将参数、模块例化、变量声明、组合逻辑与时序更新逻辑混杂。强制使用标准化的单行长分割线（如 `// ------ [Section] ------ //`）作为区块 Header。

```scala
// BAD: 结构混乱，例化、声明、组合逻辑与时序更新逻辑交叉混杂
class SomeModule(someParam: ParamType) extends Module {
  val io = IO(new SomeIOBundle(someParam))
  private val datawidth = someParam.datawidth // <- 违规：参数计算应置于最顶部
  val count_r = RegInit(0.U(datawidth.W))
  val count_nxt = Wire(UInt(datawidth.W))
  val moduleA = Module(new ModuleA)           // <- 违规：模块例化未单独成块，穿插在声明后
  count_nxt := count_r + 1.U                // <- 违规：组合逻辑赋值与声明混杂
  count_r := count_nxt                      // <- 违规：寄存器次态更新逻辑未统一靠后放置
  moduleA.io.in := io.in
}

// GOOD: 严格遵守 Header 分割顺序，各类型逻辑严格隔离
class SomeModule(someParam: ParamType) extends Module {
  // ------------------------------------------ Parameters ---------------------------------------------- //
  /**
   * Alias of params
   * 为了coding方便以及降低参数名称耦合程度,在此处对传入参数做别名处理。对于需要计算得到的参数,也应在此处完成。
   */
  private val datawidth = someParam.datawidth

  // ------------------------------------------ IO Declaration ------------------------------------------ //
  val io = IO(new SomeIOBundle(someParam))

  // ------------------------------------------ SubModules ---------------------------------------------- //
  /**
   * 将所有的 module 放在一起定义, 可以极大地降低阅读代码的难度。此处不做任何 module 的连线。
   * 如果需要计算 module class 构造函数的参数,可以在module 定义前,按 module 顺序依次计算列出。
   */
  val moduleA = Module(new ModuleA) // <- 合规：例化单独分配独立区块，禁止在此连线

  // ------------------------------------------ Wire/Reg Declaration ------------------------------------ //
  /**
   * 此处定义Wire和Reg。
   * 当出现寄存器次态和现态组合时，现态声明为Reg或RegInit，次态声明为WireDefault(现态)，并且禁止在此处进行任何组合逻辑赋值。
   */
  val count_r   = RegInit(0.U(datawidth.W))   // <- 合规：现态声明为 Reg
  val count_nxt = WireDefault(count_r)        // <- 合规：次态声明为 Wire

  // ------------------------------------------ State Machine ------------------------------------------- //
  // (状态转移逻辑...)

  // ------------------------------------------ Combinational Logic ------------------------------------- //
  count_nxt := Mux(moduleA.io.out, count_r + 1.U, count_r) // <- 合规：组合逻辑计算次态

  // ------------------------------------------ Register Update ----------------------------------------- //
  /**
   * 对于次态和现态的组合，直接进行赋值。
   * 若是单独定义的寄存器，且需要编写寄存器更新逻辑，需要保证同一个寄存器的更新逻辑集中在同一个when-elsewhen-otherwise中, 
   * 避免when block的次序导致的硬件电路差异。
   */
  count_r := count_nxt // <- 合规：次态赋值给现态，严格限制在此时序区块

  // ------------------------------------------ SubModule IO Assignments -------------------------------- //
  moduleA.io.in := io.in

  // ------------------------------------------ Assertions ---------------------------------------------- //
  assert(...)

  // ------------------------------------------ Don't Touch --------------------------------------------- //
  dontTouch(...)
}
```

---

## 命名与注释
### 5. 强制内部成员私有化封装 (Strict Encapsulation with Private Modifiers)
**Rule**: 必须严格控制 `Module` 内部成员的访问权限，实施默认私有化策略。
1. **内部信号 (`val`)**：除对外暴露的接口 `val io = IO(...)` 外，模块内定义的所有其他线网 (Wire)、寄存器 (Reg)、参数或常量，**强制**添加 `private` 前缀。
2. **内部方法 (`def`)**：对于仅在模块内部调用的辅助计算函数或状态判断逻辑，**强制**添加 `private` 前缀。仅当明确需要被外部对象调用时才允许公开。

```scala
// BAD: 内部信号与辅助函数暴露为 public，破坏封装性
class MyMod extends Module {
  val io = IO(new Bundle { val in = Input(Bool()) }) 
  val temp = Wire(Bool())              // <- 违规：内部连线缺失 private
  val count = RegInit(0.U)             // <- 违规：内部寄存器缺失 private
  def nextVal(v: UInt) = v + 1.U         // <- 违规：仅供内部使用的函数缺失 private
}

// GOOD: 严格遵守最小权限原则，隔离内部实现细节
class MyMod extends Module {
  val io = IO(new Bundle { val in = Input(Bool()) }) // <- 合规：IO 接口必须公开
  
  private val temp  = Wire(Bool())     // <- 合规：内部连线强制 private
  private val count = RegInit(0.U)     // <- 合规：内部寄存器强制 private
  
  private def nextVal(v: UInt) = v + 1.U // <- 合规：内部辅助逻辑强制 private
  
  temp := nextVal(io.in)
}
```

### 6. 强制使用驼峰命名
* Modules and Bundles (Classes, Traits, and Objects) 首字母大写驼峰
* Values and Methods 首字母小写驼峰
* Chisel 生成 verilog后,原本的"."会使用下划线表示,如io_in_camelCase,试用驼峰命名便于阅读

```scala
trait UsefulScalaUtilities {
  def isEven(n: Int): Boolean = (n % 2) == 0
  def isOdd(n: Int): Boolean = !isEven(n)
}

class MyCustomBundle extends Bundle { }
object MyCustomBundle { }

val mySuperReg = Reg(init = 0.asUInt(32))
def myImportantMethod(a: UInt): Bool = a < 23.asUInt
```

为了整体代码可读性, Module内部特殊变量可酌情增加下划线标注：
* 流水线: `s0_xxx`, `s1_xxx`, `s2_xxx`
* 寄存器的现态和次态: `xxx_r`, `xxx_nxt`
* 寄存器复制: `xxx_dup`
* 状态机的状态名: `s_idle`, `s_busy`, `s_camelCaseState`，如果状态机较多或者状态较多,可以增加前缀区分不同状态机的状态名,例如 `a_s_idle`, `a_s_busy`, `b_s_idle`, `b_s_busy`

### 7. 单次使用 Bundle 内联摊平与复用豁免 (Inline Single-Use Bundles & Reuse Exemption)
**Rule**: 
1. **就地摊平**：对于全局仅实例化一次的内部信号（Wire/Reg）或接口（IO），绝对禁止为其声明独立的 `class xxx extends Bundle`。必须直接在例化时使用匿名类 `new Bundle { ... }` 就地摊平定义。
2. **【复用豁免】**：本规范高度强调代码复用性！如果某个 Bundle 结构在当前作用域或跨模块中可能被**多次实例化（$\ge 2$ 次）**，则本摊平规则自动失效，强制要求提取为独立的 `class` 以保证复用逻辑。

```scala
// BAD: 仅使用一次的内部连线，却定义了冗余的独立类
class TempData extends Bundle {     // <- 违规：仅实例化一次，禁止独立声明
  val a = UInt(8.W)
  val b = Bool()
}
class MyMod extends Module {
  val temp = Wire(new TempData)   // <- 违规：调用了冗余的单次使用类
}

// GOOD: 仅使用一次的非 IO 信号，直接就地摊平
class MyMod extends Module {
  val temp = Wire(new Bundle {    // <- 合规：单次使用，直接就地摊平匿名 Bundle
    val a = UInt(8.W)
    val b = Bool()
  })
}

// GOOD: 【复用豁免】被多次实例化的 Bundle，必须提取为独立类
class SharedData extends Bundle {   // <- 合规：触发复用豁免，必须独立声明
  val a = UInt(8.W)
  val b = Bool()
}
class MyMod extends Module {
  val w_data1 = Wire(new SharedData)
  val w_data2 = Wire(new SharedData) // <- 合规：多次使用，复用独立类
  val r_data3 = Reg(new SharedData)
}
```

### 8. 硬件级函数封装原则 (Hardware-Aware Function Encapsulation & Area Control)
**Rule**: 
1. **鼓励可读性封装**：允许将大块逻辑封装为函数（`def`）以提升代码可读性，即使仅使用一次。若需在 Verilog 中保留函数内部信号以供 debug，**强制使用** `dontTouch`。
2. **硬件实例化思维**：绝对禁止将“软件函数调用”等同于“硬件函数调用”。在 Chisel 中，每次调用 `def` 都会在底层平铺生成一份全新的物理电路。
3. **防范面积爆炸**：对于包含**复杂逻辑**（如乘法器、复杂状态转移等）且需要**多次调用**的代码块，**严禁**使用 `def` 进行复用。必须将其封装为独立的 `Module`，通过物理模块实例化（或结合分时复用设计）来严格控制电路面积。

```scala
// GOOD: 语义化封装特定的时序/状态条件，以提升代码可读性，一目了然
def isLast: Bool = io.in.fire && io.in.bits.last && (count_r === maxLen)
when(isLast) {                        // <- 合规：将复杂判断条件提炼为高可读性的 def
  r_state := s_done
}

// GOOD: 将具有一定复杂度的多行逻辑封装为 def（仅单次调用），净化主干代码
def calcStatus(a: UInt, b: UInt): UInt = {
  val res = WireDefault(0.U(4.W))
  when(a > b) { res := 1.U }
  .elsewhen(a === b) { res := 2.U }
  .otherwise { res := 4.U }
  dontTouch(res)                          // <- 合规：保留内部连线，方便 debug
  res
}
io.status := calcStatus(io.in1, io.in2)   // <- 合规：仅调用一次，提升整体可读性

// BAD: 缺乏硬件思维，多次调用包含复杂逻辑的 def，导致面积爆炸 (Area Explosion)
def heavyALU(a: UInt, b: UInt): UInt = a * b + (a / b) // <- 复杂硬件逻辑
val res1 = heavyALU(in1, in2)    // <- 违规：生成第 1 份庞大的组合逻辑电路
val res2 = heavyALU(in3, in4)    // <- 违规：完全复制第 2 份电路，面积翻倍

// GOOD: 将多次复用的复杂逻辑提取为 Module，建立明确的物理边界
class HeavyALU extends Module {  // <- 合规：封装为 Module，利于综合优化与复用设计
  val io = IO(new Bundle { val a = Input(UInt()); val b = Input(UInt()); val res = Output(UInt()) })
  io.res := io.a * io.b + (io.a / io.b)
}
val alu1 = Module(new HeavyALU)
alu1.io.a := in1; alu1.io.b := in2
val res1 = alu1.io.res
```

### 9. 添加必要的注释,以及注释风格

```scala
// 单行注释

/**
 * 多行注释
 * 多行注释中可以使用 @, 如 @param 标注参数说明
 * 多行注释中可以用 <> 高亮<func>
 * 多行注释中可以使用[[]] 使得括号中内容可以跳转 [[value]]
 */
```
**需要增加注释的地方:**
* 模块功能点的简要说明
* 存储体读写口,读写条件,时序的简要说明
* Conner case 的场景说明
* 时序和面积优化点的说明
* 部分使用固定数字的切片或者比特位需要说明

### 10. 代码设计时加入必要的Assertion
设计时有意识的增加一些assert语句,一方面便于定位问题;另一方面便于读懂逻辑中的一些属性,起到注释作用。对于在Debug时发现的问题,也可在代码中增加对应assert,以便下次出bug快速定位。

---

## 硬件设计

### 11. 强制数据信号门控化与粒度控制 (Strict Clock Gating & Granularity Control)
**Rule**: 
1. **强制门控**：对于非控制类的信号（特别是 Data 和 Bundle 数据流），**绝对禁止**默认使用无门控的 `RegNext`。**必须**使用 `RegEnable` 添加使能条件，以提升静态时钟门控覆盖率（CG Coverage）。
2. **时序豁免**：若因关键路径时序优化（Timing Optimization）必须放弃门控而退回使用 `RegNext`，**强制**在对应行上方添加明确的优化原因注释。
3. **聚合门控粒度**：**禁止**为大量细碎的小位宽信号（<16 bits）生成各自独立的门控。**必须**将具备相同使能条件的信号打包（如 Bundle），确保单个门控单元（ICG）驱动的寄存器宽度 $\ge$ 16 或 32 bits，以防止局部时钟门控过多导致功耗激增和 IR Drop（电压降）恶化。

```scala
// BAD: 数据信号裸奔（无门控）或细碎门控滥用
val r_data = RegNext(io.in_data)      // <- 违规：数据信号禁止使用 RegNext，缺失门控
val r_flag1 = RegEnable(io.f1, io.en) // <- 违规：细碎信号(1-bit)独立分配门控
val r_flag2 = RegEnable(io.f2, io.en) // <- 违规：大量细碎门控将导致严重的 IR Drop

// GOOD: 聚合细碎信号，共享大位宽门控
val w_flags = Wire(new Bundle { val f1 = Bool(); val f2 = Bool() /* Other signals controlled by the same enable */ })
w_flags.f1 := io.f1; 
w_flags.f2 := io.f2
val r_flags = RegEnable(w_flags, io.en) // <- 合规：聚合信号共享单一使能，优化物理实现

// GOOD: 标准数据寄存器门控化
val r_data = RegEnable(io.in_data, io.en) // <- 合规：数据流强制使用 RegEnable

// GOOD: 【时序豁免】因时序苛刻移除门控，强制带注释
// OPTIMIZATION: Timing critical path, clock gating removed to save logic depth
val r_fast_data = RegNext(io.in_fast)     // <- 合规：附带明确的时序优化声明
```

### 12. 强制单向连线符约束 (Strict Unidirectional Assignment)
**Rule**: 绝对禁止在单向信号或单向 Bundle 连接时使用双向操作符 `<>`。对于所有明确只有输入或输出方向的数据流，强制使用单向赋值操作符 `:=`，以防止潜在的连接方向推导错误。

```scala
// BAD: 对单向信号/Bundle 滥用双向连接符
io.out_data <> w_data // <- 违规：单向赋值禁止使用 <>

// GOOD: 严格使用单向连线符
io.out_data := w_data // <- 合规：明确方向的单向数据流必须使用 :=
```

### 13. 强制同一 Sink 集中连线与显式优先级 (Enforce Centralized Sink Assignment & Explicit Priority)
**Rule**: 绝对禁止对同一个目标信号（Sink）进行分散的、缺乏 `otherwise` 的多次独立 `when` 赋值。由于 Chisel 的覆盖机制，后写的 `when` 会隐式覆盖前面的赋值（越靠后优先级越高），极易引发隐藏 Bug。**强制**将同一目的端的所有赋值逻辑集中在同一个完整且连续的 `when-elsewhen-otherwise` 控制流中，并将**最高优先级条件置于最顶层**。

```scala
// BAD: 分散赋值引发隐式优先级（越靠后优先级越高，逻辑割裂）
val a = Wire(UInt(4.W))
a := 0                 // <- 违规：被当作隐式的 otherwise
when(condA) { a := 1 } // <- 违规：逻辑分散
when(condB) { a := 2 } // <- 违规：后置 when 隐藏了最高优先级，极难维护

// GOOD: 集中式赋值，显式声明优先级与默认状态
val a = Wire(UInt(4.W))
when(condB) {          // <- 合规：最高优先级明确前置
  a := 2
}.elsewhen(condA) {    // <- 合规：集中在同一控制流中
  a := 1
}.otherwise {          // <- 合规：显式声明默认 fallback 状态
  a := 0
}
```

### 14. 强制现代三段式状态机规范 (Strict Modern 3-Segment FSM Architecture)
**Rule**: 必须严格执行改良版的三段式状态机结构，彻底解耦状态切换与输出逻辑，并强制遵守以下现代 Chisel 范式：
1. **强制命名与初始化**：现态必须命名为 `state_r`，次态必须命名为 `state_nxt`。必须使用更现代的 `WireDefault(state_r)` 来初始化次态。
2. **强制状态判定别名化**：绝对禁止在逻辑中直接写 `state_r === s_xxx`。必须为其声明私有布尔变量别名（如 `private val is_idle = state_r === s_idle`），在全模块中使用该别名进行判断。可配合 `dontTouch` 保留波形。
3. **强制后置更新逻辑**：状态寄存器的时序更新逻辑（`state_r := state_nxt` 及 flush 逻辑）必须放置在整个 FSM 代码块的最末尾，绝对禁止将其置于状态转移主体之上。

```scala
// BAD: 命名陈旧，判定条件硬编码，寄存器更新前置污染主体
val state = RegInit(s_idle)
val stateNext = WireInit(state)     // <- 违规：命名不规范，使用了 WireInit

when(flush) { state := s_idle }.otherwise { state := stateNext } // <- 违规：寄存器更新逻辑前置

switch(state) {
  is(s_idle) { when(in.fire) { stateNext := s_run } }
}
io.out.valid := state === s_run     // <- 违规：直接硬编码 state === s_run 进行判定

// GOOD: 严格遵守现代规范，别名判定，次态默认值，更新后置
// ------------------------------------------ Wire/Reg Declaration ---------------------------------------------- //
val state_r   = RegInit(s_idle)             // <- 合规：现态命名为 state_r
val state_nxt = WireDefault(state_r)        // <- 合规：次态命名为 state_nxt，使用 WireDefault

private val idle = state_r === s_idle       // <- 合规：强制别名化状态判定，
private val run  = state_r === s_run

// ------------------------------------------ State Machine ----------------------------------------- //
when (idle) {
	state_nxt := Mux(in.fire, s_run, s_idle) // <- 合规：状态转移逻辑集中，且最高优先级前置
}.elsewhen (run) {
	state_nxt := Mux(out.fire, s_idle, s_run)
}.otherwise {
  state_nxt := s_idle // <- 合规：显式声明默认状态，防止综合器优化掉未覆盖的状态
}

// ------------------------------------------ Combinational Logic ------------------------------------- //
io.in.ready  := idle                        // <- 合规：使用别名进行极简的输出赋值
io.out.valid := run

// ------------------------------------------ Register Update ------------------------------------------ //
/**
 *  若有flush或reset逻辑, 则加入相关逻辑，否则直接 state_r := state_nxt。
 *  注意，此处加入flush只是演示，不一定有相关逻辑。
 */
when(flush) {
  state_r := s_idle
}.otherwise {
  state_r := state_nxt                      // <- 合规：时序更新逻辑严格后置，不污染逻辑主体
}

// ------------------------------------------ Don't Touch ---------------------------------------------- //
dontTouch(idle)
dontTouch(run)
```

### 15. 强制限制 DontCare 使用场景 (Strict Limitation on DontCare Usage)
**Rule**: 绝对禁止在最终交付的生产代码中使用 `DontCare` 进行信号赋值。此举旨在防止已写好的合法连线逻辑被综合器意外优化或覆盖（"被 DontCare 掉"）。仅允许在开发/测试阶段，将其作为尚未完成逻辑的临时占位符，且**强制**在此类占位代码上方添加 `// TODO` 或 `// FIXME` 注释。

```scala
// BAD: 在正式代码中滥用 DontCare 兜底，极易覆盖前置逻辑
io.out_valid := DontCare // <- 违规：生产代码绝对禁止使用 DontCare

// GOOD: 生产代码必须给出明确的默认值或完整逻辑连线
io.out_valid := false.B  // <- 合规：赋予确定的安全默认值

// GOOD: 【开发阶段豁免】仅用于未完成逻辑的临时编译测试，强制带 TODO
// TODO: pending ALU module integration
io.out_valid := DontCare // <- 合规：作为临时测试占位，且配有明确的 TODO 标识
```

### 16. 强制时序与组合逻辑分离原则 (Strict Next/R State Separation)
**Rule**: 为了对齐业界顶尖的 RTL 编码规范（分离组合与时序逻辑），对于包含分支或状态转换的复杂寄存器，**强制**实施现态（Current State）与次态（Next State）分离设计：
1. **命名规范**：变量主体**强制**保持 `lowerCamelCase`，现态必须追加 `_r` 后缀，次态必须追加 `_nxt` 后缀。绝对禁止使用 `xxxNext` 或 `_q`/`_d` 等其他风格。
2. **初始化与防 Latch**：次态**必须**使用 `WireDefault(xxx_r)` 进行声明，通过绑定现态作为默认值来杜绝隐式 Latch 行为。
3. **计算与更新隔离**：在业务逻辑块中，**绝对禁止**直接对现态 `_r` 赋值，所有的组合逻辑运算只能作用于次态 `_nxt`。现态的更新（`xxx_r := xxx_nxt`）**必须**统一且唯一地放置在模块的最末尾。
4. **【极简逻辑豁免】**：对于无需复杂判断的极简逻辑（如纯粹的打拍延迟、单行累加器），允许跳过分离原则，直接赋值。

```scala
// BAD: 隐式更新现态、命名不规范、时序与组合逻辑严重混杂
val dataIter = RegInit(0.U(8.W))
val dataIterNext = WireInit(dataIter)     // <- 违规：后缀使用了 Next 而非 _nxt，且未使用 WireDefault
when(io.valid) {
  dataIter := dataIter + 1.U              // <- 违规：在业务逻辑块中直接修改了现态（寄存器）
}

// GOOD: 严格遵守 Next/R 分离，规范后缀，集中进行时序更新
// ------------------------------------------ Wire/Reg Declaration ------------------------------------------ //
val dataIter_r = RegInit(0.U(8.W))        // <- 合规：现态严格追加 _r
val dataIter_nxt = WireDefault(dataIter_r)// <- 合规：次态追加 _nxt，并强制使用 WireDefault 赋初值

// ------------------------------------------ Combinational Logic ----------------------------------------- //
when(io.valid) {
  dataIter_nxt := dataIter_r + 1.U        // <- 合规：业务块中仅通过读取 _r 来计算并赋值给 _nxt
}

// ------------------------------------------ Register Update --------------------------------------------- //
dataIter_r := dataIter_nxt                // <- 合规：时序逻辑（打环）纯粹且唯一地放置在模块末尾

// GOOD: 【极简逻辑豁免】
val valid_r = RegNext(io.valid)           // <- 合规：极简的单行打拍逻辑无需分离
```

---

## 其他

### 17. 强制重度缩写注释规范 (Strict Annotation for Aggressive Abbreviations)
**Rule**: 鼓励代码命名紧凑化，允许对冗长的信号或变量名进行重度缩写。但是，如果使用了非业界公认的、或是非常激进的局部缩写（例如将 `AlphaBlendingUnit` 缩写为 `abu`），**强制**在变量定义的同一行末尾添加注释，明确写出其完整的英文全名。常见标准缩写（如 `pc`, `addr`, `clk`）可豁免。

```scala
// BAD: 子模块例化时仍然使用冗长的全名
val alphaBlendingUnit = Module(new AlphaBlendingUnit) // <- 违规：冗长的命名未缩写

// GOOD: 适度缩写，提升代码简洁性
val abu = Module(new AlphaBlendingUnit) // <- 合规：适度缩写

// BAD: 使用了不常见的重度缩写，且未提供全拼说明
// 违规：缩写过于晦涩，令队友或 AI 难以推导语义
val uValid = RegInit(0.U) 
val dValid = RegInit(0.U)

// GOOD: 激进缩写 + 完整的行内释义
val uValid = RegInit(0.U) // u 代表 upstream valid   <- 合规：提供全称注释
val dValid = RegInit(0.U) // d 代表 downstream valid <- 合规：提供全称注释
```

### 18. 强制 Bundle 逻辑内聚封装 (Strict Bundle Encapsulation & Cohesion)
**Rule**: 绝对禁止在 `Module` 的主体逻辑中，散落大量仅针对某个 `Bundle` 内部字段的特定解析、解码或连线逻辑。**强制**实现“高内聚、低耦合”，将针对该 Bundle 的条件判断方法（`def isXxx`）或特定的端口映射逻辑（自定义 `def :=`）直接封装在 `Bundle` 类内部，确保顶层模块的连线调用保持极简清爽。

```scala
// BAD: 针对 Bundle 内部字段的散碎逻辑堆积在 Module 主体中
class CmdBundle extends Bundle {
  val valid = Bool()
  val op = UInt(4.W)
}
class MyMod extends Module {
  val io = IO(new Bundle { val cmd = Input(new CmdBundle) })
  // <- 违规：解码逻辑强耦合在模块顶层
  val is_idle = io.cmd.valid && io.cmd.op === 0.U 
  val is_write = io.cmd.valid && io.cmd.op === 1.U
}

// GOOD: 将状态解码逻辑高内聚封装于 Bundle 内部
class CmdBundle extends Bundle {
  val valid = Bool()
  val op = UInt(4.W)
  
  def isIdle: Bool = valid && op === 0.U // <- 合规：自身字段的解析方法内聚在内部
  def isWrite: Bool = valid && op === 1.U
}
class MyMod extends Module {
  val io = IO(new Bundle { val cmd = Input(new CmdBundle) })
  val is_idle = io.cmd.isIdle            // <- 合规：顶层直接调用，逻辑清爽
}

// GOOD: 在 Bundle 中重载 := 封装特定的连线映射逻辑，避免顶层暴露切片细节
class CvtBundle extends Bundle {
  val addr_sfx = UInt(12.W)
  val id = UInt(4.W)

  def := (in: InFlit): Unit = {          // <- 合规：封装专属的赋值映射逻辑
    this.addr_sfx := in.addr(11, 0)
    this.id := in.id
  }
}
```

### 19. 强制单信号条件赋值表达式化 (Strict Single-Signal Expression Assignment)
**Rule**: 针对单一目标信号的条件赋值，**绝对禁止**使用冗长的 `when/elsewhen/otherwise` 控制流块。**强制**使用具有返回值的表达式算子 `Mux`（针对二选一）或 `PriorityMux`（针对多路优先级选择），以确保单一赋值点（Single Point of Assignment），彻底消除左值（LHS）的重复书写，并生成极其紧凑的 Verilog 嵌套三目运算符。

```scala
// BAD: 针对单一信号使用冗长控制流，导致目标变量 (w_out) 多次重复书写
val w_out = Wire(UInt(32.W))
when(c1) { 
  w_out := a               // <- 违规：左值冗余，逻辑分散，增加阅读负担
}.elsewhen(c2) { 
  w_out := b 
}.otherwise { 
  w_out := c 
}

// GOOD: 强制使用 PriorityMux，实现数据与条件成对的高内聚单点赋值
val w_out = PriorityMux(Seq( // <- 合规：右侧作为表达式整体，自动映射为 Verilog 嵌套三目
  c1     -> a,
  c2     -> b,
  true.B -> c                // <- 合规：通过 true.B 显式声明默认 fallback
))

// GOOD: 简单的二选一强制使用 Mux
val w_res = Mux(cond, x, y)  // <- 合规：一行搞定，左值唯一，意图极度清晰
```

### 20. 强制禁用非标准中缀表达式 (Disable Non-Standard Infix Expressions)
**Rule**: 绝对禁止使用中缀表达式风格调用常规方法（即省略点号 `.` 和括号 `()` 的写法）。除了基础运算符（`+`, `-`, `*` 等）和范围操作符（`to`, `until`, `by`）之外，所有集合操作和对象方法**强制**使用标准的点号调用格式。

```scala
// BAD: 滥用中缀表达式调用常规集合方法
val res = seqA zip seqB map { case (a, b) => a + b } // <- 违规：zip 和 map 禁止作为中缀使用

// GOOD: 强制使用标准的点号和括号调用
val res = seqA.zip(seqB).map { case (a, b) => a + b } // <- 合规：标准方法调用
for (i <- 0 until 4) {}                               // <- 合规：until/to/by 豁免，允许中缀
```

### 21. 严格区分 map 与 foreach 的语义 (Strict Semantic Separation of map and foreach)
**Rule**: 必须根据是否需要“返回值”严格区分 `map` 和 `foreach` 的使用场景。

### 22. 复杂链式操作强制换行 (Mandatory Line Breaks for Chained Sequence Operations)
**Rule**: 当对序列进行两次及以上的复杂链式调用（如 `.take().zip().foreach()` 的组合）时，**强制**在每个链式调用符（`.`）之前进行换行，并保持同一层级的缩进。这旨在强制暴露每一步的返回类型，极大增强复杂流式处理的 IDE 可读性。单一或极短的调用允许不换行。

```scala
// BAD: 复杂的链式序列操作挤在同一行，导致中间类型难以推导和阅读
seqA.take(2).zip(seqB.take(2)).foreach(x => x._1 := x._2) // <- 违规：复杂链式调用堆叠在单行

// GOOD: 复杂链式调用必须在点号前逐层换行对齐
seqA
  .take(2)                     // <- 合规：操作符前换行缩进，IDE 易于提示该行返回 Seq[UInt]
  .zip(seqB.take(2))           // <- 合规：清晰展示组合结构
  .foreach(x => x._1 := x._2)  // <- 合规：末端终结操作
```

---

## 验证覆盖率

### 23. 缺乏otherwise条件导致生成死代码

```scala
// 易导致死代码的写法
when (state === s_1 && io.goS2_0) {
  when (io.goS2_1) {
    when (io.goS2_2) {
      state := s_2
    }
  }.otherwise {
    state := s_3
  }
}
```
上述代码缺乏 `when (io.goS2_2)` 的 otherwise 分支，chisel 自动补全时会导致生成冲突逻辑（生成的Verilog中会产生永远无法达成的条件），对验证覆盖率的收集造成困扰,并且无意义的代码浪费硬件资源。

**修改建议1: 手动补全otherwise**
```scala
when (io.goS2_2) {
  state := s_2
}.otherwise{
  state := s_1
}
```

**修改建议2: 使用Mux而不是when**
```scala
when (state === s_1 && io.goS2_0) {
  when(io.goS2_1) {
    state := Mux(io.goS2_2, s_2, s_1)
  }.otherwise {
    state := s_3
  }
}
```