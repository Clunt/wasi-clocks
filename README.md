# WASI Clocks

[WebAssembly系统接口(WebAssembly System Interface)](https://github.com/WebAssembly/WASI)API提案。

### 当前阶段（Current Phase）

WASI-clocks目前处于[第3阶段(Phase 3)][Phase 3]。

[Phase 3]: https://github.com/WebAssembly/WASI/blob/main/Proposals.md#phase-3---implementation-phase-cg--wg

### 拥护者（Champions）

- Dan Gohman

### 可移植性标准（Portability Criteria）

WASI clocks必须有至少可以在Windows、macOS和Linux上通过测试套件(testsuite)的主机实现。

WASI clocks必须至少有两个完整独立的实现。

## 目录（Table of Contents）

- [介绍（Introduction）](#介绍introduction)
- [目标（Goals）](#目标goals)
- [非目标（Non-goals）](#非目标non-goals)
- [API详解（API walk-through）](#API详解api-walk-through)
  - [Use case 1](#use-case-1)
  - [Use case 2](#use-case-2)
- [详细设计讨论（Detailed design discussion）](#详细设计讨论detailed-design-discussion)
  - [[Tricky design choice 1]](#tricky-design-choice-1)
  - [[Tricky design choice 2]](#tricky-design-choice-2)
- [Considered alternatives](#considered-alternatives)
  - [[Alternative 1]](#alternative-1)
  - [[Alternative 2]](#alternative-2)
- [项目相关方利益 & 反馈（Stakeholder Interest & Feedback）](#项目相关方利益--反馈stakeholder-interest--feedback)
- [参考文献 & 致谢（References & acknowledgements）](#参考文献--致谢references--acknowledgements)
- [开发（Development）](#开发development)

### 介绍（Introduction）

WASI Clocks是一个用于读取当前时间和测量运行时间(elapsed time)的WASI API。

与许多时钟(clock)API不同，WASI Clocks是面向能力的。
WASI Clocks的API不会隐式引用时钟的函数，而是传递时钟句柄。

### 目标（Goals）

WASI Clocks的主要目标是允许用户使用WASI程序读取当前时间和测量运行时间。

### 非目标（Non-goals）

WASI Clocks的目的不是涵盖日期格式化，或修改时钟的时间。

### API详解（API walk-through）

#### 测量运行时间（Measuring elapsed time）

单调时钟(monotonic clock，逻辑时钟)可以用于测量代码区域的运行时间：

```wit
default-monotonic-clock: monotonic-clock
```

```rust
   let start: Instant = monotonic_clock::now(clock);

   // 一些代码

   let stop: Instant = monotonic_clock::now(clock);

   let elapsed: Instant = stop - start;
```


#### 显示当前人类时间（Telling the current human time）：

```rust
    let the_current_time = wall_clock::now();

    println!("从Unix时间至今已有 {} seconds 和 {} nanoseconds！", the_current_time.seconds, the_current_time.nanoseconds);
```

#### 获取时区（Retrieving the timezone）:

```rust
    let datetime: Datetime = wall_clock::now();

    let timezone_display: TimezoneDisplay = timezone::display(datetime);

    println!("当前时区为 {}", timezone_display.name);
```

### 详细设计讨论（Detailed design discussion）

### 时间戳的类型应该是什么？（What should the type of a timestamp be?）

在POSIX中，`clock_gettime`使用单个`timespec`类型表示所有时钟的时间戳，包含两个字段：秒(seconds)和纳秒(nanoseconds)。
然而，在应用程序中之需要测量运行时间，并且不需要关注挂钟时间(wall clock time，时钟时间/壁钟时间/墙上时钟时间)，
将秒(seconds)和纳秒(nanoseconds)作为单独的字短会增加代码的大小和复杂性。
对于这些用例，单个64-bit纳秒值，可以测量最多大约584年，是足够的和简单的。

对于挂钟时间，仍然有必要同时使用秒和纳秒，一方面可以表示遥远未来的日期，
另一方面，反映出代码通常希望以不同方式处理挂钟时间中秒和秒的小数部分这一事实。

因此，该API对不同类型的时钟使用不同的数据类型。

### 考虑替代方案（Considered alternatives）

#### Per-process和per-thread时钟（Per-process and per-thread clocks）

WASI preview1包含两个时钟，分别测量当前进程和当前线程的CPU时间。这些时钟很难在同一个主机进程中，具有多个wasm实例的WASI实现中有效实现，因此它们已从此API中删除。

Wasi-libc 支持通过使用单调时钟来模拟这些时钟，虽然这在技术上不是准确替代，但足以确保与现有代码的最小兼容性。

### 项目相关方利益 & 反馈（Stakeholder Interest & Feedback）

进入第3阶段之前的TODO。

Preview1具有单调和挂钟函数，它们在工具链中广泛使用。

### 参考文献 & 致谢（References & acknowledgements）

非常感谢以下人员提供的宝贵反馈和建议：

- [Person 1]
- [Person 2]
- [etc.]

### 开发（Development）

#### 生成imports.md（Generating imports.md）

文件`imports.md`使用[wit-bindgen](https://github.com/bytecodealliance/wit-bindgen)生成。

```bash
wit-bindgen markdown wit --html-in-md --features clocks-timezone
```
