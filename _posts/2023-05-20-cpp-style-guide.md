---
layout: post
title: C++ 代码风格指南
categories: [视觉, 电控, C++, 没人用, 冷门]
author: Julyfun
---

![](/assets/2023-05-20-cpp-style-guide/example.jpg)

[本仓库](https://github.com/SJTU-RoboMaster-Team/style-team)代码规范的参考主要是 [Chromium C++ style guide](http://chromium.googlesource.com/chromium/src/+/HEAD/styleguide/c++/c++.md)，[Google 开源项目风格指南](https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/)和 [Rust style guide](https://github.com/rust-lang/style-team)。

如何自动化地应用这些规范？

* 将本仓库的 `.clang-format` 和 `.clang-tidy` 文件拷贝至你的项目根目录下。
* 在编辑器中开启 `Clang-Format` 和 `Clang-Tidy` 插件。插件通常默认使用工作区根目录下的配置文件。
* 在 C++ 代码文件中执行编辑器的格式化文档操作。你也可以开启保存时自动格式化的功能。

## 设计原则

* 可读性 🐰
  - 阅读速度
  - 防止误导
  - 可访问性 - 适用于不同硬件环境下，包括非可视化环境
  - 在编译器报错信息中的可读性

* 美学 🏛
  - sense of beauty
  - 与现代编程语言保持一致

* 细节 🖋
  - 易于进行版本维护
  - 尽可能兼容未来代码
  - 增加代码密集度，防止右飘

* 应用 👶🏻
  - 规则易于手动实践（在最简编辑环境中）
  - 规则易于自动实践（当可以使用 Clang-Format 等其他工具时）
  - 规则的一致性
  - 保持风格规则的简并性

---

# C++ 代码风格指南

## 动机 - 为什么使用格式化工具？

格式化代码其实是一个机械性的任务，但是人工实现又非常耗精力。格式化工具可以一键实现这个目的，解放程序员的生产力。

此外，如果坚持既定的风格指南（就比如这个），程序员们就不需要花时间讨论风格，从而节省精力。

人类往往以模式匹配的方式来理解代码。所以如果一份 C++ 代码拥有统一的风格，理解一个新项目的代码就只需要更少的脑力，降低新开发者的进入门槛。

由此观之，使用格式化工具（例如 Clang-Format）能提高生产力。如果团队坚持使用一种风格，就会有更大好处，想要达成此目标并不难，只要使用团队 `.clang-format, .clang-tidy` 的默认设置就可以了。

## 格式化约定

### 缩进和行宽

* 使用空格，而不是 Tab。
* 每级缩进使用 4 个空格（也就是说在纯字符串和注释之外的缩进都是 4 的倍数）。
* 行宽最大为 100。

### 空行

不同的语句之间要么不空行，要么空一行。例如，

```cpp
void foo() {
    int x = ...;

    int y = ...;
    int z = ...;
}

void bar() {}
void baz() {}
```

### 注释

以下关于注释的规范只是建议。格式化工具可以跳过对注释的格式化。

行注释（`//`）相较于块注释（`/* ... */`）更好。

当使用行注释时，开头标记后应该有一个空格。

使用行内块注释时，开符号后和闭符号前均有一个空格。多行块注释，开符号后和闭符号前均有一个新行。

行尾注释比其他注释更好。行尾注释之前带有 1 个空格。应该把块注释当作关键字一样处理其周围的空格。尾随注释和多行注释任意一行的末尾不应有尾随空格。

```cpp
// 条目上方的注释
struct Foo { ... };

void foo() {} // 条目尾随注释

namespace {
void foo(/* 参数前的注释 */ T x) {...}
}
```

注释应是完整的句子，行内块注释则不用。

纯注释行的注释宽度不大于 80，且算上缩进总宽度不超过 100。

```cpp
// This comment goes up to the ................................. 80 char margin.

{
    // This comment is .............................................. 80 chars wide.
}

{
    {
        {
            {
                {
                    {
                        // This comment is limited by the ......................... 100 char margin.
                    }
                }
            }
        }
    }
}
```

#### 文档注释

行注释（`///`）比块注释（`/** ... */`）更好。

多写文档注释（`///` 或 `/** ... */`），而非实现注释（`//!` 和 `/*! ... */`），实现注释多用于编写模块级文档。

文档注释应该写在属性之前。

### 属性

每个属性独占一行，与其修饰的条目保持相同缩进等级。

尽可能使用外部属性。

有参数的属性按照函数格式化。

```cpp
[[deprecated("Use NewCRepr instead.")]]
struct CRepr {
    float x;
    float y;

    [[nodiscard]]
    float func() {}
};
```

### **小的**条目

在本指南中，对于小条目，我们会采用不同的格式化方式。例如，对于结构体的列表初始化：

```cpp
// 常规格式化
Foo {
    an_expression,
    another_expression(),
}

// **小项目** 格式化
Foo { 1, 2 };
```

我们把**小**的界定权留给格式化工具。格式化工具可以在不同的环境中使用不同的定义。

有一些因素是不错的参考，比如条目的名字长度和复杂性（子属性无子表达式）。

格式化工具应允许用户忽略这些因素，从而总是采用常规格式化。

---

## 条目

`#include` 语句必须放在文件的最前面。包含头文件的次序如下：

1. 源文件对应的头文件
2. C 系统头文件
3. C++ 系统头文件
4. 第三方库的头文件
5. 本项目内的头文件

同一类别的头文件按照字典序排列。

`using` （指 `using a::b;` 和 `using namespace a;`，不是类型别名 `using a = b;`）应必须放在其作用域中其他语句的前面。同一类别的声明按照字典序排列。

格式化工具应该让以上排序方法是可选的。

### 函数定义

在函数签名内部避免加注释。

如果函数的签名不能放在一行内，就在左小括号后和右小括号前换行，而且每个参数独占一行并使用块缩进。例如：

```cpp
int foo(
    int arg1,
    int arg2
) {
    ...
}
```

### 枚举

在声明中，每个枚举成员独占一行，并使用块缩进。

```cpp
enum class FooBar {
    FIRST,
    SECOND,
    ERROR,
};
```

### 结构体和联合体

结构体的名字在同一行尾随 `struct` 关键字，当左大括号能被放在右边距内时，左小括号也在同一行。所有结构体字段缩进一次。右大括号不缩进，且独占一行。

```cpp
struct Foo {
    A a;
    B b;
};
```

当且仅当变量名不能放在右边距内时，它才被放在下一行，并再次缩进。

```rust
struct Foo {
    A a;
    LongType
        long_name;
};
```

对于联合体也使用同样的规范。

```cpp
union Foo {
    A a;
    B b;
    LongType
        long_name;
};
```

### 类

类中的条目使用块缩进。如果没有条目，则类可以被格式化为单行。否则在左大括号后和右大括号前应换行。访问修饰符不缩进。当访问修饰符开始一个新的逻辑块时添加空行。

```cpp
struct Foo {};

struct Bar {
public:
    A a;
    B b;

private:
    C c;
};
```

如果类型有父类，则在冒号后和每个逗号后有一个空格，例如，

```cpp
struct Foo: public Debug, public Bar {};
```

在父类列表中最好不要换行。如果要换行，每个父类独占一行，左大括号也独占一行。

```cpp
struct IndexRanges:
    public Index<Range<size_t>, Output>,
    public Index<RangeTo<size_t>, Output>,
    public Index<RangeFrom<size_t>, Output>,
    public Index<RangeFull, Output>
{
    ...
};
```

### #include

`#include <foo>`

`include` 后有一个空格。井号后、尖括号与头文件之间没有空格。

### 命名空间

```cpp
namespace foo {
}
```

```cpp
namespace bar = foo;
```

在关键字、左大括号前和 `=` 两边使用空格。分号两边没有空格。

### #define

当宏的完整定义包含多条语句时，用 `do { ... } while (0)` 包含语句块。反斜杠续航符与语句之间有一个空格。

```cpp
#define FOO(x) \
    do { \
        static_assert( \
            a_long_expression, \
            "..." \
        ); \
        A_MACRO_CALL(); \
    } while (0)
```

### 泛型

泛型的模板参数部分与定义部分之间应换行。最好把模板参数部分放在同一行。

在尖括号两边不要加空格。每个逗号后面应有一个空格。

```cpp
template<typename T, typename U>
void foo(const std::vector<T>& x, const std::vector<U>& y) ...

template<typename T, typename U>
struct SomeType { ...
```

如果模板参数部分必须被格式化为多行，则每个参数应该独占一行且缩进一次，左尖括号后应换行。

```cpp
template<
    typename T,
    typename U>
void foo(const std::vector<T>& x, const std::vector<U>& y) ...
```

对于泛型参数，最好使用一个字母命名。

### 类型别名

类型别名通常应该写在一行内。如果有必要换行，则在 `=` 后换行。`=` 右边的名称应使用块缩进。

```cpp
using Foo = Bar<T>;

// 如果需要拆成多行
using VeryLongType<T, U> =
    AnEvenLongerType<T, U, Foo<T>>;
```

### extern "C" 条目

当编写 extern "C" 条目时，总是使用显式 ABI。比如，使用 `extern "C" void func` 而不是 `extern "C" { ... }`。

### `using` 声明

最好把 `using` 声明格式化为单行。

```cpp
using a::b::c;
using namespace a::b::d;
```

不要在头文件中使用 `using namespace`。

---

### 定义和初始化

尽可能使用 `=` 初始化，当它不可使用或者造成类型名冗余时，使用“统一初始化方法” `Type pattern { expr };`。

```cpp
// 使用
std::vector<int> numbers = { 1, 2, 3 };
auto numbers = std::vector<int>(100, 2);
Foo bar { 0.01, 10 };
// 不使用
Foo bar = Foo(0.01, 10);
Foo bar(0.01, 10);
```

尽量在一行内定义完成。如果做不到，可以拆成两行。此时表达式应使用块缩进。

```cpp
Type pattern =
    expr;
```

### 语句中的宏

语句中调用的宏应该在尾部加上分号。小括号两边都不应有空格。

```cpp
// 一个注释。
A_MACRO(...);
```

### 语句中的表达式

表达式和分号之间不应有空格。

```cpp
<expr>;
```

不应给 `return` 语句后的表达式加上括号。

---

## 表达式
### 语句块

语句块在左大括号 `{` 之后和右大括号 `}` 之前都应有换行符。块前的任何限定符都应与 `{` 在同一行，且间隔一个空格。语句块中的内容应使用块缩进。

```cpp
void block_as_stmt() {
    a_call();

    {
        a_call_inside_a_block();

        // 语句块中的一条注释
        return the_value;
    }
}

void block_as_expr() {
    auto foo = [&]() {
        a_call_inside_a_block();

        // 语句块中的一条注释
        return the_value;
    }();
}
```

在语句块中的预处理指令的缩进应和其他表达式一致

```cpp
void block_as_stmt() {
    #ifdef a_definition
    {
        #define another_definition

        // 语句块中的一条注释
        #undef another_definition
    }
    #endif
}
```

不要在大括号的同一行中写注释。

空块的写法是 `{}`。

块在同时满足以下条件的情况下可以只占一行：

* 块以 lambda 表达式而非语句的形式出现
* 其中包含单行语句
* 其中不包含注释

单行语句块在左大括号前喝右大括号后都应有一个空格。

例如，

```cpp
void func() {
    // 单行
    int cnt0 = []() { return 2; }();

    // 以语句形式出现时
    {
        a_call();
    }

    // 包含注释
    int cnt1 = [&]() {
        // 注释
        return 2;
    }();

    // 包含多行语句
    int cnt2 = [&]() {
        if (cnt0 != 2) {
            return 3;
        } else {
            return 4;
        }
    }();
    int cnt3 = [&](){
        return a_call(
            an_argument,
            another_arg
        );
    }();
}
```

### 列表初始化

如果初始化列表是**小**的，则它可以放在一行。否则，初始化成员应该有自己的块缩进。多行初始化列表的最后一个参数应有尾随逗号。

左大括号 `{` 之前应有一个空格。在单行初始化列表中，左大括号后和右大括号前需有一个空格。

```cpp
Foo f1 = Foo { 1, 2, 3 };
Foo f2 = Foo {
    field1,
    an_expr,
};
```

### 数组访问

中括号两边不加空格，尽量避免换行。在目标表达式和左中括号之间不要空行。如果下标表达式有多行，应使用块缩进，而且左中括号之后和右中括号之前应换行。不过要避免多行的下标表达式。

例如，

```cpp
void func() {
    foo[10];
    foo[4 + 5 / bar];
    a_long_target[
        a_long_indexing_expression
    ];
}
```

### 一元运算符

一元运算符和操作数之间不应有空格（即 `!x` 而非 `! x`），避免一元运算符和操作数之间换行。

### 二元运算符

二元运算符和操作数之间应有空格。（即 `x + 1` 而非 `x+1`）（包括 `=` 和其他赋值运算符，例如 `+=` 或 `*=`）。

大方使用括号，不要因为运算符优先级就省略括号（比如，使用 `(a && b) || (c && d)` 而非 `a && b || c && d`，虽然他们表达的意思一致）。格式化工具不可添加或移除括号。不要使用括号来表明优先级。

如果表达式有多行，把运算符放在后一行，并且使用块缩进。各个子表达式独占一行。例如，

```cpp
foo_bar
    + bar
    + baz
    + qux
    + whatever
```

在赋值运算符（`=` 或 `+=` 等）处换行比在其他运算符处换行更好。

### 控制流

如果能增强可读性，可以在数学表达式和逻辑表达式中加上额外的括号（`(x * 15) + (y * 20)` 挺好的）。

### 函数调用

不要在函数名和左小括号之间加空格。

不要在参数和其尾随的逗号之间加空格。

在参数前的逗号和参数之间加空格。

调用最好写成单行的。

#### 单行调用

函数名与左括号，左括号和首个参数，最后一个参数和右小括号之间不要有空格。

```cpp
foo(x, y, z)
```

#### 多行调用

若函数不是**小**的，或者它会超出行宽限制，或者任何参数或参数的调用是多行的，则该调用应格式化为多行。该情况下，每个参数独占一行并使用块缩进。左小括号后、右小括号前应换行。例如，

```cpp
a_function_call(
    arg1,
    a_nested_call(a, b)
)
```

### 方法调用

和函数调用保持一致。

在 `.` 两边不要有空格。

```cpp
x.foo().bar().baz(x, y, z);
```

### 宏调用

如果宏可以像其他结构一样解析，则像其他结构一样格式化。例如 `FOO(a, b, c)` 可以被解析为一个函数调用（除了命名风格不同），所以它按照函数调用一样格式化。

### 域和方法调用链

调用链由域访问（`::`）和方法调用（`. 或 ->`）构成。

尽量写在单行内。如果需要写成多行，则所有元素应该独占一行，且以 `.` 作为新行的开头。每行都应使用块缩进。例如，

```cpp
int foo = bar
    .baz
    .qux();
```

如果一个调用链的第一个元素的最后一行加上其缩进小于下一行的缩进，则只要有足够的空间，就应合并这两行。例如，

```cpp
// 使用
x.baz
    .qux()
// 不使用
x
    .baz
    .qux()

// 使用
int foo = x
    .baz
    .qux();

foo(
    expr1,
    expr2
).baz
    .qux();
```

#### 多行元素

若一个调用链中的某个元素被格式化为多行，则该元素和其后的元素应独占一行。其前的元素可以写在同一行。例如，

```cpp
a.b.c().d
    .foo(
        an_expr,
        another_expr,
    )
    .bar
    .baz
```

注意在上述例子中，调用链和函数调用都造成了缩进。

将整个调用格式化为多行且每个元素独占一行，要优于把部分元素放在同一行而其他元素格式化为多行。例如，

```cpp
// 好的
this->pre_comment
    .as_ref()
    .map_or(false, [&](auto comment) { return comment.starts_with("//"); })

// 坏的
this->pre_comment.as_ref().map_or(
    false,
    [&](auto comment) { return comment.starts_with("//"); }
)
```

### 控制流表达式

这一部分适用于 `if`，`for`，`while`，`do-while` 表达式。

关键字、条件表达式和左大括号应在同一行。执行语句块应使用[语句块的格式化](#语句块)。

如果有 `else` 部分，则 `else` 之前的右大括号，`else`，接下来的条件表达式和左大括号都应在同一行。`else` 前后应有一个空格。例如：

```cpp
if (...) {
    ...
} else {
    ...
}

if (...) {
    ...
} else if (...) {
    ...
} else {
    ...
}
```

如果控制语句分行，则左大括号独占一行且不缩进。例如：

```cpp
if (a_long_expression
    && another_long_expression
    || a_third_long_expression)
{
    ...
}
```

#### 三元运算符

如果三元运算符分行，则在三元运算符前有一个换行符，并使用块缩进。运算符和运算数之间应有一个空格。

```cpp
int y = x ? 0 : 1;

// 必须拆为多行的一个例子。
int y = something_very_long
    ? not_small
    : also_not_small;
```

### Switch

`case` 语句和 `case` 语句后面的块都使用一个块缩进，`default` 后不要加 `break`：

```cpp
switch (foo) {
    case a_very_long_expression:
        // ...
        break;
    case another_expression:
    case yet_another_expression:
        // ...
        break;
    default:
        // ...
}
```

### 可结合的表达式

一个函数调用如果只有一个参数，且这个参数被格式化为多行，那么较外面的调用可以被当作单行调用来格式化。此类结合行为可以被用于任何相似的表达式（拥有多行、块缩进的子表达式列表，且被小括号括起来，例如宏和列表初始化的结构体）。例如，

```cpp
foo(bar(
    an_expr,
    another_expr
));

auto x = foo(Bar {
    whatever,
});

foo([&param]() {
    action();
    return foo(param);
});
```

这种结合规则适用于多层调用。然而格式化工具可能会限制嵌套的深度。

只有当多行的子表达式是一个 lambda 表达式的时候，这个结合规则才可能被用于拥有多个参数的函数调用，前提是所有参数和 lambda 表达式的首行都在第一行，lambda 表达式是最后一个参数且只有一个捕获变量。

```cpp
foo(first_arg, x, [param]() {
    action();
    return foo(param);
});
```

### 基于 range 的 for 循环

在基于 range 的 for 循环中，冒号后面应有一个空格。冒号前面没有空格。

```cpp
for(auto v: values) {}
```

### 十六进制字面量

十六进制字面量可以使用大写或小写字母，但是在同一表达式中不要混用大小写。在同一工程中应该使用同样的大小写，但是我们对此不进行建议。格式化工具应该提供转换混用大小写的字面量的选项，以及转换所有字面量为大写或小写的选项。

---

## 类型

### 单行格式化

* `T name[expr]`，例如 `uint32_t a[32]`, `std::vector<Foo> a[10 * 2 + foo()]`（中括号两边没有空格）
* `const T* name`, `T& name`（`*` 和 `&` 与类型名之间没有空格，与变量名之间有一个空格）
* `template<typename T, typename U>`（逗号后有一个空格，尖括号两边没有空格）
* `std::tuple<A, B, C, D>` （逗号后有空格，尖括号两边没有空格）
* `struct A: public B, public C {}` （冒号、逗号与类型名之间没有空格）。

### 分行

避免在类型中分行。如果要分行，最好在最外层分行。例如，这种写法

```cpp
Foo<
    Bar,
    Baz<Type1, Type2>,
>
```

比下面这个写法更好：

```cpp
Foo<Bar, Baz<
    Type1,
    Type2,
>>
```

---

# 其他风格建议

## 命名

 * 类型 `CamelCase`，
 * 枚举成员 `UPPER_CASE`，
 * 在以上情况中, 若使用缩写, 则仅大写缩写单词的首字母 `HttpRequest`。
 * 命名空间 `lower_case`，
 * 成员 `lower_case`，
 * 函数 `lower_case`，
 * 变量 `lower_case`，
 * 宏 `UPPER_CASE`，
 * 全局常量 `UPPER_CASE`，
* 如果和关键字冲突（例如 `namespace`），就连接一个下划线（例如 `namespace_`）。

## 单位

程序内部角度变量使用弧度制。给人编辑的角度常量（包括在参数表文件内）用角度制。

如果变量的名称中没有说明单位，默认采用国际单位。

```cpp
const double SOME_ANGLE = 30.0 / 180.0 * M_PI; // 角度制转弧度制
const double SIN_RESULT = std::sin(SOME_ANGLE);
```

## 不省略 if / for statements 的大括号

```cpp
// 使用
if (true) {
    continue;
}
// 不使用
if (true)
    continue;
```

## 文件后缀

使用 `.cpp` 和 `.hpp` 作为 C++ 文件后缀。

## 调用

除 std:: 外，不可跨具名作用域 (named scope) 调用。

```cpp
namespace a::b {
int foo(const int& x, const int& y) {  
    return std::abs(x + y); // ok
}
}

namespace a::b::c {
int foo() {
    // 使用
    return b::func(2, 3); // 光标在调用处时，需要保证调用处的第一个前缀 "b" 出现在面包屑中
    // 不使用
    return func(2, 3);
}
}
```

如果在当前作用域需大量使用外部作用域，请使用 namespace 别名。

```cpp
namespace a::b::c {
namespace foo = b::foo;

void fn0() {
    foo::Bar x;
}

void fn1() {
    foo::baz::Qux y;
}
}
```

不使用隐式 `this`。

```cpp
struct A {
public:
    A(const int& x);
private: 
    int member = 0;
}

A::A(const int& x) {
    // 使用
    this->member = x;
    // 不使用
    member = x;
}
```

## 使用 struct 而不是 class

## 不将 struct 作为命名空间使用

```cpp
// 使用
namespace a {
struct Foo {
    int a;
    int b;
};

struct Bar {
    int operator()(const Foo& x) {
        // 不将 Bar 视为作用域时，就可以理所当然地使用 Foo 而不是 a::Foo 来调用
        return x.a + x.b;
    }
};
} // namespace a

// 不使用
struct Bar {
    struct Foo {
        int a;
        int b;
    };

    int operator()(const Foo& x) {
        return x.a + x.b;
    }
};
```

---

# CMakeLists.txt, *.cmake 规范

## 格式约定

使用与 C++ 代码相同的行宽和缩进。

在标准键名（不含空格等）周围不使用引号。

```cmake
set(key "value")
```

---

## 非常重要的指南

块缩进比对齐缩进更好。例如：

```cpp
// 块缩进
a_function_call(
    foo,
    bar
);

// 对齐缩进
a_function_call(foo,
                bar);
```

这样能减小代码修改时的 diff（例如 `a_function_call` 在上例中被重命名）而且能防止右飘。

列表元素应尾随逗号，这样移动代码（比如：复制粘贴）更容易，而且减小代码修改造成的 diff。
