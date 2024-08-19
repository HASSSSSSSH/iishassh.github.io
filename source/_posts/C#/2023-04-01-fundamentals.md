---
title: C# 基础
date: 2023-04-01
updated: 2024-08-10
permalink: /csharp/fundamentals/
categories:
- [C#]
---

# 1. C# 程序的通用结构

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/program-structure/
>
> https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/#program-structure

C# 程序由一个或多个文件组成，每个文件均包含 0 个或多个命名空间，一个命名空间包含类、结构、接口、枚举、委托等类型或者其他命名空间。

C# 中的关键组织结构概念包括 *程序 (programs)*、*命名空间 (namespaces)*、*类型 (types)*、*成员 (members)* 和 *程序集 (assemblies)*。程序声明类型，而类型 (类、结构、接口等) 则包含成员 (字段、方法、属性、事件等)，并被整理到命名空间中。

编译完的 C# 程序实际上会打包到程序集中，程序集的文件扩展名通常为 `.exe` 或 `.dll`，具体视其分别实现的是应用程序还是库而定。

C# 程序可以存储在多个源文件中。在编译 C# 程序时，将同时处理所有源文件，并且源文件可以自由地相互引用。从概念上讲，就好像所有源文件在被处理之前都连接到一个大文件。

C# 并不限制源文件只能声明一种公共类型，也不要求源文件的文件名必须与其中声明的类型相匹配。



<!-- more -->

# 2. 类型系统

## 2.1 类型和变量

> https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/#types-and-variables

### 2.1.1 类型

C# 是一种强类型语言。

**类型 (Type)** 定义 C# 中的任何数据的结构和行为。类型的声明可以包含其成员、基类型、实现的接口以及该类型允许的操作。

C# 中的类型可以分为两大类：**值类型 (Value Types)** 和 **引用类型 (Reference Types)**。



### 2.1.2 变量

**变量 (Variable)** 是用于引用特定类型的实例的标签。

**标识符 (Identifier)** 是变量的名称。标识符是不包含任何空格的 unicode 字符序列。如果标识符的前缀为 `@`，则该标识符可以是 C# 保留字。在与其他语言交互时，使用保留字作为标识符很有用。

C# 中有多种变量，其中包括字段、数组元素、局部变量和参数。

变量表示存储位置，每个变量都具有一种类型，用于确定可以在变量中存储哪些值，如下所示：

- 不可为 null 的值类型
  - 具有精确类型的值
- 可为 null 的值类型
  - `null` 值
  - 具有精确类型的值
- object
  - `null` 引用
  - 对任意引用类型的对象的引用
  - 对任意值类型的装箱值的引用
- Class 类型
  - `null` 引用
  - 对类类型实例的引用
  - 对派生自类类型的类实例的引用
- Interface 类型
  - `null` 引用
  - 对实现接口类型的类类型实例的引用
  - 对实现接口类型的值类型的装箱值的引用
- Array 类型
  - `null` 引用
  - 对数组类型实例的引用
  - 对兼容的数组类型实例的引用
- Delegate 类型
  - `null` 引用
  - 对兼容的委托类型实例的引用



## 2.2 类型系统概述

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/

C# 是一种强类型语言。每个变量和常量都有一个类型，每个求值的表达式也是如此。每个方法声明都为每个输入参数和返回值指定名称、类型和种类 (值、引用或输出)。.NET 类库定义了内置数值类型和表示各种构造的复杂类型，其中包括文件系统、网络连接、对象的集合和数组以及日期。典型的 C# 程序使用类库中的类型，以及对程序问题域的专属概念进行建模的用户定义类型。

类型中可存储的信息包括：

- 类型变量所需的存储空间。
- 可以表示的最大值和最小值。
- 包含的成员 (方法、字段、事件等)。
- 继承自的基类型。
- 它实现的接口。
- 允许执行的运算种类。

编译器使用类型信息来确保在代码中执行的所有操作都是类型安全的。例如，如果声明了 `int` 类型的变量，那么编译器允许在加法和减法运算中使用此变量。如果尝试对 `bool` 类型的变量执行这些相同操作，则编译器将生成错误。

编译器将类型信息作为元数据嵌入可执行文件中。**公共语言运行时 (Common Language Runtime, CLR)** 在运行时使用元数据，以在分配和回收内存时进一步保证类型安全性。



### 2.2.1 在变量声明中指定类型

当在程序中声明变量或常量时，必须指定其类型或使用 `var` 关键字让编译器推断类型。

方法声明需要指定方法参数的类型和返回值。

在声明变量后，不能使用新的类型重新声明该变量，并且不能分配与其声明的类型不兼容的值。例如，不能在声明 `int` 后再向它分配 `true` 的布尔值。不过，可以将值转换成其他类型。例如，在将值分配给新变量或作为方法参数传递时，编译器会自动执行不会导致数据丢失的类型转换。如果类型转换可能会导致数据丢失，则必须在源代码中进行显式转换。



### 2.2.2 内置类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/built-in-types

C# 提供了一组标准的内置类型。这些类型表示整数、浮点值、布尔表达式、文本字符、十进制值和其他数据类型，还有内置的 `string` 和 `object` 类型。这些类型可供在任何 C# 程序中使用。

内置类型分为内置值类型和内置引用类型。其中，内置值类型包括：

| C# 类型关键字                                                | .NET 类型                                                    |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`bool`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/bool) | [System.Boolean](https://learn.microsoft.com/en-us/dotnet/api/system.boolean) |
| [`byte`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) | [System.Byte](https://learn.microsoft.com/en-us/dotnet/api/system.byte) |
| [`sbyte`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) | [System.SByte](https://learn.microsoft.com/en-us/dotnet/api/system.sbyte) |
| [`char`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/char) | [System.Char](https://learn.microsoft.com/en-us/dotnet/api/system.char) |
| [`decimal`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/floating-point-numeric-types) | [System.Decimal](https://learn.microsoft.com/en-us/dotnet/api/system.decimal) |
| [`double`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/floating-point-numeric-types) | [System.Double](https://learn.microsoft.com/en-us/dotnet/api/system.double) |
| [`float`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/floating-point-numeric-types) | [System.Single](https://learn.microsoft.com/en-us/dotnet/api/system.single) |
| [`int`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) | [System.Int32](https://learn.microsoft.com/en-us/dotnet/api/system.int32) |
| [`uint`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) | [System.UInt32](https://learn.microsoft.com/en-us/dotnet/api/system.uint32) |
| [`nint`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) | [System.IntPtr](https://learn.microsoft.com/en-us/dotnet/api/system.intptr) |
| [`nuint`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) | [System.UIntPtr](https://learn.microsoft.com/en-us/dotnet/api/system.uintptr) |
| [`long`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) | [System.Int64](https://learn.microsoft.com/en-us/dotnet/api/system.int64) |
| [`ulong`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) | [System.UInt64](https://learn.microsoft.com/en-us/dotnet/api/system.uint64) |
| [`short`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) | [System.Int16](https://learn.microsoft.com/en-us/dotnet/api/system.int16) |
| [`ushort`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) | [System.UInt16](https://learn.microsoft.com/en-us/dotnet/api/system.uint16) |

内置引用类型包括：

| C# 类型关键字                                                | .NET 类型                                                    |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [`object`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/reference-types#the-object-type) | [System.Object](https://learn.microsoft.com/en-us/dotnet/api/system.object) |
| [`string`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/reference-types#the-string-type) | [System.String](https://learn.microsoft.com/en-us/dotnet/api/system.string) |
| [`dynamic`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/reference-types#the-dynamic-type) | [System.Object](https://learn.microsoft.com/en-us/dotnet/api/system.object) |

在上表中，左侧列中的每个 C# 类型关键字 (`dynamic` 除外) 都是相应 .NET 类型的别名，它们是可互换的。例如，以下代码声明了相同类型的变量：

```c#
int a = 123;
System.Int32 b = 123;
```

[`void`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/void) 关键字表示缺少类型，将其用作不返回值的方法的返回类型。



### 2.2.3 自定义类型

可以使用 [`struct`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/struct)、[`class`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/class)、[`interface`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interface)、[`enum`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/enum) 和 [`record`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/record) 构造来创建自己的自定义类型。.NET 类库本身是一组自定义类型，提供给应用程序使用。默认情况下，类库中最常用的类型在任何 C# 程序中均可用。其他类型只有在显式添加对定义这些类型的程序集的项目引用时才可用。编译器引用程序集之后，可以声明在源代码的此程序集中声明的类型的变量和常量。可在 [.NET Class Library](https://learn.microsoft.com/en-us/dotnet/standard/class-library-overview) 了解关于 .NET 类库的更多信息。



### 2.2.4 通用类型系统

对于 .NET 中的类型系统，需要了解以下两个基本要点：

- 类型系统支持继承原则。类型可以派生自其他类型 (称为基类型)。派生类型继承 (存在一些限制) 基类型的方法、属性和其他成员。基类型可以继而从某种其他类型派生，在这种情况下，派生类型继承其继承层次结构中的两种基类型的成员。所有类型 (包括 `System.Int32` 等内置数值类型) 最终都派生自单个基类型，即 `System.Object`。这样的统一类型层次结构称为 **[通用类型系统 (Common Type System, CTS)](https://learn.microsoft.com/en-us/dotnet/standard/base-types/common-type-system)**。
- CTS 中的每种类型被定义为值类型或引用类型。这些类型包括 .NET 类库中的所有自定义类型以及用户自定义类型。使用 `struct` 关键字定义的类型是值类型，所有内置数值类型都是 `structs`。使用 `class` 或 `record` 关键字定义的类型是引用类型。引用类型和值类型遵循不同的编译时规则和运行时行为。

下图展示了 CTS 中值类型和引用类型之间的关系：

![value-reference-types-common-type-system](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/types/media/index/value-reference-types-common-type-system.png)

类和结构是 .NET 通用类型系统的两种基本构造。C# 9 添加记录，记录是一种类。每种本质上都是一种数据结构，其中封装了同属一个逻辑单元的一组数据和行为。数据和行为是类、结构或记录的成员，这些行为包括方法、属性和事件等。

类、结构或记录的声明类似于一张蓝图，用于在运行时创建实例或对象。如果定义了名为 `Person` 的类、结构或记录，则 `Person` 是类型的名称。如果声明和初始化 `Person` 类型的变量 `p`，那么 `p` 就是所谓的 `Person` 对象或实例。可以创建同一 `Person` 类型的多个实例，每个实例都可以有不同的属性和字段值。

类属于引用类型。创建引用类型的对象后，向其分配对象的变量仅保留对相应内存的引用。将对象引用分配给新变量后，新变量会引用原始对象。通过一个变量所做的更改将反映在另一个变量中，因为它们引用相同的数据。

结构属于值类型。创建结构时，向其分配结构的变量保留结构的实际数据。将结构分配给新变量时，会复制结构。因此，新变量和原始变量包含相同数据的副本 (有两个)。对一个副本所做的更改不会影响另一个副本。

记录类型可以是引用类型 (`record class`) 或值类型 (`record struct`)。

一般来说，类用于对更复杂的行为建模。类通常存储计划在创建类对象后进行修改的数据。结构最适用于小型数据结构。结构通常存储不打算在创建结构后修改的数据。记录类型是具有附加编译器合成成员的数据结构。记录通常存储不打算在创建对象后修改的数据。



### 2.2.5 值类型

值类型派生自 [`System.ValueType`](https://learn.microsoft.com/en-us/dotnet/api/system.valuetype) (派生自 `System.Object`)。派生自 `System.ValueType` 的类型在 CLR 中具有特殊行为。值类型的变量直接包含其值。结构的内存在声明变量的任何上下文中进行内联分配。对于值类型变量，没有单独的堆分配或者垃圾回收开销。可以声明属于值类型的 `record struct` 类型，并包括记录的合成成员。

值类型分为两类：`struct` 和 `enum`。

内置的数值类型是结构，它们具有可访问的字段和方法：

```c#
// constant field on type byte.
byte b = byte.MaxValue;
```

但可将这些类型视为简单的非聚合类型，为其声明并赋值：

```c#
byte num = 0xA;
int i = 5;
char c = 'Z';
```

值类型已密封。不能从任何值类型 (例如 `System.Int32`) 派生类型。不能将结构定义为从任何用户定义的类或结构继承，因为结构只能从 `System.ValueType` 继承。但是，一个结构可以实现一个或多个接口。可以将结构类型强制转换为其实现的任何接口类型，这将导致 "装箱" 操作，以将结构包装在托管堆上的引用类型对象内。当将值类型传递给使用 `System.Object` 或任何接口类型作为输入参数的方法时，就会发生 "装箱" 操作。

使用 `struct` 关键字可以创建自定义值类型。结构通常用作一小组相关变量的容器，如以下示例所示：

```c#
public struct Coords
{
    public int x, y;

    public Coords(int p1, int p2)
    {
        x = p1;
        y = p2;
    }
}
```

另一种值类型是 `enum`。枚举定义的是一组已命名的整型常量。例如，.NET 类库中的 [`System.IO.FileMode`](https://learn.microsoft.com/en-us/dotnet/api/system.io.filemode) 枚举包含一组已命名的常量整数，用于指定打开文件应采用的方式，如下所示：

```c#
public enum FileMode
{
    CreateNew = 1,
    Create = 2,
    Open = 3,
    OpenOrCreate = 4,
    Truncate = 5,
    Append = 6,
}
```

其中，[`System.IO.FileMode.Create`](https://learn.microsoft.com/en-us/dotnet/api/system.io.filemode#system-io-filemode-create) 常量的值为 2。不过，名称对于阅读源代码的人来说更有意义。因此，最好使用枚举，而不是常量数字文本。

所有枚举从 [`System.Enum`](https://learn.microsoft.com/en-us/dotnet/api/system.enum) (继承自 `System.ValueType`) 继承。适用于结构的所有规则也适用于枚举。



### 2.2.6 引用类型

定义为 `class`、`record`、`delegate`、数组、 `interface` 的类型是引用类型。

在声明变量引用类型时，它将包含值 `null`，直到将其分配给该类型的实例，或者使用 `new` 运算符创建一个。

创建对象时，会在托管堆上分配内存。变量只保留对对象位置的引用。对于托管堆上的类型，在分配内存和回收内存时都会产生开销。垃圾回收是 CLR 的自动内存管理功能，用于执行回收。但是，垃圾回收已是高度优化，并且在大多数情况下，不会产生性能问题。可在 [自动内存管理](https://learn.microsoft.com/en-us/dotnet/standard/automatic-memory-management) 了解关于垃圾回收的详细信息。

所有数组都是引用类型，即使元素是值类型也不例外。数组隐式派生自 [`System.Array`](https://learn.microsoft.com/en-us/dotnet/api/system.array) 类。

引用类型完全支持继承。创建类时，可以从其他任何未定义为 `sealed` 的接口或类继承。其他类可以从自己创建的类继承并覆盖虚方法。



### 2.2.7 文本值的类型

在 C# 中，文本值从编译器接收类型。可以通过在数字末尾追加一个字母来指定数字文本应采用的类型。例如，如果要指定应按 `float` 来处理值 `4.56`，则应在该数字后追加一个 `f` 或者 `F`，即 `4.56f`。如果没有追加字母，那么编译器就会推断文本值的类型。可在 [整型数值类型](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types) 和 [浮点数值类型](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/floating-point-numeric-types) 了解可以使用字母后缀指定的类型。

由于文本已类型化，且所有类型最终都是从 `System.Object` 派生，因此可以编写和编译如下所示的代码：

```c#
string s = "The answer is " + 5.ToString();
// Outputs: "The answer is 5"
Console.WriteLine(s);

Type type = 12345.GetType();
// Outputs: "System.Int32"
Console.WriteLine(type);
```



### 2.2.8 泛型类型

可使用一个或多个类型参数声明的类型，用作实际类型的占位符。客户端代码在创建类型实例时提供具体类型。这种类型称为泛型类型。例如，.NET 类型 `System.Collections.Generic.List<T>` 具有一个类型参数，它按照惯例被命名为 `T`。当创建类型的实例时，指定列表将包含的对象的类型，例如 `string`。

通过使用类型参数，可重新使用相同类以保存任意类型的元素，且无需将每个元素转换为对象。泛型集合类称为强类型集合，因为编译器知道集合元素的具体类型，并能在编译时引发错误。



### 2.2.9 隐式类型、匿名类型和可为 null 的值类型

可以使用 [`var`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/var) 关键字隐式键入一个局部变量 (但不能是类成员)。变量仍然可以在编译时获取类型，但类型是由编译器提供。

当不方便为不打算存储或传递外部方法边界的简单相关值集合创建有命名的类型时，可以创建匿名类型。

普通的值类型不能具有 `null` 值。不过，可以在类型后面追加 `?`，来创建可为 null 的值类型。例如，`int?` 是还可以包含值 `null` 的 `int` 类型。可为 null 的值类型是泛型结构类型 [`System.Nullable<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.nullable-1) 的实例。在将数据传入和传出数据库 (数值可能为 `null`) 时，可为 null 的值类型特别有用。



### 2.2.10 编译时类型和运行时类型

变量可以具有不同的编译时和运行时类型。编译时类型是源代码中变量的声明或推断类型，运行时类型是该变量所引用的实例的类型，这两种类型通常是相同的。

但是，在一些情况下，编译时类型是不同的，如以下两个示例所示：

```c#
object anotherMessage = "This is another string of characters";
IEnumerable<char> someCharacters = "abcdefghijklmnopqrstuvwxyz";
```

在上面的示例中，运行时类型都为 `string`，编译时类型在第一行中为 `object`，而在第二行中为 `IEnumerable<char>`。

如果变量的这两种类型不同，需要了解编译时类型和运行时类型的应用情况。编译时类型确定编译器执行的所有操作。这些编译器操作包括方法调用解析、重载决策以及可用的隐式和显式强制转换。运行时类型确定在运行时解析的所有操作。这些运行时操作包括调度虚方法调用、计算 `is` 和 `switch` 表达式以及其他类型的测试 API。为了更好地了解代码如何与类型进行交互，应识别哪个操作应用于哪种类型。



## 2.3 命名空间

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/namespaces

在 C# 编程中，命名空间在两个方面被大量使用。首先，.NET 使用命名空间来组织它的许多类，例如：

```c#
System.Console.WriteLine("Hello World!");
```

其中，[System](https://learn.microsoft.com/en-us/dotnet/api/system) 是一个命名空间，[Console](https://learn.microsoft.com/en-us/dotnet/api/system.console) 是该命名空间中的一个类。可以使用 `using` 关键字，这样就不必使用完整的名称，例如：

```c#
using System;

Console.WriteLine("Hello World!");
```

其次，在较大的编程项目中，声明自己的命名空间有助于控制类和方法名称的范围。使用 [namespace](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/namespace) 关键字可以声明命名空间，例如：

```c#
namespace SampleNamespace
{
    class SampleClass
    {
        public void SampleMethod()
        {
            System.Console.WriteLine(
                "SampleMethod inside SampleNamespace");
        }
    }
}
```

命名空间的名称必须是有效的 C# [标识符名称](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/identifier-names)。

从 C# 10 开始，可以为该文件中定义的所有类型声明一个命名空间，如以下示例所示：

```c#
namespace SampleNamespace;

class AnotherSampleClass
{
    public void AnotherSampleMethod()
    {
        System.Console.WriteLine(
            "SampleMethod inside SampleNamespace");
    }
}
```

这种新语法的优点是更简单，节省了水平空间且不必使用大括号，使得代码更易于阅读。

命名空间具有以下属性：

- 命名空间用于组织大型代码项目。
- 通过使用 `.` 运算符分隔命名空间。
- `using` 指令可免去为每个类指定命名空间的名称。
- `global` 命名空间是根命名空间：`global::System` 始终引用 .NET `System` 命名空间。

### 2.3.1 using 指令

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/using-directive

`using` 指令允许使用在命名空间中定义的类型，而无需指定该类型的完全限定命名空间。`using` 指令以基本形式从单个命名空间导入所有类型，例如：

```c#
using System.Text;
```

可以将两个修饰符应用于 `using` 指令：

- `global` 修饰符与将同一 `using` 指令添加到项目中的每个源文件具有相同的效果。此修饰符是在 C# 10 中引入的。
- `static` 修饰符从单个类型中导入该 `static` 成员和嵌套类型，而不是导入命名空间中的所有类型。

可以结合使用这两个修饰符，从项目的所有源文件中的类型导入静态成员。

还可以通过 using 别名指令为名称空间或类型创建别名，例如：

```c#
using Project = PC.MyCompany.Project;
```

可以对 using 别名指令使用 `global` 修饰符。

没有 `global` 修饰符的 `using` 指令的作用域是显示它的文件。

可能出现 `using` 指令的位置如下：

- 源代码文件的开头，位于任何命名空间或类型声明之前。
- 在任何命名空间中，但在该命名空间中声明的任何命名空间或类型之前，除非使用 `global` 修饰符，在这种情况下，指令必须出现在所有命名空间和类型声明之前。

否则，将生成编译器错误 [CS1529](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/compiler-messages/using-directive-errors)。

创建 `using` 指令，以便在命名空间中使用类型而不必指定命名空间。需要注意，`using` 指令不提供对嵌套在指定命名空间中的任何命名空间的访问权限。命名空间分为两类：用户定义的命名空间和系统定义的命名空间。用户定义的命名空间是在代码中定义的命名空间。可在 [.NET API Browser](https://learn.microsoft.com/en-us/dotnet/api/) 了解关于系统定义的命名空间的信息。

另外，`using` 关键字还用于创建 `using` 语句，此类语句有助于确保正确处理 [IDisposable](https://learn.microsoft.com/en-us/dotnet/api/system.idisposable) 对象 (如文件和字体)。可在 [using 语句](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/using) 了解详细信息。



## 2.4 值类型和引用类型

> https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/#types-and-variables
>
> https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/types
>

C# 类型系统的大纲如下：

- **值类型 (Value types)**

  - **简单类型 (Simple types)**

    - **有符号整型 (Signed integral)**

      其中有 `sbyte`、`short`、`int`、`long`

    - **无符号整型 (Unsigned integral)**

      其中有 `byte`、`ushort`、`uint`、`ulong`

    - **Unicode 字符 (Unicode characters)**

      其中有 `char`，表示 UTF-16 代码单元

    - **IEEE 二进制浮点 (IEEE binary floating-point)**

      其中有 `float`、`double`

    - **高精度十进制浮点数 (High-precision decimal floating-point)**

      其中有 `decimal`

    - **布尔值 (Boolean)**

      其中有 `bool`，表示 `true` 或 `false`

  - **枚举类型 (Enum types)**

    - `enum E {...}` 格式的用户定义类型

      `enum` 类型是一种包含已命名常量的独特类型。

      每个 `enum` 类型都有一个基础类型，而且必须是上面提到的 8 种整型类型之一。

      `enum` 类型的值集与基础类型的值集相同。

  - **结构类型 (Struct types)**

    - 格式为 `struct S {...}` 的用户定义类型

  - **可为 null 的值类型 (Nullable value types)**

    - 值为 `null` 的其他所有值类型的扩展

  - **元组值类型 (Tuple value types)**

    - 格式为 `(T1, T2, ...)` 的用户定义类型

- **引用类型 (Reference types)**

  - **类类型 (Class types)**
    - 其他所有类型的最终基类：`object`
    - Unicode 字符串：`string`，表示 UTF-16 代码单元序列
    - 格式为 `class C {...}` 的用户定义类型
  - **接口类型 (Interface types)**
    - 格式为 `interface I {...}` 的用户定义类型
  - **数组类型 (Array types)**
    - 一维、多维和交错。例如：`int[]`、`int[,]` 和 `int[][]`
  - **委托类型 (Delegate types)**
    - 格式为 `delegate int D(...)` 的用户定义类型

C# 程序使用 *类型声明 (type declarations)* 创建新类型。类型声明指定新类型的名称和成员。

用户可定义以下 6 种 C# 类型：类类型、结构类型、接口类型、枚举类型、委托类型和元组值类型，还可以声明 `record` 类型 (`record struct` 或 `record class`)。



### 2.4.1 值类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/value-types

值类型的变量直接包含数据。

值类型的变量没有单独的堆分配或垃圾回收开销，每个变量都有自己的数据副本。因此，对一个变量执行的运算不会影响另一个变量 (`ref` 和 `out` 参数变量除外)。

值类型隐式派生自 [`System.ValueType`](https://learn.microsoft.com/en-us/dotnet/api/system.valuetype)。

值类型已密封，不能从任何值类型 (例如 `System.Int32`) 派生类型。

如果值类型包含引用类型的数据成员，则在复制值类型实例时，只会复制对引用类型实例的引用。副本和原始值类型实例都具有对同一引用类型实例的访问权限。

#### 2.4.1.1 简单类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/value-types#built-in-value-types

C# 提供以下内置值类型，也称为简单类型：

- [整型数值类型](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/integral-numeric-types)
- [浮点型数值类型](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/floating-point-numeric-types)
- [bool](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/bool)，表示布尔值
- [char](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/char)，表示 Unicode UTF-16 字符

所有简单类型都是结构类型，它们与其他结构类型的不同之处在于，它们允许特定的额外操作：

- 可以使用文字为简单类型提供值。例如，`'A'` 是类型 `char` 的文本，`2001` 是类型 `int` 的文本。
- 可以使用 `const` 关键字声明简单类型的常数。不能具有其他结构类型的常数。
- 常数表达式的操作数都是简单类型的常数，在编译时进行评估。

值元组是值类型，但不是简单类型。

#### 2.4.1.2 结构类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/struct

结构类型是用户可自定义的类型，可包含数据成员 (字段等) 和函数成员 (方法、属性等) 。结构类型属于值类型，从 `System.ValueType` 隐式派生。结构类型是较为简单的类型，其主要目的是存储数据值。

##### 2.4.1.2.1 概述

*结构类型 (structure type 或 struct type)* 是一种可封装数据和相关功能的值类型。使用 `struct` 关键字来定义结构类型。

结构类型具有 *值语义 (value semantics)*。也就是说，结构类型的变量包含类型的实例。默认情况下，在分配中，通过将参数传递给方法并返回方法结果来复制变量值。对于结构类型变量，将复制该类型的实例。

通常，可以使用结构类型来设计以数据为中心的较小类型，这些类型只有很少的行为或者没有行为。例如，.NET 使用结构类型来表示数字 (整数和实数)、布尔值、Unicode 字符以及 [时间实例](https://learn.microsoft.com/en-us/dotnet/api/system.datetime)。如果侧重于类型的行为，应考虑定义一个类。类类型具有 *引用语义 (reference semantics)*，即类类型的变量包含的是对类型的实例的引用，而不是实例本身。

由于结构类型具有值语义，因此建议定义 *不可变的 (immutable)* 结构类型。

##### 2.4.1.2.2 结构初始化和默认值

`struct` 类型的变量直接包含该 `struct` 类型的数据。这会让未初始化的 `struct` (具有其默认值) 和已初始化的 `struct` (通过构造值来存储一组值) 之间存在区别。例如：

```c#
public readonly struct Measurement
{
    public Measurement()
    {
        Value = double.NaN;
        Description = "Undefined";
    }

    public Measurement(double value, string description)
    {
        Value = value;
        Description = description;
    }

    public double Value { get; init; }
    public string Description { get; init; }

    public override string ToString() => $"{Value} ({Description})";
}

public static void Main()
{
    var m1 = new Measurement();
    Console.WriteLine(m1);  // output: NaN (Undefined)

    var m2 = default(Measurement);
    Console.WriteLine(m2);  // output: 0 ()

    var ms = new Measurement[2];
    Console.WriteLine(string.Join(", ", ms));  // output: 0 (), 0 ()
}
```

在上面的示例中，使用 [默认值表达式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/default) 忽略了无参数构造函数，并生成了结构类型的默认值。结构类型数组实例化还忽略无参数构造函数并生成使用结构类型的默认值填充的数组。

结构的所有成员字段在创建时必须进行明确指定，因为 `struct` 类型直接存储其数据。结构的 `default` 值已将所有字段明确指定为 0。调用构造函数时，必须明确指定所有字段。可以使用以下机制初始化字段：

- 可以将字段初始化表达式添加到任何字段或自动实现的属性。
- 可以在构造函数主体中初始化任何字段或自动属性。

##### 2.4.1.2.3 结构类型的设计限制

结构具有 `class` 类型的大部分功能，但也存在一些限制：

- 结构类型不能从其他类或结构继承，也不能作为其他类或结构的基类。但是，结构可以实现一个或多个接口。可以将结构类型强制转换为其实现的任何接口类型，这将导致 "装箱" 操作，以将结构包装在托管堆上的引用类型对象内。
- 不能在结构类型中声明终结器。
- 在 C# 11 之前，结构类型的构造函数必须初始化该类型的所有实例字段。

##### 2.4.1.2.4 按引用传递结构类型变量

将结构类型变量作为参数传递给方法或从方法返回结构类型值时，将复制结构类型的整个实例。通过值传递可能会影响高性能方案中涉及大型结构类型的代码的性能。通过按引用传递结构类型变量，可以避免值复制操作。使用 `ref`、`out`、`in` 或 `ref readonly` 方法参数修饰符，指示必须按引用传递某个参数。使用 ref 返回值按引用返回方法结果。

##### 2.4.1.2.5 转换

对于任何结构类型 (`ref struct` 类型除外)，都存在与 `System.ValueType` 和 `System.Object` 类型之间的装箱和取消装箱相互转换。还存在结构类型和它所实现的任何接口之间的装箱和取消装箱转换。

##### 2.4.1.2.6 readonly 结构

可以使用 `readonly` 修饰符来声明结构类型为不可变。`readonly` 结构的所有数据成员都必须是只读的，如下所示：

- 任何字段声明都必须具有 `readonly` 修饰符。
- 任何属性 (包括自动实现的属性) 都必须是只读的或 [init only](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/init)。

这样可以保证 `readonly` 结构的成员不会修改该结构的状态，意味着除构造函数外的其他实例成员是隐式 `readonly`。

在 `readonly` 结构中，可变引用类型的数据成员仍可改变其自身的状态。例如，不能替换 `List<T>` 实例，但可以向其中添加新元素。

##### 2.4.1.2.7 readonly 实例成员

还可以使用 `readonly` 修饰符来声明实例成员不会修改结构的状态。如果不能将整个结构类型声明为 `readonly`，可使用 `readonly` 修饰符标记不会修改结构状态的实例成员。

在 `readonly` 实例成员内，不能分配到结构的实例字段。但是，`readonly` 成员可以调用非 `readonly` 成员。在这种情况下，编译器将创建结构实例的副本，并调用该副本上的非 `readonly` 成员。因此，不会修改原始结构实例。

通常，将 `readonly` 修饰符应用于以下类型的实例成员：

- 方法：

  ```c#
  public readonly double Sum()
  {
      return X + Y;
  }
  ```

  还可以将 `readonly` 修饰符应用于可替代在 `System.Object` 中声明的方法的方法：

  ```c#
  public readonly override string ToString() => $"({X}, {Y})";
  ```

- 属性和索引器：

  ```c#
  private int counter;
  public int Counter
  {
      readonly get => counter;
      set => counter = value;
  }
  ```

  如果需要将 `readonly` 修饰符应用于属性或索引器的两个访问器，应在属性或索引器的声明中应用它。

  注意，编译器会将自动实现的属性的 `get` 访问器声明为 `readonly`，而不管属性声明中是否存在 `readonly` 修饰符。

  可以将 `readonly` 修饰符应用于具有 `init` 访问器的属性或索引器：

  ```c#
  public readonly double X { get; init; }
  ```

  可以将 `readonly` 修饰符应用于结构类型的静态字段，但不能应用于任何其他静态成员，例如属性或方法。

  编译器可以使用 `readonly` 修饰符进行性能优化。

##### 2.4.1.2.8 record 结构

从 C# 10 开始，可定义记录结构类型。记录类型提供用于封装数据的内置功能。可以同时定义 `record struct` 和 `readonly record struct` 类型。记录结构不能是 `ref struct`。

##### 2.4.1.2.9 ref 结构

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/ref-struct

可以在结构类型的声明中使用 `ref` 修饰符。`ref struct` 类型的实例是在栈上分配的，不能转移到托管堆。为了确保这一点，编译器将 `ref struct` 类型的使用限制为：

- `ref struct` 不能是数组的元素类型。
- `ref struct` 不能是类或者非 `ref struct` 的字段的声明类型。
- `ref struct` 不能实现接口。
- `ref struct` 不能被装箱为 `System.ValueType` 或 `System.Object`。
- `ref struct` 不能是类型参数。
- `ref struct` 变量不能由 Lambda 表达式或者本地函数捕获。
- `ref struct` 变量不能在 `async` 方法中使用。但是，可以在同步方法中使用 `ref struct` 变量。例如，在返回 `Task` 或 `Task<TResult>` 的方法中使用。
- `ref struct` 变量不能在迭代器中使用。

可以定义一次性的 `ref struct`。为此，应确保 `ref struct` 符合 [一次性模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-8.0/using#pattern-based-using)。也就是说，它有一个实例 `Dispose` 方法，该方法是可访问、无参数的，并且具有 `void` 返回类型。可以将 using 语句与可释放的 `ref struct` 的实例一起使用。

通常，如果需要一种同时包含 `ref struct` 类型的数据成员的类型，可以定义 `ref struct` 类型：

```c#
public ref struct CustomRef
{
    public bool IsValid;
    public Span<int> Inputs;
    public Span<int> Outputs;
}
```

如果要将 `ref struct` 声明为 `readonly`，应在类型声明中组合使用 `readonly` 修饰符和 `ref` 修饰符，`readonly` 修饰符必须位于 `ref` 修饰符之前：

```c#
public readonly ref struct ConversionRequest
{
    public ConversionRequest(double rate, ReadOnlySpan<double> values)
    {
        Rate = rate;
        Values = values;
    }

    public double Rate { get; }
    public ReadOnlySpan<double> Values { get; }
}
```

在 .NET 中，`ref struct` 的示例分别是 [`System.Span<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.span-1) 和 [`System.ReadOnlySpan<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.readonlyspan-1)。

#### 2.4.1.3 枚举类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/enum

枚举类型是由基础整型数值类型的一组命名常量定义的值类型。

默认情况下，枚举成员的关联常数值为类型 `int`，从 0 开始，并按定义文本顺序递增 1。可以显式指定任何其他整数数值类型作为枚举类型的基础类型，还可以显式指定关联的常数值。

不能在枚举类型的定义内定义方法。如果需要向枚举类型添加功能，应创建扩展方法。

枚举类型 `E` 的默认值是由表达式 `(E)0` 生成的值，即使 0 没有相应的枚举成员也是如此。

所有枚举从 [`System.Enum`](https://learn.microsoft.com/en-us/dotnet/api/system.enum) (继承自 `System.ValueType`) 继承，`System.Enum` 提供多种方法来获取有关枚举类型及其值的信息。例如，使用 [`Enum.IsDefined`](https://learn.microsoft.com/en-us/dotnet/api/system.enum.isdefined) 方法来确定枚举类型是否包含具有特定关联值的枚举成员。

##### 2.4.1.3.1 作为位标志的枚举类型

如果希望枚举类型表示选项组合，可以为这些选项定义枚举成员，以便使单个选项成为位字段。也就是说，这些枚举成员的关联值应该是 2 的幂。然后，可以使用按位逻辑运算符 `|` 或 `&` 分别合并选项或交叉组合选项。如果想要指示枚举类型声明位字段，应对其应用 [Flags](https://learn.microsoft.com/en-us/dotnet/api/system.flagsattribute) 特性。例如：

```c#
[Flags]
public enum Days
{
    None      = 0b_0000_0000,  // 0
    Monday    = 0b_0000_0001,  // 1
    Tuesday   = 0b_0000_0010,  // 2
    Wednesday = 0b_0000_0100,  // 4
    Thursday  = 0b_0000_1000,  // 8
    Friday    = 0b_0001_0000,  // 16
    Saturday  = 0b_0010_0000,  // 32
    Sunday    = 0b_0100_0000,  // 64
    Weekend   = Saturday | Sunday
}

public class FlagsEnumExample
{
    public static void Main()
    {
        Days meetingDays = Days.Monday | Days.Wednesday | Days.Friday;
        Console.WriteLine(meetingDays);
        // Output:
        // Monday, Wednesday, Friday

        Days workingFromHomeDays = Days.Thursday | Days.Friday;
        Console.WriteLine($"Join a meeting by phone on {meetingDays & workingFromHomeDays}");
        // Output:
        // Join a meeting by phone on Friday

        bool isMeetingOnTuesday = (meetingDays & Days.Tuesday) == Days.Tuesday;
        Console.WriteLine($"Is there a meeting on Tuesday: {isMeetingOnTuesday}");
        // Output:
        // Is there a meeting on Tuesday: False

        var a = (Days)37;
        Console.WriteLine(a);
        // Output:
        // Monday, Wednesday, Saturday
    }
}
```

##### 2.4.1.3.2 转换

对于任何枚举类型，枚举类型与其基础整型类型之间存在显式转换。如果将枚举值转换为其基础类型，则结果为枚举成员的关联整数值。

可以使用 [`Enum.IsDefined`](https://learn.microsoft.com/en-us/dotnet/api/system.enum.isdefined) 方法来确定枚举类型是否包含具有特定关联值的枚举成员。

对于任何枚举类型，都存在分别与 `System.Enum` 类型的装箱和取消装箱相互转换。

#### 2.4.1.4 元组类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/value-tuples

元组提供了简洁的语法来将多个数据元素分组成一个轻型数据结构。可以通过声明 `(` 和 `)` 之间的成员的类型和名称来实例化元组，例如：

```c#
(double, int) t1 = (4.5, 3);
Console.WriteLine($"Tuple with elements {t1.Item1} and {t1.Item2}.");
// Output:
// Tuple with elements 4.5 and 3.

(double Sum, int Count) t2 = (4.5, 3);
Console.WriteLine($"Sum of {t2.Count} elements is {t2.Sum}.");
// Output:
// Sum of 3 elements is 4.5.
```

在上面的示例中，如果想要定义元组类型，需要指定其所有数据成员的类型。或者，可以指定字段名称。

虽然不能在元组类型中定义方法，但可以使用 .NET 提供的方法，例如：

```c#
(double, int) t = (4.5, 3);
Console.WriteLine(t.ToString());
Console.WriteLine($"Hash code of {t} is {t.GetHashCode()}.");
// Output:
// (4.5, 3)
// Hash code of (4.5, 3) is 718460086.
```

元组类型是值类型，元组元素是公共字段，这使得元组为可变的值类型。

##### 2.4.1.4.1 元组字段名称

可以在元组初始化表达式中或元组类型的定义中显式指定元组字段名称，例如：

```c#
var t = (Sum: 4.5, Count: 3);
Console.WriteLine($"Sum of {t.Count} elements is {t.Sum}.");

(double Sum, int Count) d = (4.5, 3);
Console.WriteLine($"Sum of {d.Count} elements is {d.Sum}.");
```

如果未指定字段名称，则可以根据元组初始化表达式中相应变量的名称来推断出名称，例如：

```c#
var sum = 4.5;
var count = 3;
var t = (sum, count);
Console.WriteLine($"Sum of {t.count} elements is {t.sum}.");
```

这称为元组投影初始值设定项。在以下情况下，变量名称不会被投影到元组字段名称中：

- 候选名称是元组类型的成员名称，例如 `Item3`、`ToString` 或 `Rest`。
- 候选名称是另一元组的显式或隐式字段名称的重复项。

可以显式指定字段的名称，或者按字段的默认名称访问字段。

元组字段的默认名称为 `Item1`、`Item2`、`Item3` 等。始终可以使用字段的默认名称，即使字段名称是显式指定的或推断出的，例如：

```c#
var a = 1;
var t = (a, b: 2, 3);
Console.WriteLine($"The 1st element is {t.Item1} (same as {t.a}).");
Console.WriteLine($"The 2nd element is {t.Item2} (same as {t.b}).");
Console.WriteLine($"The 3rd element is {t.Item3}.");
// Output:
// The 1st element is 1 (same as 1).
// The 2nd element is 2 (same as 2).
// The 3rd element is 3.
```

元组赋值和元组相等性比较不会考虑字段名称。

在编译时，编译器会将非默认字段名称替换为相应的默认名称。因此，显式指定或者推断的字段名称在运行时不可用。

##### 2.4.1.4.2 元组赋值和析构

C# 支持满足以下两个条件的元组类型之间的赋值：

- 两个元组类型有相同数量的元素。
- 对于每个元组位置，右侧元组元素的类型与左侧相应的元组元素的类型相同或可以隐式转换为左侧相应的元组元素的类型。

元组元素是按照元组元素的顺序赋值的。元组字段的名称会被忽略且不会被赋值，例如：

```c#
(int, double) t1 = (17, 3.14);
(double First, double Second) t2 = (0.0, 1.0);
t2 = t1;
Console.WriteLine($"{nameof(t2)}: {t2.First} and {t2.Second}");
// Output:
// t2: 17 and 3.14

(double A, double B) t3 = (2.0, 3.0);
t3 = t2;
Console.WriteLine($"{nameof(t3)}: {t3.A} and {t3.B}");
// Output:
// t3: 17 and 3.14
```

还可以使用赋值运算符 `=` 在单独的变量中析构元组实例。可以通过许多方式来进行此操作：

- 在括号外使用 `var` 关键字来声明隐式类型化变量，并让编译器推断其类型：

  ```c#
  var t = ("post office", 3.6);
  var (destination, distance) = t;
  Console.WriteLine($"Distance to {destination} is {distance} kilometers.");
  // Output:
  // Distance to post office is 3.6 kilometers.
  ```

- 在括号内显式声明每个变量的类型：

  ```c#
  var t = ("post office", 3.6);
  (string destination, double distance) = t;
  Console.WriteLine($"Distance to {destination} is {distance} kilometers.");
  // Output:
  // Distance to post office is 3.6 kilometers.
  ```

- 在括号内显式声明一些类型，隐式声明其他类型 (使用 `var`)：

  ```c#
  var t = ("post office", 3.6);
  (var destination, double distance) = t;
  Console.WriteLine($"Distance to {destination} is {distance} kilometers.");
  // Output:
  // Distance to post office is 3.6 kilometers.
  ```

- 使用现有变量：

  ```c#
  var destination = string.Empty;
  var distance = 0.0;
  
  var t = ("post office", 3.6);
  (destination, distance) = t;
  Console.WriteLine($"Distance to {destination} is {distance} kilometers.");
  // Output:
  // Distance to post office is 3.6 kilometers.
  ```

析构表达式的目标可以包括现有变量和析构声明中声明的变量。

还可以将析构与模式匹配相结合，以检查元组中字段的特征。以下示例循环访问多个整数，并输出可被 3 整除的整数。它析构 [`Int32.DivRem`](https://learn.microsoft.com/en-us/dotnet/api/system.int32.divrem) 的元组结果，并与 `Remainder` 0 进行匹配：

```c#
for (int i = 4; i < 20;  i++)
{
    if (Math.DivRem(i, 3) is ( Quotient: var q, Remainder: 0 ))
    {
        Console.WriteLine($"{i} is divisible by 3, with quotient {q}");
    }
}
```

##### 2.4.1.4.3 元组的相等性

元组类型支持 `==` 和 `!=` 运算符。这些运算符按照元组元素的顺序将左侧操作数的成员与相应的右侧操作数的成员进行比较。例如：

```c#
(int, double) t1 = (17, 3.14);
(double First, double Second) t2 = (0.0, 1.0);
t2 = t1;
Console.WriteLine($"{nameof(t2)}: {t2.First} and {t2.Second}");
// Output:
// t2: 17 and 3.14

(double A, double B) t3 = (2.0, 3.0);
t3 = t2;
Console.WriteLine($"{nameof(t3)}: {t3.A} and {t3.B}");
// Output:
// t3: 17 and 3.14
```

如上面的示例所示，`==` 和 `!=` 操作不会考虑元组字段名称。

当同时满足以下两个条件时，两个元组可比较：

- 两个元组具有相同数量的元素。例如，如果 `t1` 和 `t2` 具有不同数目的元素，`t1 != t2` 则不会进行编译。
- 对于每个元组位置，可以使用 `==` 和 `!=` 运算符对左右侧元组操作数中的相应元素进行比较。例如，`(1, (2, 3)) == ((1, 2), 3)` 不会进行编译，因为 `1` 不可与 `(1, 2)` 比较。

##### 2.4.1.4.4 元组与 System.Tuple

[`System.ValueTuple`](https://learn.microsoft.com/en-us/dotnet/api/system.valuetuple) 类型支持的 C# 元组不同于 [`System.Tuple`](https://learn.microsoft.com/en-us/dotnet/api/system.tuple) 类型表示的元组。主要区别如下：

- `System.ValueTuple` 类型是值类型，而 `System.Tuple` 类型是引用类型。
- `System.ValueTuple` 类型是可变的，而 `System.Tuple` 类型是不可变的。
- `System.ValueTuple` 类型的数据成员是字段，而 `System.Tuple` 类型的数据成员是属性。

#### 2.4.1.5 可为 null 的值类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-value-types

可为 null 值类型 `T?`，表示其基础值类型 `T` 的所有值及额外的 [`null`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/null) 值。

任何可为 null 的值类型都是泛型 [`System.Nullable<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.nullable-1) 结构的实例。可以使用以下任何一种可互换形式引用具有基础类型 `T` 的可为 null 值类型：`Nullable<T>` 或 `T?`。

当需要表示基础值类型的未定义值时，通常使用可为 null 的值类型。例如，布尔值或 `bool` 变量只能为 `true` 或 `false`。但是，有时变量值可能未定义或者缺失。例如，某个数据库字段可能包含 `true` 或 `false`，或者它可能不包含任何值，即 `NULL`。在这种情况下，可以使用 `bool?` 类型。

##### 2.4.1.5.1 声明和赋值

由于值类型可隐式转换为相应的可为 null 的值类型，因此可以像向其基础值类型赋值一样，向可为 null 值类型的变量赋值，还可以分配 `null` 值。

可为 null 值类型的默认值是 `null`。

##### 2.4.1.5.2 检查可为 null 值类型的实例

可以将 `is` 运算符与类型模式结合使用，既检查 `null` 的可为 null 值类型的实例，又检索基础类型的值：

```c#
int? a = 42;
if (a is int valueOfA)
{
    Console.WriteLine($"a is {valueOfA}");
}
else
{
    Console.WriteLine("a does not have a value");
}
// Output:
// a is 42
```

始终可以使用以下只读属性来检查和获取可为 null 值类型变量的值：

- [`Nullable<T>.HasValue`](https://learn.microsoft.com/en-us/dotnet/api/system.nullable-1.hasvalue) 指示可为 null 值类型的实例是否有基础类型的值。
- 如果 `Nullable<T>.HasValue` 为 `true`，则 [`Nullable<T>.Value`](https://learn.microsoft.com/en-us/dotnet/api/system.nullable-1.value) 获取基础类型的值。如果 `Nullable<T>.HasValue` 为 `false`，则 `Nullable<T>.Value` 属性将引发 [`InvalidOperationException`](https://learn.microsoft.com/en-us/dotnet/api/system.invalidoperationexception)。

还可以将可为 null 的值类型的变量与 `null` 进行比较，而不是使用 `Nullable<T>.HasValue` 属性。

##### 2.4.1.5.3 从可为 null 的值类型转换为基础类型

如果要将可为 null 值类型的值分配给不可为 null 的值类型变量，则可能需要指定要分配的替代 `null` 的值。使用 Null 合并操作符 `??` 执行此操作，也可以将 [`Nullable<T>.GetValueOrDefault(T)`](https://learn.microsoft.com/en-us/dotnet/api/system.nullable-1.getvalueordefault#system-nullable-1-getvalueordefault(-0)) 方法用于相同的目的：

```c#
int? a = 28;
int b = a ?? -1;
Console.WriteLine($"b is {b}");  // output: b is 28

int? c = null;
int d = c ?? -1;
Console.WriteLine($"d is {d}");  // output: d is -1
```

如果要使用基础值类型的默认值来替代 `null`，应使用 `Nullable<T>.GetValueOrDefault(T)` 方法。

还可以将可为 null 的值类型显式强制转换为不可为 null 的类型，例如：

```c#
int? n = null;

//int m1 = n;    // Doesn't compile
int n2 = (int)n; // Compiles, but throws an exception if n is null
```

在运行时，如果可为 null 的值类型的值为 `null`，则显式强制转换将抛出 `InvalidOperationException`。

不可为 null 的值类型 `T` 可以隐式转换为相应的可为 null 值类型 `T?`。

##### 2.4.1.5.4 装箱和取消装箱

在对可为 null 值类型的实例 `T?` 进行装箱时，会有：

- 如果 `Nullable<T>.HasValue` 返回 `false`，则生成空引用。
- 如果 `Nullable<T>.HasValue` 返回 `true`，则将基础值类型 `T` 的对应值进行装箱，而不对 `Nullable<T>` 的实例进行装箱。

可以将值类型 `T` 的已装箱值取消装箱到相应的可为 null 值类型 `T?`。

##### 2.4.1.5.5 如何确定可为 null 的值类型

可以使用 [`Nullable.GetUnderlyingType`](https://learn.microsoft.com/en-us/dotnet/api/system.nullable.getunderlyingtype) 方法来确定 [`System.Type`](https://learn.microsoft.com/en-us/dotnet/api/system.type) 实例是否表示已构造的可为 null 值类型，即具有指定类型参数 `T` 的 `System.Nullable<T>` 类型：

```c#
Console.WriteLine($"int? is {(IsNullable(typeof(int?)) ? "nullable" : "non nullable")} value type");
Console.WriteLine($"int is {(IsNullable(typeof(int)) ? "nullable" : "non-nullable")} value type");

bool IsNullable(Type type) => Nullable.GetUnderlyingType(type) != null;

// Output:
// int? is nullable value type
// int is non-nullable value type
```

如上面示例所示，可以使用 `typeof` 运算符来创建 `System.Type` 实例。

如果需要确定实例是否是可为 null 的值类型，不应使用 `Object.GetType` 方法来获取 `System.Type` 实例。如果对可为 null 值类型的实例调用 `Object.GetType` 方法，该实例将装箱到 `Object`。由于对可为 null 值类型的非 null 实例的装箱等同于对基础类型的值的装箱，因此 GetType 会返回表示可为 null 的值类型的基础类型的 Type 实例。例如：

```c#
int? a = 17;
Type typeOfA = a.GetType();
Console.WriteLine(typeOfA.FullName);
// Output:
// System.Int32
```

另外，不能使用 `is` 运算符来确定实例是否是可为 null 的值类型，如下面示例所示：

```c#
int? a = 14;
if (a is int)
{
    Console.WriteLine("int? instance is compatible with int");
}

int b = 17;
if (b is int?)
{
    Console.WriteLine("int instance is compatible with int?");
}
// Output:
// int? instance is compatible with int
// int instance is compatible with int?
```



### 2.4.2 引用类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/reference-types

引用类型的变量存储对其数据 (这样的数据常被称为 "对象") 的引用。

对于引用类型，两个变量可以引用同一个对象，对一个变量执行的操作会影响另一个变量所引用的对象。

#### 2.4.2.1 内置引用类型

C# 提供了以下内置引用类型：

- object
- string
- dynamic

##### 2.4.2.1.1 object

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/reference-types#the-object-type

`object` 类型是 `System.Object` 在 .NET 中的别名。在 C# 的统一类型系统中，所有类型 (预定义类型、用户定义类型、引用类型和值类型) 都是直接或间接从 `System.Object` 继承的。可以将任何类型的值赋给 `object` 类型的变量。可以使用文本 `null` 将任何 `object` 变量赋值给其默认值。将值类型的变量转换为对象的过程称为 *装箱 (boxed)*。将 `object` 类型的变量转换为值类型的过程称为 *取消装箱 (unboxed)*。

##### 2.4.2.1.2 string

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/reference-types#the-string-type

`string` 类型表示 0 个或多个 Unicode 字符的序列。`string` 是 `System.String` 在 .NET 中的别名。

尽管 `string` 为引用类型，但是定义 [相等运算符 `==` 和 `!=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/equality-operators#string-equality) 可以比较 `string` 对象 (而不是引用) 的值。基于值的相等性使得对字符串相等性的测试更为直观。例如：

```c#
string a = "hello";
string b = "h";
// Append to contents of 'b'
b += "ello";
Console.WriteLine(a == b);
Console.WriteLine(object.ReferenceEquals(a, b));
```

在上面的示例中，首先显示 "True"，然后显示  "False"。因为字符串的内容是相等的，但 `a` 和 `b` 并不指向同一字符串实例。

可以使用 [`+` 运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/addition-operator#string-concatenation) 连接字符串：

```c#
string a = "good " + "morning";
```

上面的代码会创建一个包含 "good morning" 的字符串对象。

字符串是不可变的，即字符串对象在创建后，其内容不可更改。例如：

```c#
string b = "h";
b += "ello";
```

在上面的代码中，编译器实际上会创建一个新的字符串对象来保存新的字符序列，且该新对象赋值给 `b`。已为 `b` 分配的内存 (当它包含字符串 "h" 时) 可用于垃圾回收。

[`[]` 运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#indexer-operator-) 可用于只读访问字符串的个别字符。有效索引于 `0` 开始，且必须小于字符串的长度。例如：

```c#
string str = "test";
char x = str[2];  // x = 's';
```

同样，`[]` 运算符也可用于循环访问字符串中的每个字符：

```c#
string str = "test";

for (int i = 0; i < str.Length; i++)
{
  Console.Write(str[i] + " ");
}
// Output: t e s t
```

###### 2.4.2.1.2.1 字符串文本

字符串字面量属于 `string` 类型且能够以三种形式编写 (原始、带引号和逐字)。

*原始字符串字面量 (raw string literals)* 从 C# 11 开始可用。字符串字面量可以包含任意文本，而无需转义序列。字符串字面量可以包括空格和新行、嵌入引号以及其他特殊字符。原始字符串字面量用至少三个双引号 (""") 括起来：

```c#
"""
This is a multi-line
    string literal with the second line indented.
"""
```

*带引号字符串 (quoted string literals)* 括在双引号 (") 内：

```c#
"good morning"  // a string literal
```

字符串文本可包含任何字符文本，其中包括转义序列。使用转义序列 `\\` 表示反斜杠，使用 `\u0066` 表示字母 f，以及使用 `\n` 表示换行符。例如：

```c#
string a = "\\\u0066\n F";
Console.WriteLine(a);
// Output:
// \f
//  F
```

注意，转义码 `\udddd` (其中 `dddd` 是一个四位数字) 表示 Unicode 字符 U+`dddd`。另外，还可识别八位 Unicode 转义码：`\Udddddddd`。

[逐字字符串文本](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/tokens/verbatim) 以 `@` 开头，并且也括在双引号内。例如：

```c#
@"good morning"  // a string literal
```

逐字字符串的优点是不处理转义序列，这样就可以更轻松地编写。例如，以下文本与完全限定的 Windows 文件名匹配：

```c#
@"c:\Docs\Source\a.txt"  // rather than "c:\\Docs\\Source\\a.txt"
```

如果要在用 `@` 引起来的字符串中包含双引号，则双倍添加引号即可：

```c#
@"""Ahoy!"" cried the captain." // "Ahoy!" cried the captain.
```

###### 2.4.2.1.2.2 UTF-8 字符串字面量

.NET 中的字符串是使用 UTF-16 编码存储的。UTF-8 是 Web 协议和其他重要库的标准。从 C# 11 开始，可以将 `u8` 后缀添加到字符串字面量以指定 UTF-8 编码。UTF-8 字面量存储为 `ReadOnlySpan<byte>` 对象。UTF-8 字符串字面量的自然类型是 `ReadOnlySpan<byte>`。使用 UTF-8 字符串字面量创建的声明比声明等效的 [`System.ReadOnlySpan<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.readonlyspan-1) 更清晰，如以下代码所示：

```c#
ReadOnlySpan<byte> AuthWithTrailingSpace = new byte[] { 0x41, 0x55, 0x54, 0x48, 0x20 };
ReadOnlySpan<byte> AuthStringLiteral = "AUTH "u8;
```

如果要将 UTF-8 字符串字面量存储为数组，需要使用 [`ReadOnlySpan<T>.ToArray()`](https://learn.microsoft.com/en-us/dotnet/api/system.readonlyspan-1.toarray#system-readonlyspan-1-toarray) 将包含字面量的字节复制到可变数组：

```c#
byte[] AuthStringLiteral = "AUTH "u8.ToArray();
```

UTF-8 字符串字面量不是编译时常量，而是运行时常量。因此，不能将其用作可选参数的默认值。UTF-8 字符串字面量不能与字符串内插结合使用。不能对同一字符串表达式使用 `$` 令牌和 `u8` 后缀。

##### 2.4.2.1.3 dynamic

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/reference-types#the-dynamic-type

`dynamic` 类型表示变量的使用和对其成员的引用绕过编译时类型检查，改为在运行时解析这些操作。`dynamic` 类型简化了对 COM API (如 Office Automation API)、动态 API (如 IronPython 库) 和 HTML 文档对象模型 (DOM) 的访问。

在大多数情况下，`dynamic` 类型与 `object` 类型的行为类似。具体而言，任何非 Null 表达式都可以转换为 `dynamic` 类型。`dynamic` 类型与 `object` 的不同之处在于，编译器不会对包含类型 `dynamic` 的表达式的操作进行解析或类型检查。编译器将有关该操作信息打包在一起，之后这些信息会用于在运行时评估操作。在此过程中，`dynamic` 类型的变量会编译为 `object` 类型的变量。因此，`dynamic` 类型只在编译时存在，在运行时则不存在。

在下面的示例中，将 `dynamic` 类型的变量与 `object` 类型的变量进行对比。如果要在编译时验证每个变量的类型，应将鼠标指针放在 `WriteLine` 语句中的 `dyn` 或 `obj` 上。可以将下面的代码复制到可以使用 IntelliSense 的编辑器中，IntelliSense 对 `dyn` 显示 "dynamic"，对 `obj` 显示 "object"：

```c#
class Program
{
    static void Main(string[] args)
    {
        dynamic dyn = 1;
        object obj = 1;

        // Rest the mouse pointer over dyn and obj to see their
        // types at compile time.
        System.Console.WriteLine(dyn.GetType());
        System.Console.WriteLine(obj.GetType());
    }
}
```

`WriteLine` 语句显示 `dyn` 和 `obj` 的运行时类型。此时，两者的类型均为整数，控制台都将输出 "System.Int32"。

如果要查看编译时 `dyn` 与 `obj` 之间的区别，可以在上面示例的声明和 `WriteLine` 语句之间添加下列两行：

```c#
dyn = dyn + 3;
obj = obj + 3;
```

当尝试在表达式 `obj + 3` 中添加整数和对象时，将报告编译器错误。但是，对于 `dyn + 3`，不会报告任何错误。在编译时不会检查包含 `dyn` 的表达式，原因是 `dyn` 的类型为 `dynamic`。

#### 2.4.2.2 类类型

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/classes
>
> https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/types#classes-and-objects

类类型是用户可自定义的类型，其定义了一种包含数据成员 (字段等) 和函数成员 (方法、属性等) 的数据结构。

类类型支持单一继承和多形性，派生类可以通过这些机制扩展和特殊化基类。

类可以实现一个或多个接口。

##### 2.4.2.2.1 引用类型

定义为 `class` 的类型是引用类型。在运行时，如果声明引用类型的变量，该变量就会一直包含值 `null`，直到使用 `new` 运算符显式创建类实例，或者直到为此变量分配可能已在其他位置创建的兼容类型的对象。

在创建对象时，会在该托管堆上为该特定对象分足够的内存，并且该变量仅保存对所述对象位置的引用。对象使用的内存由 CLR 的自动内存管理功能 (称为 *垃圾回收 (garbage collection)*) 回收。可在 [Automatic memory management and garbage collection](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/fundamentals) 了解关于垃圾回收的详细信息。

##### 2.4.2.2.2 声明类

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/class

使用后跟唯一标识符的 `class` 关键字可以声明类，如以下示例所示：

```c#
//[access modifier] - [class] - [identifier]
public class Customer
{
   // Fields, properties, methods and events go here...
}
```

可选的访问修饰符位于 `class` 关键字前面。在上面的示例中使用的是 `public`，因此任何人都可创建该类的实例。类的名称在 `class` 关键字之后。类名称必须是有效的 C# 标识符名称。定义的其余部分是类的主体，其中定义了行为和数据。类上的字段、属性、方法和事件统称为 *类成员 (class members)*。

在 C# 中仅允许单一继承，一个类仅能从一个基类继承实现。但是，一个类可实现多个接口。

直接在命名空间中声明的、未嵌套在其它类中的类，可以是 `public` 或者 `internal`。默认情况下，类为 `internal`。

类成员 (包括嵌套的类) 可以是 `public`、`protected internal`、`protected`、`internal`、`private` 或者 `private protected`。默认情况下，成员为 `private`。

可以声明具有类型参数的泛型类。

一个类可以包含以下成员的声明：

- 构造函数
- 常量
- 字段
- 终结器
- 方法
- 属性
- 索引器
- 运算符
- 事件
- 委托
- 类
- 接口
- 结构类型
- 枚举类型

##### 2.4.2.2.3 类和对象

类和对象是不同的概念：一个类定义了一种对象类型，但是类不是对象本身。一个对象是基于某一个类的一个实体，也被称作类的 *实例 (instance)*。

在运行时，声明类类型的变量，此变量就会一直包含值 `null`，直到为此变量创建或者分配类实例。使用 `new` 运算符创建类实例，此运算符为新实例分配内存，调用构造函数来初始化实例，并返回对实例的引用。创建对象时，会在托管堆上为该特定对象分足够的内存，类对象的变量仅保存该对象在托管堆上的地址。

对于托管堆上的类型，在分配内存和回收内存时都会产生开销。*垃圾回收 (garbage collection)* 是 CLR 的自动内存管理功能，用于执行回收。

##### 2.4.2.2.4 构造函数和初始化

在创建类的实例时，需要确保其字段和属性已初始化为有用的值。可以通过多种方式初始化值：

- 接受默认值
- 字段初始化表达式
- 构造函数参数
- 对象初始值设定项

每个 .NET 类型都有一个默认值。通常，对于数字类型，该值为 0；对于所有引用类型，该值为 `null`。如果默认值在应用中是合理的，则可以依赖于该默认值。

当 .NET 默认值不是想要的值时，可以使用字段初始化表达式设置初始值，例如：

```c#
public class Container
{
    // Initialize capacity field to a default value of 10:
    private int _capacity = 10;
}
```

可以通过定义负责设置初始值的构造函数来要求调用方提供初始值，例如：

```c#
public class Container
{
    private int _capacity;

    public Container(int capacity) => _capacity = capacity;
}
```

##### 2.4.2.2.5 继承

类完全支持 *继承 (inheritance)*，这是面向对象编程的基本特点。在创建类时，可以从其他任何未定义为 `sealed` 的类继承，其他类也可以从该类继承并重写类的虚方法。此外，类可以实现一个或多个接口。

继承是通过使用 *派生 (derivation)* 来实现的，意味着类是通过使用其从中继承数据和行为的 *基类 (base class)* 来声明的，而基类通过在派生的类名称后面追加冒号 `:` 和基类名称来指定。当声明省略基类时，类默认继承自 `object`。

类继承其基类的成员，意味着一个类隐式包含其基类的几乎所有成员，但是类不继承实例、静态构造函数以及终结器。派生类可以在其继承的成员中添加新成员，但无法删除继承成员的定义。

类可以声明为 `abstract`。抽象类包含抽象方法，抽象方法包含签名定义但不包含实现。抽象类不能实例化。只能通过可实现抽象方法的派生类来使用该类。`sealed` 类不允许其他类继承。

类定义可以在不同的源文件之间分割。可在 [Partial Classes and Methods](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods) 了解详细信息。

##### 2.4.2.2.6 类和结构

类和结构的主要相同点有：

- 都可以实现接口。
- 可定义数据成员和函数成员。

类和结构的主要区别有：

- 结构属于值类型，在栈上分配内存；类属于引用类型，在堆上分配内存。
- 结构不支持继承，结构的成员不能使用 `protected` 访问修饰符，而类则没有这个限制。
- 结构在声明成员变量时不能指定初始值，而类则没有这个限制。
- 结构不能声明无参的构造函数，而类则没有这个限制。
- 结构需要在构造函数中初始化所有成员变量，而类则没有这个限制。
- 结构不能声明析构函数，而类则没有这个限制。
- 不能使用 `static` 修饰符声明结构 (不存在静态结构)，但可以使用 `static` 修饰符声明静态类。

#### 2.4.2.3 接口类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/interface

接口定义一个协定，实现该协定的任何 `class`、`record` 或 `struct` 必须提供接口中定义的成员的实现。接口可以为成员定义默认的实现。还可以定义 `static` 成员，以便提供常见功能的单个实现。从 C# 11 开始，接口可以定义 `static abstract` 或 `static virtual` 成员来声明实现类型必须提供声明的成员。通常，`static virtual` 方法声明实现必须定义一组重载运算符。

##### 2.4.2.3.1 概述

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/interfaces

接口包含非抽象的 `class` 或 `struct` 必须实现的一组相关功能的定义。接口可以定义 `static` 方法，这类方法必须具有实现。接口可以为成员定义默认实现。接口不能声明实例数据，如字段、自动实现的属性、类似属性的事件。

可以使用 `interface` 关键字来定义接口，例如：

```c#
interface IEquatable<T>
{
    bool Equals(T obj);
}
```

接口名称必须是有效的 C# 标识符名称。按照约定，接口名称以大写字母 `I` 开头。

实现 `IEquatable<T>` 接口的任何类或结构都必须包含与该接口指定的签名匹配的 `Equals` 方法的定义。因此，可以依靠实现 `IEquatable<T>` 的类来包含 `Equals` 方法。

`IEquatable<T>` 的定义不为 `Equals` 提供实现。类或结构可以实现多个接口，但是类只能从单个类继承。

接口可以包含实例方法、属性、事件和索引器这 4 种成员类型，可以为成员定义默认实现。接口可以包含静态构造函数、字段、常量或运算符。接口可以定义 `static` 方法，但方法必须具有实现。接口不能包含实例字段、实例构造函数或终结器。从 C# 11 开始，非字段接口成员可以是 `static abstract`。

接口成员默认是 `public`，可以显式指定可访问性修饰符 (如 `public`、`protected`、`internal`、`private`、`protected internal` 或 `private protected`)，其中`private` 成员必须有默认实现。

如果要实现接口成员，实现类的对应成员必须是公共、非静态，并且具有与接口成员相同的名称和签名。

需要注意，当接口声明静态成员时，实现该接口的类型也可能声明具有相同签名的静态成员。但是，它们是不同的，并且由声明成员的类型唯一标识。在类型中声明的静态成员不会覆盖接口中声明的静态成员。

实现接口的类或结构必须为所有已声明的成员提供实现，而非接口提供的默认实现。但是，如果基类实现了接口，则从基类派生的任何类都会继承该实现。

接口可以从一个或多个接口继承。派生接口从其基接口继承成员。实现派生接口的类必须实现派生接口中的所有成员，包括派生接口的基接口的所有成员。该类可能会隐式转换为派生接口或其任何基接口。类可能通过它继承的基类或者通过其他接口继承的接口来多次包含某个接口。但是，类只能实现接口一次，并且仅当类将接口作为类定义的一部分 (即 `class ClassName : InterfaceName`) 进行声明时才能提供。如果由于继承实现接口的基类而继承了接口，则基类会提供接口的成员的实现。但是，派生类可以重新实现任何虚拟的接口成员，而不是使用继承的实现。当接口声明了方法的默认实现时，实现该接口的任何类都会继承该实现，但是需要将类实例强制转换为接口类型，才能访问接口成员上的默认实现。

基类还可以使用虚拟成员实现接口成员。在这种情况下，派生类可以通过重写虚拟成员来更改接口行为。

总的来说，接口具有以下属性：

- 在 8.0 以前的 C# 版本中，接口类似于只有抽象成员的抽象基类。实现接口的类或结构必须实现其所有成员。
- 从 C# 8.0 开始，接口可以定义其部分或全部成员的默认实现。实现接口的类或结构不一定要实现具有默认实现的成员。
- 接口无法直接进行实例化。其成员由实现接口的任何类或结构来实现。
- 一个类或结构可以实现多个接口。一个类可以继承一个基类，还可实现一个或多个接口。

##### 2.4.2.3.2 默认接口成员

接口成员可以声明主体。接口中的成员主体是默认实现。具有主体的成员允许接口为不提供重写实现的类和结构提供默认的实现。

接口可以包括：

- 方法、属性、索引器、事件 (这些成员可以没有任何实现的签名)
- 常量
- 运算符
- 静态构造函数
- 嵌套类型
- 静态字段
- 使用显式接口实现语法的成员声明
- 显式访问修饰符 (默认访问权限为 `public`)

##### 2.4.2.3.3 接口继承

接口不能包含实例状态。虽然现在允许使用静态字段，但接口中不允许使用实例字段。接口中不支持自动实现的属性，因为它们将隐式声明隐藏的字段。此规则对属性声明有细微影响。在接口声明中，下面的示例代码不会像在 `class` 或 `struct` 中一样声明自动实现的属性，而是会声明一个属性，该属性没有默认实现，必须在该实现接口的任何类型中实现它：

```c#
public interface INamed
{
  public string Name {get; set;}
}
```

一个接口可以从一个或多个基接口继承。当接口重写基接口中的方法实现时，必须使用 [显式接口实现](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/explicit-interface-implementation) 语法。

当基类型列表包含基类和接口时，基类必须是列表中的第 1 项。

实现接口的类可以显式实现该接口的成员。显式实现的成员不能通过类实例访问，而只能通过接口实例访问。此外，只能通过接口实例访问默认接口成员。

#### 2.4.2.4 委托类型

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/

委托是一种引用类型，表示对具有特定参数列表和返回类型的方法的引用。在实例化委托时，可以将其实例与任何具有兼容签名和返回类型的方法相关联。可以通过委托实例调用方法。

通过委托，可以将方法视为可分配给变量并可作为参数传递的实体。委托可用于将方法作为参数传递给其他方法。事件处理程序就是通过委托调用的方法。可以创建一个自定义方法，当发生特定事件时，某个类 (如 Windows 控件) 就可以调用该方法。下面代码示例是一个委托声明：

```c#
public delegate int PerformCalculation(int x, int y);
```

可以将任何可访问的类或结构中与委托类型匹配的方法分配给委托。该方法可以是静态方法，也可以是实例方法。这种灵活性意味着可以通过编程方式来更改方法调用，还可以向现有类中插入新代码。

需要注意，在方法重载的上下文中，方法的签名不包括返回值。但在委托的上下文中，签名包括返回值。换句话说，方法和委托必须具有相同的返回类型。

这种将方法作为参数引用的能力使委托成为定义回调方法的理想选择。可以编写一个方法来比较程序中的两个对象，该方法可用于排序算法的委托中。由于比较代码与库是分开的，因此排序方法可以更加通用。

[函数指针](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-9.0/function-pointers) 支持类似的场景，但需要对调用约定进行更多控制。使用添加到委托类型的虚方法调用与委托关联的代码。使用函数指针，可以指定不同的约定。

总的来说，委托具有以下属性：

- 委托类似于 C++ 函数指针，但委托是完全面向对象的，与 C++ 指向成员函数的指针不同，委托会同时封装对象实例和方法。
- 委托允许将方法作为参数进行传递。
- 委托可用于定义回调方法。
- 委托可以链接在一起。例如，可以对一个事件调用多个方法。
- 方法不必与委托类型完全匹配。可在 [使用委托中的变体](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/using-variance-in-delegates) 了解详细信息。
- 使用 Lambda 表达式可以更简练地编写内联代码块。Lambda 表达式 (在某些上下文中) 可编译为委托类型。

#### 2.4.2.5 数组类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/arrays

可以将同一类型的多个变量存储在一个数组数据结构中。通过指定数组的元素类型来声明数组。如果希望数组存储任意类型的元素，可将其类型指定为 `object`。在 C# 的统一类型系统中，所有类型 (预定义类型、用户定义类型、引用类型和值类型) 都是直接或间接从 `Object` 继承的。

所有数组都是引用类型，即使元素是值类型也不例外。

数组类型是通过在类型名称后面添加方括号构造而成。例如：`int[]` 是 `int` 类型的一维数组，`int[,]` 是 `int` 类型的二维数组，`int[][]` 是由 `int` 类型的一维数组或 "交错" 数组构成的一维数组。

数组具有以下属性：

- 数组可以是一维、多维或交错的。
- 在声明数组变量时设置维度数。创建数组实例时，将建立每个维度的长度。这些值在实例的生命周期内无法更改。
- 数值数组元素的默认值设置为 0，而引用元素设置为 `null`。
- 交错数组是数组的数组，因此其元素为引用类型且被初始化为 `null`。
- 数组从 0 开始编制索引：包含 `n` 元素的数组从 `0` 索引到 `n-1`。
- 数组元素可以是任何类型，其中包括数组类型。
- 数组类型是从抽象的基类型 `System.Array` 派生的引用类型。所有数组都会实现 [`IList`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.ilist) 和 [`IEnumerable`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.ienumerable)。可以使用 `foreach` 语句循环访问数组。单维数组还实现了 [`IList<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ilist-1) 和 [`IEnumerable<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1)。

需要注意，可以使用 [集合表达式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/collection-expressions) (方括号) 来初始化数组。集合表达式最初在 C# 12 中引入，该版本随 .NET 8 发布。如果未升级到 C# 12，应改用 `{` 和 `}` 来初始化数组。

#### 2.4.2.6 可为 null 的引用类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-reference-types

可为 null 的引用类型在已选择加入 *可为 null 的感知上下文 (nullable aware context)* 的代码中可用。可为 null 的引用类型、null 静态分析警告和 [null 包容运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-forgiving) 是可选的语言功能。默认情况下，所有功能都是关闭的。*可为 null 的上下文 (nullable context)* 在项目级别中使用生成设置进行控制，或者在代码中使用 pragmas 进行控制。

在可为 null 的感知上下文中：

- 引用类型 `T` 的变量必须用非 null 值进行初始化，并且不能为其分配可能为 `null` 的值。
- 引用类型 `T?` 的变量可以用 `null` 进行初始化，也可以分配 `null`，但在取消引用之前必须对 `null` 进行检查。
- 类型为 `T?` 的变量 `m` 在应用 null 包容运算符时被认为是非空的，例如 `m!`。

不可为 null 的引用类型 `T` 和可为 null 的引用类型 `T?` 之间的区别按照编译器对上述规则的解释强制执行的。类型为 `T` 的变量和类型为 `T?` 的变量由相同的 .NET 类型表示。这种区别由编译器和某些库读取的元数据体现：当可为 null 的引用在没有先对照 `null` 检查其值的情况下取消引用时，编译器会发出警告；当不可为 null 的引用分配了可能为 `null` 的值时，编译器也会发出警告。

因为不可为 null 的类型和可为 null 的类型都存储为相同的类型，所以有几个位置不允许使用可为 null 的引用类型。通常，可为 null 的引用类型不能用作基类或实现的接口。可为 null 的引用类型不能用于任何对象创建或类型测试表达式。可为 null 的引用类型不能是成员访问表达式的类型。如下面示例所示：

```c#
public MyClass : System.Object? // not allowed
{
}

var nullEmpty = System.String?.Empty; // Not allowed
var maybeObject = new object?(); // Not allowed
try
{
    if (thing is string? nullableString) // not allowed
        Console.WriteLine(nullableString);
} catch (Exception? e) // Not Allowed
{
    Console.WriteLine("error");
}
```

##### 2.4.2.6.1 可为 null 的引用和静态分析

可为 null 的引用类型不是新的类型，而是对现有引用类型的注释。编译器使用这些注释来帮助查找代码中潜在的 null 引用错误。不可为 null 的引用类型和可为 null 的引用类型在运行时没有区别。编译器不会为不可为 null 的引用类型添加任何运行时检查。

在可为 null 的上下文中，编译器对任何引用类型的变量 (可为 null 的和不可为 null 的) 执行静态分析。编译器会跟踪每个引用变量的 *null-state*，即 *not-null* 或者 *maybe-null*。不可为 null 的引用的默认状态为 *not-null*。可为 null 的引用的默认状态为 *maybe-null*。

不可为 null 的引用类型在取消引用时应该始终是安全的，因为它们的 *null-state* 是 *not-null*。如果强制执行该规则，如果不可为 null 的引用类型没有初始化为非 null 值，编译器将发出警告。必须在声明的位置分配局部变量。必须在字段初始化表达式或每个构造函数中为每个字段分配 *not-null* 值。如果将不可为 null 的引用分配给状态为 *maybe-null* 的引用，编译器会发出警告。一般情况下，不可为 null 的引用的状态为 *not-null*，在取消引用这些变量时不会发出警告。

注意，如果将 *maybe-null* 表达式分配给不可为 null 的引用类型，编译器会生成警告。然后，编译器会针对该变量生成警告，直到将该变量分配给 *not-null* 表达式。

可为 null 的引用类型可以进行初始化或分配给 `null`。因此，静态分析必须在取消对变量的引用之前确定该变量的状态为 *not-null*。如果可为 null 的引用的状态确定为 *maybe-null*，将其分配给不可为 null 的引用变量会生成编译器警告。下面是编译器警告的示例：

```c#
public class ProductDescription
{
    private string shortDescription;
    private string? detailedDescription;

    public ProductDescription() // Warning! shortDescription not initialized.
    {
    }

    public ProductDescription(string productDescription) =>
        this.shortDescription = productDescription;

    public void SetDescriptions(string productDescription, string? details=null)
    {
        shortDescription = productDescription;
        detailedDescription = details;
    }

    public string GetDescription()
    {
        if (detailedDescription.Length == 0) // Warning! dereference possible null
        {
            return shortDescription;
        }
        else
        {
            return $"{shortDescription}\n{detailedDescription}";
        }
    }

    public string FullDescription()
    {
        if (detailedDescription == null)
        {
            return shortDescription;
        }
        else if (detailedDescription.Length > 0) // OK, detailedDescription can't be null.
        {
            return $"{shortDescription}\n{detailedDescription}";
        }
        return shortDescription;
    }
}
```

下面代码显示了编译器在使用此类时发出警告的位置：

```c#
string shortDescription = default; // Warning! non-nullable set to null;
var product = new ProductDescription(shortDescription); // Warning! static analysis knows shortDescription maybe null.

string description = "widget";
var item = new ProductDescription(description);

item.SetDescriptions(description, "These widgets will do everything.");
```

上面的示例展示了编译器的静态分析如何确定引用变量的 *null-state*。编译器对 null 检查和分配应用语言规则以通知其分析。编译器无法对方法或属性的语义进行假设。如果调用执行 null 检查的方法，则编译器无法得知这些方法会影响变量的 *null-state*。可以将特性添加到 API，以告知编译器有关参数和返回值的语义。这些特性已应用于 .NET Core 库中的许多常见 API。例如，[`String.IsNullOrEmpty`](https://learn.microsoft.com/en-us/dotnet/api/system.string.isnullorempty) 方法已经更新，编译器正确地将该方法解释为 null 检查。可在 [Nullable attributes](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/attributes/nullable-analysis) 了解更多信息。

##### 2.4.2.6.2 设置可为 null 的上下文

可以通过两种方式控制可为 null 的上下文：

- 在项目级别，可以添加 `<Nullable>enable</Nullable>` 项目设置。
- 在单个 C# 源文件中，可以添加 `#nullable enable` 来启用可为 null 的上下文。

可在 [setting a nullable strategy](https://learn.microsoft.com/en-us/dotnet/csharp/nullable-migration-strategies) 了解更多信息。

 在 .NET 6 之前，新项目使用默认值 `<Nullable>disable</Nullable>`。从 .NET 6 开始，新项目将在项目文件中包含 `<Nullable>enable</Nullable>` 元素。



## 2.5 记录类型

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/records

C# 中的记录是一个类或结构，它提供用于处理数据模型的特殊语法和行为。`record` 修饰符指示编译器合成对主要作用是存储数据的类型有用的成员，这些成员包括 `ToString()` 的重载和支持值相等的成员。



### 2.5.1 记录的用途

在以下情况中，应考虑使用记录而不是类或结构：

- 需要定义一个依赖于 [值相等性](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons#value-equality) 的数据模型。
- 需要定义一个对象不可变的类型。

#### 2.5.1.1 值相等性

对记录来说，值相等性是指如果记录类型的两个变量的类型相匹配，且所有属性和字段值都相同，那么记录类型的两个变量是相等的。对于其他引用类型 (例如类)，相等性默认指 [引用相等性](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons#reference-equality)，[除非执行了值相等性](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/how-to-define-value-equality-for-a-type)。也就是说，如果类的两个变量引用同一个对象，则这两个变量是相等的。确定两个记录实例的相等性的方法和运算符使用值相等性。

并非所有数据模型都适合使用值相等性。例如，[Entity Framework Core](https://learn.microsoft.com/en-us/ef/core/) 依赖引用相等性，来确保它对概念上是一个实体的实体类型只使用一个实例。因此，记录类型不适合用作 Entity Framework Core 中的实体类型。

#### 2.5.1.2 不可变性

不可变类型会阻止在对象实例化后更改该对象的任何属性或字段值。如果需要一个类型是线程安全的，或者需要哈希代码在哈希表中能保持不变，那么不可变性很有用。记录为创建和使用不可变类型提供了简洁的语法。

不可变性并不适用于所有数据方案。例如，Entity Framework Core 不支持通过不可变实体类型进行更新。



### 2.5.2 记录与类和结构的区别

声明和实例化类或结构时使用的语法与操作记录时的相同。只是将 `class` 关键字替换为 `record`，或者使用 `record struct` 而不是 `struct`。同样地，记录类支持相同的表示继承关系的语法。

记录与类的区别如下所示：

- 可以在 [主构造函数](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/instance-constructors#primary-constructors) 中使用 [位置参数](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/record#positional-syntax-for-property-definition) 来创建和实例化具有不可变属性的类型。
- 在类中指示引用相等性或不相等的方法和运算符 (例如 `Object.Equals(Object)` 和 `==`) 在记录中指示 [值相等或不相等](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/record#value-equality)。
- 可以使用 [`with` 表达式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/record#nondestructive-mutation) 对不可变对象创建在所选属性中具有新值的副本。
- 记录的 `ToString` 方法会创建一个格式字符串，它显示对象的类型名称及其所有公共属性的名称和值。
- 记录可以 [从另一个记录继承](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/record#inheritance)。但记录不可以从类继承，类也不可以从记录继承。

`record struct` 和 `struct` 的不同之处是，编译器合成了方法来确定相等性和 `ToString`。编译器为位置记录结构合成 `Deconstruct` 方法。

编译器为 `record class` 中的每个主构造函数参数合成一个公共的 init-only 属性。在 `record struct` 中，编译器合成公共的读写属性。编译器不会在不包含 `record` 修饰符的 `class` 和 `struct` 类型中创建主构造函数参数的属性。

另外，可在 [Records](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/record) 了解关于记录的更多信息。



## 2.6 匿名类型

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/anonymous-types

匿名类型提供了一种方便的方法，可用来将一组只读属性封装到单个对象中，而无需首先显式定义一个类型。类型名由编译器生成，并且不能在源代码级使用。每个属性的类型由编译器推断。

可以结合使用 `new` 运算符和对象初始值设定项创建匿名类型。

下面代码示例是使用两个名为 `Amount` 和 `Message` 的属性进行初始化的匿名类型：

```c#
var v = new { Amount = 108, Message = "Hello" };

// Rest the mouse pointer over v.Amount and v.Message in the following
// statement to verify that their inferred types are int and string.
Console.WriteLine(v.Amount + v.Message);
```

匿名类型通常用于查询表达式的 [`select`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/select-clause) 子句中，以便返回源序列中每个对象的属性子集。

匿名类型包含一个或多个公共的只读属性。不能包含其他种类的类成员 (如方法或事件)。用来初始化属性的表达式不能为 `null`、匿名函数或指针类型。

还可以按另一种类型 (类、结构或另一个匿名类型) 的对象定义字段，这是通过使用保存此对象的变量来完成的。在下面的示例中，两个匿名类型是使用已实例化的用户定义类型创建的。在这两种情况下，匿名类型 `shipment` 和 `shipmentWithBonus` 中的 `product` 字段的类型均为 `Product`，其中包含每个字段的默认值。`bonus` 字段将是编译器创建的匿名类型。

```c#
var product = new Product();
var bonus = new { note = "You won!" };
var shipment = new { address = "Nowhere St.", product };
var shipmentWithBonus = new { address = "Somewhere St.", product, bonus };
```

通常，当使用匿名类型来初始化变量时，可以通过使用 `var` 将变量作为隐式键入的局部变量来进行声明。类型名称无法在变量声明中给出，因为只有编译器能访问匿名类型的基础名称。

可以通过将隐式键入的局部变量与隐式键入的数组相结合创建匿名键入的元素的数组。例如：

```c#
var anonArray = new[] { new { name = "apple", diam = 4 }, new { name = "grape", diam = 1 }};
```

匿名类型是 `class` 类型，其直接派生自 `object`，并且无法强制转换为除 `object` 外的任何类型。虽然应用程序不能访问它，编译器还是提供了每一个匿名类型的名称。从公共语言运行时的角度来看，匿名类型与任何其他引用类型没有什么不同。

如果程序集中的两个或多个匿名对象初始值指定了属性序列，这些属性采用相同顺序且具有相同的名称和类型，则编译器将对象视为相同类型的实例，它们共享同一编译器生成的类型信息。

无法将字段、属性、时间或方法的返回类型声明为具有匿名类型。同样，也不能将方法、属性、构造函数或索引器的形参声明为具有匿名类型。如果要将匿名类型或包含匿名类型的集合作为参数传递给某一方法，可以将参数作为类型 `object` 进行声明。但是，对匿名类型使用 `object` 违背了强类型的目的。如果必须存储查询结果或者必须将查询结果传递到方法边界外部，应考虑使用普通的命名结构或类而不是匿名类型。

由于匿名类型上的 `Equals` 和 `GetHashCode` 方法是根据方法属性的 `Equals` 和 `GetHashCode` 定义的，因此仅当同一匿名类型的两个实例的所有属性都相等时，这两个实例才相等。

需要注意，匿名类型的访问修饰符为 `internal`，因此在不同程序集中定义的两种匿名类型并非同一类型。因此，当在不同的程序集中进行定义时，匿名类型的实例不能彼此相等，即使其所有属性都相等。

匿名类型会重写 `ToString` 方法，将用大括号括起来的每个属性的名称和 `ToString` 输出连接起来。例如：

```c#
var v = new { Title = "Hello", Age = 24 };

Console.WriteLine(v.ToString()); // "{ Title = Hello, Age = 24 }"
```



## 2.7 泛型

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/generics

**泛型 (Generics)** 将 **类型参数 (type parameters)** 的概念引入 .NET，这样就可设计具有以下特征的类和方法：在客户端代码声明并初始化这些类或方法之前，这些类或方法会延迟指定一个或多个类型。通过使用泛型类型参数，可以编写其他客户端代码能够使用的单个类，而不会产生运行时转换或装箱操作的成本或风险。

泛型类和泛型方法兼具可重用性、类型安全性和效率，这是非泛型类和非泛型方法无法实现的。在编译过程中将泛型类型参数替换为类型参数。泛型通常与集合以及作用于集合的方法一起使用。System.Collections.Generic 命名空间包含几个基于泛型的集合类。不建议使用非泛型集合 (如 [`ArrayList`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.arraylist))，只是出于兼容性目的而维护非泛型集合。可在 [.NET 中的泛型](https://learn.microsoft.com/en-us/dotnet/standard/generics/) 了解详细信息。

对于泛型有以下几点概述：

- 使用泛型类型可以最大限度地重用代码、保护类型安全性以及提高性能。
- 泛型最常见的用途是创建集合类。
- .NET 类库在 System.Collections.Generic 命名空间中包含几个新的泛型集合类，应尽可能使用泛型集合来代替某些类，如 System.Collections 命名空间中的 `ArrayList`。
- 可以创建自己的泛型接口、泛型类、泛型方法、泛型事件、泛型委托。
- 可以对泛型类进行约束以访问特定数据类型的方法。
- 可以使用反射在运行时获取有关泛型数据类型中使用的类型的信息。



### 2.7.1 类型参数的约束

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/constraints-on-type-parameters

约束用于告知编译器类型参数必须具备的功能。在没有任何约束的情况下，类型参数可以是任何类型，编译器只能假定类型参数派生自 `System.Object`。如果客户端代码使用不满足约束的类型，编译器会报告错误。

通过使用 `where` 上下文关键字来指定约束，下表列出了各种类型的约束：

| 约束                               | 描述                                                         |
| :--------------------------------- | :----------------------------------------------------------- |
| `where T : struct`                 | 类型参数必须是不可为 null 的值类型，其中包含 `record struct` 类型。由于所有值类型都具有可访问的无参数构造函数 (无论是声明的还是隐式的)，因此 `struct` 约束表示 `new()` 约束，并且不能与 `new()` 约束结合使用。`struct` 约束也不能与 `unmanaged` 约束结合使用。 |
| `where T : class`                  | 类型参数必须是引用类型。此约束还应用于任何类、接口、委托或数组类型。在可为 null 的上下文中，`T` 必须是不可为 null 的引用类型。 |
| `where T : class?`                 | 类型参数必须是可为 null 或不可为 null 的引用类型。此约束还应用于任何类、接口、委托或数组类型 (包括记录)。 |
| `where T : notnull`                | 类型参数必须是不可为 null 的类型。参数可以是不可为 null 的引用类型，也可以是不可为 null 的值类型。 |
| `where T : unmanaged`              | 类型参数必须是不可为 null 的 [非托管类型](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/unmanaged-types)。`unmanaged` 约束表示 `struct` 约束，且不能与 `struct` 约束或 `new()` 约束结合使用。 |
| `where T : new()`                  | 类型参数必须具有公共无参数构造函数。与其他约束一起使用时，`new()` 约束必须最后指定。`new()` 约束不能与 `struct` 和 `unmanaged` 约束结合使用。 |
| `where T :` *`<base class name>`*  | 类型参数必须是指定的基类或派生自指定的基类。在可为 null 的上下文中，`T` 必须是从指定基类派生的不可为 null 的引用类型。 |
| `where T :` *`<base class name>?`* | 类型参数必须是指定的基类或派生自指定的基类。在可为 null 的上下文中，`T` 可以是从指定基类派生的可为 null 或不可为 null 的类型。 |
| `where T :` *`<interface name>`*   | 类型参数必须是指定的接口或实现指定的接口。可指定多个接口约束。约束接口也可以是泛型。在可为 null 的上下文中，`T` 必须是实现指定接口的不可为 null 的类型。 |
| `where T :` *`<interface name>?`*  | 类型参数必须是指定的接口或实现指定的接口。可指定多个接口约束。约束接口也可以是泛型。在可为 null 的上下文中，`T` 可以是可为 null 的引用类型、不可为 null 的引用类型或值类型。`T` 不能是可为 null 的值类型。 |
| `where T : U`                      | 为 `T` 提供的类型参数必须是为 `U` 提供的参数或派生自为 `U` 提供的参数。在可为 null 的上下文中，如果 `U` 是不可为 null 的引用类型，`T` 必须是不可为 null 的引用类型。如果 `U` 是可为 null 的引用类型，则 `T` 可以是可为 null 的引用类型，也可以是不可为 null 的引用类型。 |
| `where T : default`                | 重写方法或提供显式接口实现时，如果需要指定不受约束的类型参数，此约束可解决歧义。`default` 约束表示基方法，但不包含 `class` 或 `struct` 约束。 |

某些约束是互斥的，而某些约束必须按指定顺序排列：

- 最多可应用 `struct`、`class`、`class?`、`notnull` 和 `unmanaged` 约束中的一个。如果提供这些约束中的任意一个，则它必须是为该类型参数指定的第一个约束。
- 基类约束 (`where T : Base` 或 `where T : Base?`) 不能与 `struct`、`class`、`class?`、`notnull` 或 `unmanaged` 约束中的任何一个组合。
- 无论哪种形式，都最多只能应用一个基类约束。如果想要支持可为 null 的基类型，应使用 `Base?`。
- 不能将接口不可为 null 和可为 null 的形式命名为约束。
- `new()` 约束不能与 `struct` 或 `unmanaged` 约束结合使用。如果指定了 `new()` 约束，则它必须是该类型参数的最后一个约束。
- `default` 约束只能应用于替代或显式接口实现。它不能与 `struct` 或 `class` 约束结合使用。

#### 2.7.1.1 使用约束的原因

约束指定了类型参数的功能和预期。声明这些约束意味着可以使用约束类型的操作和方法调用。如果泛型类或方法对泛型成员使用除简单赋值之外的任何操作，又或者调用 `System.Object` 不支持的任何方法，则应该对类型参数使用约束。例如：基类约束告诉编译器，仅此类型的对象或派生自此类型的对象可用作类型参数。编译器有了此保证后，就能够允许在泛型类中调用该类型的方法。

可以对同一类型参数应用多个约束，并且约束自身可以是泛型类型，例如：

```c#
class EmployeeList<T> where T : Employee, System.Collections.Generic.IList<T>, IDisposable, new()
{
    // ...
}
```

当应用 `where T : class` 约束时，应避免对类型参数使用 `==` 和 `!=` 运算符，因为这些运算符仅测试引用标识而不测试值相等性。即使在用作参数的类型中重载这些运算符也会发生此行为。例如，在下面的示例中，即使 `string` 类重载了 `==` 运算符，输出也为 false：

```c#
public static void OpEqualsTest<T>(T s, T t) where T : class
{
    System.Console.WriteLine(s == t);
}

private static void TestStringEquality()
{
    string s1 = "target";
    System.Text.StringBuilder sb = new System.Text.StringBuilder("target");
    string s2 = sb.ToString();
    OpEqualsTest<string>(s1, s2);
}
```

编译器只知道 `T` 在编译时是引用类型，并且必须使用对所有引用类型都有效的默认运算符。如果必须测试值相等性，应使用 `where T : IEquatable<T>` 或 `where T : IComparable<T>` 约束，并在用于构造泛型类的任何类中实现该接口。

#### 2.7.1.2 约束多个参数

可以对多个参数应用多个约束，对一个参数应用多个约束，例如：

```c#
class Base { }
class Test<T, U>
    where U : struct
    where T : Base, new()
{ }
```

#### 2.7.1.3 未绑定的类型参数

没有约束的类型参数 (如公共类 `SampleClass<T>{}` 中的 `T`) 称为未绑定的类型参数。

未绑定的类型参数具有以下规则：

- 不能使用 `!=` 和 `==` 运算符，因为无法保证具体的类型参数能支持这些运算符。
- 可以在它们与 `System.Object` 之间来回转换，或者将它们显式转换为任何接口类型。
- 可以将它们与 `null` 进行比较。将未绑定的参数与 `null` 进行比较时，如果类型参数为值类型，则该比较始终返回 `false`。

#### 2.7.1.4 类型参数作为约束

在具有自己类型参数的成员函数必须将该参数约束为包含类型的类型参数时，将泛型类型参数用作约束非常有用，例如：

```c#
public class List<T>
{
    public void Add<U>(List<U> items) where U : T {/*...*/}
}
```

在上面的示例中，`T` 在 `Add` 方法的上下文中是一个类型约束，而在 `List` 类的上下文中是一个未绑定的类型参数。

类型参数还可以在泛型类定义中用作约束。必须在尖括号中声明该类型参数以及任何其他类型参数：

```c#
//Type parameter V is used as a type constraint.
public class SampleClass<T, U, V> where T : V { }
```

类型参数作为泛型类的约束的作用非常有限，因为编译器除了假设类型参数派生自 `System.Object` 以外，不会做其他任何假设。如果要在两个类型参数之间强制继承关系，可以将类型参数用作泛型类的约束。

#### 2.7.1.5 notnull 约束

可以使用 `notnull` 约束指定类型参数必须是不可为 null 的值类型或不可为 null 的引用类型。与其他大多数约束不同，如果类型参数违反 `notnull` 约束，编译器会生成警告而不是错误。

`notnull` 约束仅在可为 null 上下文中使用时才有效。如果在过时的可为 null 上下文中添加 `notnull` 约束，编译器不会针对违反约束的情况生成任何警告或错误。

#### 2.7.1.6 class 约束

可为 null 的上下文中的 `class` 约束指定类型参数必须是不可为 null 的引用类型。在可为 null 上下文中，当类型参数是可为 null 的引用类型时，编译器会生成警告。

#### 2.7.1.7 default 约束

添加可为 null 引用类型会使泛型类型或方法中的 `T?` 使用复杂化。`T?` 可以与 `struct` 或 `class` 约束一起使用，但必须存在其中一项。使用 `class` 约束时，`T?` 引用了 `T` 的可为空引用类型。可在这两个约束均未应用时使用 `T?`。在这种情况下，对于值类型和引用类型，`T?` 解读为 `T?`。但是，如果 `T` 是 `Nullable<T>` 的实例，则 `T?` 与 `T` 相同。换句话说，它不会成为 `T??`。

由于现在可在没有 `class` 或 `struct` 约束的情况下使用 `T?`，因此在重写或显式接口实现中可能会出现歧义。在这两种情况下，重写不包含约束，但从基类继承。当基类不应用 `class` 或 `struct` 约束时，派生类需要通过某种方式在不使用任一种约束的情况下指定应用于基方法的重写。派生方法应用 `default` 约束。`default` 约束不阐明 `class` 和 `struct` 约束。

#### 2.7.1.8 非托管约束

可使用 `unmanaged` 约束来指定类型参数必须是不可为 null 的非托管类型。通过 `unmanaged` 约束，可编写可重用的例程，从而使用可作为内存块操作的类型。例如：

```c#
unsafe public static byte[] ToByteArray<T>(this T argument) where T : unmanaged
{
    var size = sizeof(T);
    var result = new Byte[size];
    Byte* p = (byte*)&argument;
    for (var i = 0; i < size; i++)
        result[i] = *p++;
    return result;
}
```

上例必须在 `unsafe` 上下文中编译，因为它并不是在已知的内置类型上使用 `sizeof` 运算符。如果没有 `unmanaged` 约束，则 `sizeof` 运算符不可用。

`unmanaged` 约束表示 `struct` 约束，且不能与其结合使用。因为 `struct` 约束表示 `new()` 约束，且 `unmanaged` 约束也不能与 `new()` 约束结合使用。

#### 2.7.1.9 委托约束

可以使用 [`System.Delegate`](https://learn.microsoft.com/en-us/dotnet/api/system.delegate) 或者 [`System.MulticastDelegate`](https://learn.microsoft.com/en-us/dotnet/api/system.multicastdelegate) 作为基类约束。CLR 始终允许该约束，但 C# 语言不允许。使用 `System.Delegate` 约束，用户能够以类型安全的方式编写使用委托的代码。

下面代码定义了合并两个同类型委托的扩展方法：

```c#
public static TDelegate? TypeSafeCombine<TDelegate>(this TDelegate source, TDelegate target)
    where TDelegate : System.Delegate
    => Delegate.Combine(source, target) as TDelegate;
```

可使用上面的方法来合并相同类型的委托：

```c#
Action first = () => Console.WriteLine("this");
Action second = () => Console.WriteLine("that");

var combined = first.TypeSafeCombine(second);
combined!();

Func<bool> test = () => true;
// Combine signature ensures combined delegates must
// have the same type.
//var badCombined = first.TypeSafeCombine(test);
```

如果取消注释最后一行，代码将不会编译。`first` 和 `test` 均为委托类型，但它们是不同的委托类型。

#### 2.7.1.10 枚举约束

还可以指定 `System.Enum` 类型作为基类约束。CLR 始终允许此约束，但 C# 语言不允许。使用 `System.Enum` 的泛型提供类型安全的编程，缓存使用 `System.Enum` 中静态方法的结果。

以下示例查找枚举类型的所有有效的值，然后生成将这些值映射到其字符串表示形式的字典。

```c#
public static Dictionary<int, string> EnumNamedValues<T>() where T : System.Enum
{
    var result = new Dictionary<int, string>();
    var values = Enum.GetValues(typeof(T));

    foreach (int item in values)
        result.Add(item, Enum.GetName(typeof(T), item)!);
    return result;
}
```

`Enum.GetValues` 和 `Enum.GetName` 使用反射，这会对性能产生影响。可调用 `EnumNamedValues` 来生成可缓存和重用的集合，而不是重复执行需要反射才能实施的调用。

如以下示例所示，可使用它来创建枚举并生成其值和名称的字典：

```c#
enum Rainbow
{
    Red,
    Orange,
    Yellow,
    Green,
    Blue,
    Indigo,
    Violet
}
```

```c#
var map = EnumNamedValues<Rainbow>();

foreach (var pair in map)
    Console.WriteLine($"{pair.Key}:\t{pair.Value}");
```

#### 2.7.1.11 类型参数实现声明的接口

某些场景要求为类型参数提供的参数实现该接口。例如：

```c#
public interface IAdditionSubtraction<T> where T : IAdditionSubtraction<T>
{
    static abstract T operator +(T left, T right);
    static abstract T operator -(T left, T right);
}
```

此模式使 C# 编译器能够确定重载运算符或任何 `static virtual` 或 `static abstract` 方法的包含类型。它提供的语法使得可以在包含类型上定义加法和减法运算符。如果没有此约束，需要将参数和自变量声明为接口，而不是类型参数：

```c#
public interface IAdditionSubtraction<T> where T : IAdditionSubtraction<T>
{
    static abstract IAdditionSubtraction<T> operator +(
        IAdditionSubtraction<T> left,
        IAdditionSubtraction<T> right);

    static abstract IAdditionSubtraction<T> operator -(
        IAdditionSubtraction<T> left,
        IAdditionSubtraction<T> right);
}
```

上述语法要求实现者对这些方法使用显式接口实现。提供额外的约束使接口能够根据类型参数来定义运算符。实现接口的类型可以隐式实现接口方法。



### 2.7.2 运行时中的泛型

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/generics-in-the-run-time

泛型类型或方法编译为 **公共中间语言 (Common Intermediate Language, CIL)** 时，它包含了将其标识为具有类型参数的元数据。泛型类型的 CIL 使用方式，会根据所提供的类型参数是值类型还是引用类型而有所不同。

首次使用值类型作为参数构造泛型类型时，运行时会使用提供的参数创建一个专用泛型类型，并在 CIL 中的适当位置替换提供的参数。专用化泛型类型会针对用作参数的每个唯一值类型创建一次。

例如，假设程序代码声明了一个由整数构造的堆栈：

```c#
Stack<int>? stack;
```

此时，运行时生成一个专用版的 `Stack<T>` 类，其中用整数相应地替换其参数。现在，每当程序代码使用整数堆栈时，运行时都重新使用已生成的专用 `Stack<T>` 类。例如，在下面的示例中创建了两个整数堆栈实例，且它们共用 `Stack<int>` 代码的一个实例：

```c#
Stack<int> stackOne = new Stack<int>();
Stack<int> stackTwo = new Stack<int>();
```

但是，如果在代码中另一点上再创建一个将不同值类型 (例如 `long` 或用户定义的结构) 作为参数的 `Stack<T>` 类。其结果是，运行时在 CIL 中生成另一个版本的泛型类型并在适当位置替换 `long`。不需要转换，因为每个专用化泛型类本身就包含值类型。

对于引用类型，泛型的工作方式有些不同。首次使用任意引用类型构造泛型类型时，运行时会创建一个专用的泛型类型，其中用对象引用替换了 CIL 中的参数。之后，每次使用引用类型作为参数实例化已构造的类型时，无论使用的是什么类型，运行时都会重新使用先前创建的专用版泛型类型。这是有可能的，因为所有引用的大小都相同。

例如，假设现在有两个引用类型，分别是 `Customer` 类和 `Order` 类，并假设已创建 `Customer` 类型的堆栈：

```c#
class Customer { }
class Order { }
```

```c#
Stack<Customer> customers;
```

此时，运行时生成一个专用版本的 `Stack<T>` 类，其存储的是之后用于填充的引用类型，而不是存储数据。现在假设下一行代码创建另外一种引用类型的堆栈，名称为 `Order`：

```c#
Stack<Order> orders = new Stack<Order>();
```

与值类型不同，不会为 `Order` 类型创建 `Stack<T>` 类的另一个专用版本，而是会创建专用版本的 `Stack<T>` 类的实例，并将 `orders` 变量设置为引用该实例。假设后续代码创建了 `Customer` 类型堆栈：

```c#
customers = new Stack<Customer>();
```

与之前使用 `Order` 类型创建的 `Stack<T>` 类一样，将创建专用版本的 `Stack<T>` 类的另一个实例，其中包含的指针被设置为引用 `Customer` 类型大小的内存区。由于引用类型的数量因程序不同而有较大差异，因此通过将编译器为引用类型的泛型类创建的专用类的数量减少至 1，泛型的 C# 实现可极大减少代码量。

此外，使用值类型或引用类型参数实例化泛型 C# 类时，反射可在运行时对其进行查询，并且可以确定其实际类型和类型参数。

# 3. 程序构建基块

> https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/program-building-blocks
>

`class`、`struct`、`interface`、`enum`、`nullable`、`tuple` 这些类型都是由以下构建基块生成的：

- **成员 (Members)**
- **表达式 (Expressions)**
- **语句 (Statements)**



## 3.1 成员

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/members

类和结构具有表示其数据和行为的成员。类的成员包括在类中声明的所有成员，以及在该类的继承层次结构中的所有类中声明的所有成员 (构造函数和析构函数除外)。基类中的私有成员被继承，但不能从派生类访问。

以下是类或结构中可以包含的成员类型：

- **[字段 (Fields)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/fields)**：字段是在类范围声明的变量。字段可以是内置数值类型或者其他类的实例。
- **[常量 (Constants)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/constants)**：常量是在编译时设置其值并且不能更改其值的字段。
- **[属性 (Properties)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/properties)**：属性是类中可以像类中的字段一样访问的方法。属性可以为类字段提供保护，以避免字段在对象不知道的情况下被更改。
- **[方法 (Methods)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/methods)**：方法定义类可以执行的操作。方法可以接受提供输入数据的参数，并可以通过参数返回输出数据。方法还可以不使用参数而直接返回值。
- **[事件 (Events)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/)**：事件向其他对象提供有关发生的事情 (如单击按钮或成功完成某个方法) 的通知。事件是使用委托定义和触发的。
- **[运算符 (Operators)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/)**：重载运算符被视为类型成员。在重载运算符时，需要将其定义为类型中的公共静态方法。
- **[索引器 (Indexers)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/)**：使用索引器可以用类似于数组的方式为对象建立索引。
- **[构造函数 (Constructors)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/constructors)**：构造函数是首次创建对象时调用的方法。它们通常用于初始化对象的数据。
- **[终结器 (Finalizers)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/finalizers)**：C# 中很少使用终结器。终结器是当对象即将从内存中移除时由运行时执行引擎调用的方法。它们通常用来确保任何必须释放的资源都得到适当的处理。
- **[嵌套类型 (Nested Types)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/nested-types)**：嵌套类型是在其他类型中声明的类型。嵌套类型通常用于描述仅由包含它们的类型使用的对象。



### 3.1.1 访问修饰符

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers

所有类型和类型成员都具有可访问性级别，该级别可以控制是否能够从自己的程序集或者其他程序集中的其他代码中使用它们。程序集是通过在单个编译中编译一个或多个 `.cs` 文件而创建的 `.dll` 或 `.exe`。

可以使用以下访问修饰符在进行声明时指定类型或成员的可访问性：

- [public](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/public)：任何程序集中的代码都可以访问此类型或成员。包含类型的可访问性级别控制该类型的公共成员的可访问性级别。
- [private](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/private)：只有在同一 `class` 或 `struct` 中声明的代码才能访问此成员。
- [protected](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/protected)：只有同一 `class` 或派生的 `class` 中的代码才能访问此类型或成员。
- [internal](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/internal)：只有同一程序集中的代码才能访问此类型或成员。
- [protected internal](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/protected-internal)：只有同一程序集中的代码或另一个程序集的派生类中的代码才能访问此类型或成员。
- [private protected](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/private-protected)：只有同一程序集和同一个类或派生类中的代码才能访问此类型或成员。
- [file](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/file)：只有同一文件中的代码可以访问此类型或成员。

类型上的 `record` 修饰符会导致编译器合成额外的成员。`record` 修饰符不会影响 `record class` 或 `record struct` 的默认可访问性。

可以将可访问性总结为下表：

| 调用方的位置          | `public` | `protected internal` | `protected` | `internal` | `private protected` | `private` | `file` |
| :-------------------- | :------: | :------------------: | :---------: | :--------: | :-----------------: | :-------: | :----: |
| 在文件中              |    ✔️️     |          ✔️           |      ✔️      |     ✔️      |          ✔️          |     ✔️     |   ✔️    |
| 在类中                |    ✔️️     |          ✔️           |      ✔️      |     ✔️      |          ✔️          |     ✔️     |   ❌    |
| 派生类 (相同程序集)   |    ✔️     |          ✔️           |      ✔️      |     ✔️      |          ✔️          |     ❌     |   ❌    |
| 非派生类 (相同程序集) |    ✔️     |          ✔️           |      ❌      |     ✔️      |          ❌          |     ❌     |   ❌    |
| 派生类 (不同程序集)   |    ✔️     |          ✔️           |      ✔️      |     ❌      |          ❌          |     ❌     |   ❌    |
| 非派生类 (不同程序集) |    ✔️     |          ❌           |      ❌      |     ❌      |          ❌          |     ❌     |   ❌    |

不是所有访问修饰符都可以在所有上下文中由所有类型或成员使用。在某些情况下，包含类型的可访问性会限制其成员的可访问性。

当分部类或分部方法的一个声明未声明其可访问性时，它具有另一声明的可访问性。如果分部类或分部方法的多个声明声明了不同的可访问性，编译器会生成错误。

另外，可在 [Access Modifiers (C# Reference)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/access-modifiers) 了解关于访问修饰符的更多信息。

#### 3.1.1.1 类和结构的可访问性

直接在命名空间中声明的类和结构 (没有嵌套在其他类或结构中) 可以为 `public` 或 `internal`。如果未指定任何访问修饰符，则默认设置为 `internal`。

结构成员 (包括嵌套的类和结构) 可以声明为 `public`、`internal` 或 `private`。类成员 (包括嵌套的类和结构) 可以声明为 `public`、`protected internal`、`protected`、`internal`、`private protected` 或 `private`。默认情况下，类成员和结构成员 (包括嵌套的类和结构) 的访问级别为 `private`。

派生类不能具有高于其基类型的可访问性。不能声明派生自内部类 `A` 的公共类 `B`。如果允许这种做法，则它将具有使 `A` 变为公共的效果，因为可以从派生类访问 `A` 的所有 `protected` 或 `internal` 成员。

可以通过使用 `InternalsVisibleToAttribute` 启用特定的其他程序集来访问内部类型。可在 [Friend Assemblies](https://learn.microsoft.com/en-us/dotnet/standard/assembly/friend) 了解详细信息。

#### 3.1.1.2 其他类型的可访问性

在命名空间内直接声明的接口可以声明为 `public` 或 `internal`，就像类和结构一样，接口默认设置为 `internal` 访问级别。接口成员默认为 `public`，因为接口的用途是启用其他类型以访问类或结构。接口成员声明可以包含任何访问修饰符。在 `interface` 成员上使用访问修饰符来提供接口的所有实现者所需的通用实现。

默认情况下，在命名空间中声明的 `delegate` 类型具有 `internal` 访问权限。

#### 3.1.1.3 成员的可访问性

可以使用 6 种访问类型中的任意一种来声明 `class` 或 `struct` 的成员 (包括嵌套的类和结构)。结构成员无法声明为 `protected`、`protected internal` 或 `private protected`，因为结构不支持继承。

通常情况下，成员的可访问性不大于包含该成员的类型的可访问性。但是，如果 `internal` 类的 `public` 成员实现了接口方法或者替代了在公共基类中定义的虚拟方法，则可以从该程序集的外部访问该成员。

任何成员字段、属性或事件的类型必须至少与成员本身具有相同的可访问性。同样，任何方法、索引器或委托的返回类型和参数类型必须至少与成员本身具有相同的可访问性。例如，除非 `C` 也是 `public`，否则不能具有返回类 `C` 的 `public` 方法 `M`。同样，如果 `A` 声明为 `private`，则不能具有类型 `A` 的 `protected` 属性。

用户定义的运算符始终必须声明为 `public` 和 `static`。

终结器不能具有可访问性修饰符。`enum` 类型的成员始终为 `public`，并且不能应用访问修饰符。

仅允许在顶级 (非嵌套) 类型声明中使用 `file` 访问修饰符。



### 3.1.2 字段

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/fields

字段是在类或结构中直接声明的任意类型的变量。字段是其包含类型的成员。

类或结构可能具有实例字段和静态字段。实例字段特定于某种类型的实例。如果有包含实例字段 `F` 的类 `T`，则可以创建两个类型为 `T` 的对象并修改每个对象中 `F` 的值，而不会影响另一个对象中的值。与此相比，静态字段属于类型本身，并在该类型的所有实例之间共享。只能使用类型名称来访问静态字段。如果按实例名称访问静态字段，将出现 [CS0176](https://learn.microsoft.com/en-us/dotnet/csharp/misc/cs0176) 编译时错误。

通常，应为字段声明 `private` 或 `protected` 可访问性。类型向客户端代码公开的数据应通过方法、属性和索引器提供。通过使用这些构造间接访问内部字段，可以防止出现无效的输入值。存储由公共属性公开的数据的私有字段称为 *后备存储 (backing store)* 或 *支持字段 (backing field)*。可以声明 `public` 字段，但这样无法阻止使用该类型的代码将该字段设置为无效值，或者以其他方式更改对象的数据。

字段通常存储必须对多个类型方法可访问且存储时间必须长于任何单个方法的生存期的数据。不在单个方法作用域外使用的变量应声明为方法主体本身中的局部变量。

字段是通过指定访问级别在类或结构块中声明的，其后跟类型，再跟字段的名称。

声明字段时，可以使用赋值运算符为字段指定一个初始值。

字段会在对象实例的构造函数被调用之前即刻初始化。如果构造函数分配了字段的值，则它将覆盖在字段声明期间给定的任何值。

需要注意，字段初始化表达式不能引用其他实例字段。

可以将字段标记为 `public`、`private`、`protected`、`internal`、`protected internal` 或 `private protected`。这些访问修饰符定义了该类型的用户访问该字段的方式。

可以选择性地将字段声明为 `static`。静态字段可以供调用方在任何时候进行调用，即使不存在任何类型的实例。

可以将字段声明为 `readonly`。只能在初始化期间或者在构造函数中为 `readonly` 字段赋值。`static readonly` 字段类似于常量，只不过 C# 编译器在编译时不具有对静态只读字段的值的访问权限，而只有在运行时才具有访问权限。

可以将字段声明为 `required`。必填字段必须由构造函数初始化，或者在创建对象时由对象初始值设定项初始化。将 [`System.Diagnostics.CodeAnalysis.SetsRequiredMembersAttribute`](https://learn.microsoft.com/en-us/dotnet/api/system.diagnostics.codeanalysis.setsrequiredmembersattribute) 特性添加到初始化所有必需成员的任何构造函数声明中。

不能在同一字段上组合 `required` 修饰符和 `readonly` 修饰符。但是，属性只能是 `required` 和 `init`。

从 C# 12 开始，[主构造函数](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/instance-constructors#primary-constructors) 参数是声明字段的替代方法。如果类型具有必须在初始化时提供的依赖项，则可以创建提供这些依赖项的主构造函数。可以捕获这些参数并将其用于代替类型中声明的字段。对于 `record` 类型，主构造函数参数显示为公共属性。



### 3.1.3 常量

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/constants

常量是不可变的值，在编译时是已知的，在程序的生命周期内不会改变。

常量使用 `const` 修饰符声明，只有 C# 内置类型 (如 `int`、`float`、`bool`、`string`) 可声明为 `const`，用户定义的类型 (包括类、结构和数组) 不能为 `const`。除 `string` 以外的引用类型常量只能使用 `null` 值进行初始化。可以使用 `readonly` 修饰符创建在运行时一次性 (例如在构造函数中) 初始化的类、结构或数组，此后不能更改。

C# 不支持 `const` 方法、属性或事件。

常量在声明时必须初始化。

当编译器遇到 C# 源代码中的常量标识符时，它直接将文本值替换到它生成的中间语言 (IL) 代码中。因为运行时没有与常量相关联的变量地址，所以 `const` 字段不能通过引用传递，并且不能在表达式中显示为左值。

需要注意，引用其他代码 (如 DLL) 中定义的常量值时要格外小心。如果新版本的 DLL 定义了新的常量值，则程序仍将保留旧的文本值，直到根据新版本重新编译。

可以将常量标记为 `public`、`private`、`protected`、`internal`、`protected internal` 或 `private protected`，这些访问修饰符定义该类的用户访问该常量的方式。

常量是作为 `static` 字段访问的，因为常量的值对于该类型的所有实例都是相同的。不使用 `static` 关键字来声明这些常量。不在定义常量的类中的表达式必须使用类名、句点和常量名称来访问该常量。

#### 3.1.3.1 const 和 readonly

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/const
>
> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/readonly

##### 3.1.3.1.1 const 关键字

使用 `const` 关键字来声明一个常量字段或局部常量。常量字段和局部常量不是变量，不能被修改。常量可以为数字、布尔值、字符串或 null 引用。不应创建常量来表示需要随时更改的信息。由于编译器会传播常量，所以必须重新编译通过库编译的其他代码才能查看更改。

常数声明的类型指定声明引入的成员类型。一个局部常量或常量字段的初始值设定项必须是一个可以隐式转换为目标类型的常量表达式。

常数表达式是在编译时可被完全计算的表达式。因此，对于引用类型的常量，可能的值只能是字符串和空引用。

常量声明中不允许使用 `static` 修饰符。

##### 3.1.3.1.2 readonly 关键字

`readonly` 关键字是一个可在 5 个上下文中使用的修饰符：

- 在字段声明中，`readonly` 指示只能在声明期间或在同一个类的构造函数中向字段赋值。可以在字段声明和构造函数中多次分配和重新分配只读字段。

  当构造函数退出后，不能分配 `readonly` 字段。此规则对于值类型和引用类型具有不同的含义：

  - 由于值类型直接包含数据，因此属于 `readonly` 值类型的字段不可变。
  - 由于引用类型包含对其数据的引用，因此属于 `readonly` 引用类型的字段必须始终引用同一对象，但是该对象可能是可变的。`readonly` 修饰符可防止将字段值替换为引用类型的其他实例。但是，修饰符不会阻止通过只读字段修改字段的实例数据。

- 在 `readonly struct` 类型定义中，`readonly` 指示结构类型是不可变的。

- 在结构类型内的实例成员声明中，`readonly` 指示实例成员不修改结构的状态。

- 在 `ref readonly` 方法返回中，`readonly` 修饰符指示该方法返回一个引用，且不允许向该引用写入内容。

  - 将 `ref readonly` 参数声明到某个方法。

##### 3.1.3.1.3 const 和 readonly 的区别

综上，可以总结出 `const` 和 `readonly` 的主要区别有：

1. 在字段声明中，使用 `const` 声明的字段必须在声明时初始化，而使用 `readonly` 声明的字段可以在字段声明和构造函数中多次赋值。
2. 在字段声明中，只有内置类型可以声明为 `const`，而 `readonly` 则没有这个限制。
3. 对于类型的所有实例，所有声明为 `const` 的字段的值都是一样的，而 `readonly` 字段则根据所使用的构造函数可能具有不同的值。
4. `const` 字段是编译时常量，而 `readonly` 字段可用于运行时常量。
5. 由于使用了常量的代码在编译时直接将文本值替换到其生成的中间语言代码中，声明 `const` 字段本身不产生内存分配，而对声明为 `readonly` 的字段进行赋值时需要分配内存。



### 3.1.4 属性

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/properties

属性是一种成员，它提供灵活的机制来读取、写入或计算私有字段的值。属性可用作公共数据成员，但属性实际上是称为 *访问器 (accessors)* 的特殊方法。此功能使得可以轻松访问数据，还有助于提高方法的安全性和灵活性。

#### 3.1.4.1 概述

可以对属性进行以下概述：

- 属性允许类公开获取和设置值的公共方法，而隐藏实现或验证代码。
- [`get`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/get) 属性访问器用于返回属性值，而 [`set`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/set) 属性访问器用于分配新值。[`init`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/init) 属性访问器仅用于在对象构造过程中分配新值。这些访问器可以具有不同的访问级别，可在 [限制访问器可访问性](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/restricting-accessor-accessibility) 了解详细信息。
- [`value`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/value) 关键字用于定义由 `set` 或 `init` 访问器分配的值。
- 属性可以是读写属性 (既有 `get` 访问器又有 `set` 访问器)、只读属性 (有 `get` 访问器，但没有 `set` 访问器) 或者只写属性 (有 `set` 访问器，但没有 `get` 访问器)。只写属性很少出现，常用于限制对敏感数据的访问。
- 不需要自定义访问器代码的简单属性可以作为表达式主体定义或者自动实现的属性来实现。

#### 3.1.4.2 具有支持字段的属性

有一个实现属性的基本模式，该模式使用私有支持字段来设置和检索属性值。`get` 访问器返回私有字段的值，`set` 访问器在向私有字段赋值之前可能会执行一些数据验证。这两个访问器还可以在存储或返回数据之前对其执行某些转换或计算。

#### 3.1.4.3 表达式主体定义

属性访问器通常由单行语句组成，这些语句只分配或只返回表达式的结果。可以将这些属性作为 expression-bodied 成员来实现。在 `=>` 符号后跟用于为属性赋值或从属性中检索值的表达式，即组成了表达式主体定义。

只读属性可以将 `get` 访问器作为 expression-bodied 成员实现。在这种情况下，既不使用 `get` 访问器关键字，也不使用 `return` 关键字。例如，在下面的示例中，将只读 `Name` 属性作为 expression-bodied 成员实现：

```c#
public class Person
{
    private string _firstName;
    private string _lastName;

    public Person(string first, string last)
    {
        _firstName = first;
        _lastName = last;
    }

    public string Name => $"{_firstName} {_lastName}";
}
```

`get` 和 `set` 访问器都可以作为 expression-bodied 成员实现。在这种情况下，必须使用 `get` 和 `set` 关键字。例如，在下面的示例中，为 `get` 和 `set` 访问器使用表达式主体定义，而 `return` 关键字不需要与 `get` 访问器一起使用：

```c#
public class SaleItem
{
    string _name;
    decimal _cost;

    public SaleItem(string name, decimal cost)
    {
        _name = name;
        _cost = cost;
    }

    public string Name
    {
        get => _name;
        set => _name = value;
    }

    public decimal Price
    {
        get => _cost;
        set => _cost = value;
    }
}
```

#### 3.1.4.4 自动实现的属性

在某些情况下，属性 `get` 和 `set` 访问器仅向支持字段赋值或仅从其中检索值，而不包括任何附加逻辑。通过使用自动实现的属性，既能简化代码，还能让 C# 编译器透明地提供支持字段。

如果属性具有 `get` 和 `set` (或 `get` 和 `init`) 访问器，则必须自动实现这两个访问器。自动实现的属性通过以下方式定义：使用 `get` 和 `set` 关键字，但不提供任何实现。

自动实现的属性可以为 `get` 和 `set` 访问器声明不同的可访问性。

##### 3.1.4.4.1 概述

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/auto-implemented-properties

当属性访问器中不需要任何其他逻辑时，自动实现的属性会使属性声明更加简洁。自动实现的属性还允许客户端代码创建对象。当声明自动实现的属性时，编译器将创建仅可以通过该属性的 `get` 和 `set` 访问器访问的私有、匿名支持字段。`init` 访问器也可以声明为自动实现的属性。

下面示例声明了几个自动实现的属性：

```c#
// This class is mutable. Its data can be modified from
// outside the class.
public class Customer
{
    // Auto-implemented properties for trivial get and set
    public double TotalPurchases { get; set; }
    public string Name { get; set; }
    public int CustomerId { get; set; }

    // Constructor
    public Customer(double purchases, string name, int id)
    {
        TotalPurchases = purchases;
        Name = name;
        CustomerId = id;
    }

    // Methods
    public string GetContactInfo() { return "ContactInfo"; }
    public string GetTransactionHistory() { return "History"; }

    // .. Additional methods, events, etc.
}

class Program
{
    static void Main()
    {
        // Initialize a new object.
        Customer cust1 = new Customer(4987.63, "Northwind", 90108);

        // Modify a property.
        cust1.TotalPurchases += 499.99;
    }
}
```

不能在接口中声明自动实现的属性。自动实现的属性声明一个私有实例支持字段，并且接口可能不声明实例字段。如果在接口中声明属性而不定义主体，应使用访问器声明属性，访问器必须由实现该接口的每个类型实现。

可以像字段一样初始化自动实现属性：

```c#
public string FirstName { get; set; } = "Jane";
```

在包含重要行为 (方法) 以及数据的复杂类中，通常有必要具有公共属性。但是，对于那些仅封装一组值 (数据) 且很少或没有行为的小型类或结构，应该使用以下选项之一使对象不可变：

- 只声明 `get` 访问器 (除了能在构造函数中可变，在其他任何位置都不可变)。
- 声明 `get` 访问器和 `init` 访问器 (除了能在对象构造函数中可变，在其他任何位置都不可变)。
- 将 `set` 访问器声明为私有的 (对使用者不可变)。

#### 3.1.4.5 必需的属性

从 C# 11 开始，可以添加 `required` 成员以强制客户端代码初始化任何属性或字段：

```c#
public class SaleItem
{
    public required string Name
    { get; set; }

    public required decimal Price
    { get; set; }
}
```

如果要创建 `SaleItem` 实例，必须使用对象初始值设定项设置 `Name` 和 `Price` 属性，如以下代码所示：

```c#
var item = new SaleItem { Name = "Shoes", Price = 19.95m };
Console.WriteLine($"{item.Name}: sells for {item.Price:C2}");
```

#### 3.1.4.6 使用属性

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/using-properties

属性结合了字段和方法的多个方面。对于对象的用户来说，属性似乎是一个字段，访问属性需要相同的语法。对于类的实现者来说，属性是一两个代码块，表示 `get`、`set` 或 `init` 访问器。读取属性时，执行 `get` 访问器的代码块。向属性赋值时，执行 `set` 或 `init` 访问器的代码块。将不带 `set` 访问器的属性视为只读，将不带 `get` 访问器的属性视为只写，将具有以上两个访问器的属性视为读写。可以通过 `init` 访问器而不是 `set` 访问器使该属性能够设置为对象初始化的一部分并在其他情况下使其只读。

与字段不同，属性不会被归类为变量。因此，不能将属性作为 `ref` 或 `out` 参数传递。

属性有许多用途：

- 可以在允许更改数据之前验证数据。
- 以透明方式将从其他源 (例如数据库) 检索数据的类上的数据公开。
- 可以在数据发生更改 (例如引发事件或更改其他字段的值) 时执行操作。

通过依次指定字段的访问级别、属性类型、属性名、声明 `get` 访问器或 `set` 访问器的代码块，在类块中声明属性。

属性数据的真实位置通常称为属性的 *后备存储 (backing store)*，属性通常使用私有字段作为后备存储。将字段标记为私有，确保只能通过调用属性来对其进行更改。

##### 3.1.4.6.1 get 访问器

`get` 访问器的正文类似于方法，必须返回属性类型的值。C# 编译器和实时 (JIT) 编译器检测 `get` 访问器的常见实现模式并优化这些模式。例如，可能会优化返回字段而不执行任何计算的 `get` 访问器对该字段的内存读取。自动实现的属性遵循此模式，并受益于这些优化。但是，无法内联虚拟 `get` 访问器方法，因为编译器在编译时不知道在运行时实际可调用哪些方法。

`get` 访问器必须是表达式主体成员，或者在 `return` 或 `throw` 语句中结束，且控件无法流出访问器主体。

`get` 访问器可用于返回字段值或计算并返回字段值。

##### 3.1.4.6.2 set 访问器

`set` 访问器类似于返回类型为 `void` 的方法。它使用名为 `value` 的隐式参数，该参数的类型为属性的类型。编译器和 JIT 编译器还识别 `set` 或 `init` 访问器的常见模式。这些常见模式经过优化，直接写入支持字段的内存。

为 `set` 访问器中的本地变量声明使用隐式参数名 `value` 是错误的。

##### 3.1.4.6.3 init 访问器

用于创建 `init` 访问器的代码与用于创建 `set` 访问器的代码相同，只不过前者使用的关键字是 `init` 而不是 `set`。不同之处在于，`init` 访问器只能在构造函数中使用，或者通过对象初始值设定项使用。

##### 3.1.4.6.4 额外说明

可以将属性标记为 `public`、`private`、`protected`、`internal`、`protected internal` 或 `private protected`。这些访问修饰符定义该类的用户访问该属性的方式。相同属性的 `get` 和 `set` 访问器可以具有不同的访问修饰符。例如，`get` 可能是 `public`，允许从类型外部进行只读访问；而 `set` 可能为 `private` 或 `protected`。

可以通过使用 `static` 关键字将属性声明为静态属性。静态属性可供调用方在任何时候使用，即使不存在类的任何实例。

可以通过使用 `virtual` 关键字将属性标记为虚拟属性。虚拟属性可使派生类使用 `override` 关键字重写属性行为。

重写虚拟属性的属性也可以是 `sealed`，此指定对于派生类来说该属性不再是虚拟的。最后，还可以将属性声明为 `abstract`。抽象属性不定义类中的实现，派生类必须编写自己的实现。

#### 3.1.4.7 接口属性

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/interface-properties

可以在接口上声明属性。例如：

```c#
public interface ISampleInterface
{
    // Property declaration:
    string Name
    {
        get;
        set;
    }
}
```

接口属性通常没有主体。访问器指示属性是读写、只读还是只写。与在类和结构中不同，在没有主体的情况下声明访问器不会声明自动实现的属性。接口可为成员 (包括属性) 定义默认实现。在接口中为属性定义默认实现的情况非常少，因为接口很可能不会定义实例数据字段。



### 3.1.5 方法

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/methods

方法是包含一系列语句的代码块。程序通过调用该方法并指定任何所需的方法参数使语句得以执行。在 C# 中，每个执行的指令均在方法的上下文中执行。

`Main` 方法是每个 C# 应用程序的入口点，并在启动程序时由公共语言运行时 (CLR) 调用。在使用 [顶级语句](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/program-structure/top-level-statements) 的应用程序中，`Main` 方法由编译器生成并包含所有顶级语句。

#### 3.1.5.1 方法签名

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/methods#method-signatures

通过指定访问级别 (如 `public` 或 `private`)、可选修饰符 (如 `abstract` 或 `sealed`)、返回值、方法的名称以及任何方法参数，在类、结构或者接口中声明方法。这些部件一起构成方法的 *签名 (signature)*。

需要注意的是，为了实现方法重载，方法的返回类型不是方法签名的一部分。但是，在确定委托与其指向的方法之间的兼容性时，方法的返回类型是方法签名的一部分。

方法参数在括号内，并且用逗号分隔。空括号指示方法不需要任何参数。

#### 3.1.5.2 方法参数

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/method-parameters

默认情况下，C# 中的参数按值传递给函数。这意味着将变量的副本会传递到方法。对于值类型，值的副本将传递到方法。对于引用类型，引用的副本将传递到方法。使用参数修饰符可以按引用传递参数。

以下概念有助于了解这些区别以及如何使用参数修饰符：

- 按值传递就是将变量副本传递给方法。
- 按引用传递就是将对变量的访问传递给方法。
- 引用类型的变量包含对其数据的引用。
- 值类型的变量直接包含其数据。

因为结构属于值类型，所以按值将结构传递给方法时，该方法接收结构参数的副本并在其上运行。该方法无法访问调用方法中的原始结构，因此无法对其进行任何更改，只能更改自己的副本。

类实例属于引用类型，而非值类型。按值将引用类型传递给方法时，方法接收对类实例的引用的副本，这两个变量都引用同一对象，参数是引用的副本。调用的方法无法在调用方法中重新分配实例。但是，调用的方法可以使用引用的副本来访问实例成员。如果调用的方法更改了实例成员，调用方法也会看到这些更改，因为它引用同一实例。

##### 3.1.5.2.1 形参类型和实参模式的组合

参数如何传递，以及它是引用类型还是值类型，控制着对参数所作的修改对于调用方是否可见：

- 按值传递值类型时：
  - 如果方法分配参数以引用其他对象，则这些更改在调用方是不可见的。
  - 如果方法修改参数所引用对象的状态，则这些更改在调用方是不可见的。
- 按值传递引用类型时：
  - 如果方法分配参数以引用其他对象，则这些更改在调用方是不可见的。
  - 如果方法修改参数所引用对象的状态，则这些更改在调用方是可见的。
- 按引用传递值类型时：
  - 如果方法分配参数以引用其他对象，则这些更改在调用方是不可见的。
  - 如果方法修改参数所引用对象的状态，则这些更改在调用方是可见的。
- 按引用传递引用类型时：
  - 如果方法分配参数以引用其他对象，则这些更改在调用方是可见的。
  - 如果方法修改参数所引用对象的状态，则这些更改在调用方是可见的。

按引用传递引用类型使得被调用方能够替换调用方中引用参数所引用的对象。对象的存储位置按引用参数的值传递到方法。如果更改参数存储位置中的值 (以指向新对象)，还可以将存储位置更改为调用方所引用的位置。例如，在下面的示例中，将引用类型的实例作为 `ref` 参数传递：

```c#
class Product
{
    public Product(string name, int newID)
    {
        ItemName = name;
        ItemID = newID;
    }

    public string ItemName { get; set; }
    public int ItemID { get; set; }
}

private static void ChangeByReference(ref Product itemRef)
{
    // Change the address that is stored in the itemRef parameter.
    itemRef = new Product("Stapler", 12345);
}

private static void ModifyProductsByReference()
{
    // Declare an instance of Product and display its initial values.
    Product item = new Product("Fasteners", 54321);
    System.Console.WriteLine("Original values in Main.  Name: {0}, ID: {1}\n",
        item.ItemName, item.ItemID);

    // Pass the product instance to ChangeByReference.
    ChangeByReference(ref item);
    System.Console.WriteLine("Calling method.  Name: {0}, ID: {1}\n",
        item.ItemName, item.ItemID);
}

// This method displays the following output:
// Original values in Main.  Name: Fasteners, ID: 54321
// Calling method.  Name: Stapler, ID: 12345
```

##### 3.1.5.2.2 引用和值的安全上下文

方法可以将参数的值存储在字段中。当参数按值传递时，这通常是安全的。值会进行复制，并且当引用类型存储在字段中时，是可以访问的。为了安全地按引用传递参数，需要编译器定义何时可以安全地将引用分配给新变量。对于每个表达式，编译器都会定义 *安全上下文 (safe context)* 来限制对表达式或变量的访问。编译器使用两个范围：*safe-context* 和 *ref-safe-context*。

- *safe-context* 定义可以安全地访问任何表达式的范围。
- *ref-safe-context* 定义可以安全地访问或修改对任何表达式的引用的范围。

在非正式情况下，可以将这些范围视为机制，以确保代码永远不会访问或修改不再有效的引用。只要一个引用指向的是有效的对象或结构，它就有效。*safe-context* 定义何时可以对变量赋值或重新赋值。*ref-safe-context* 定义何时可以对 ref 赋值或者对 ref 重新赋值。赋值操作会为变量赋一个新值，ref 赋值操作会为变量赋值以引用其他存储位置。

##### 3.1.5.2.3 引用参数

将以下修饰符之一应用于参数声明，以按引用而不是按值传递参数：

- `ref`：在调用方法之前必须初始化参数。被调用方法可以为参数分配新值，但这不是必须的。
- `out`：在调用方法之前，调用方法不需要初始化参数。被调用方法必须向参数赋值。
- `readonly ref`：在调用方法之前必须初始化参数。被调用方法不能为参数分配新值。
- `in`：在调用方法之前必须初始化参数。被调用方法不能为参数分配新值。编译器可能会创建一个临时变量来保存 `in` 参数的副本。

类的成员不能具有仅在 `ref`、`ref readonly`、`in` 或 `out` 方面不同的签名。如果类型的两个成员之间的唯一区别在于其中一个具有 `ref` 参数，而另一个具有 `out`、`ref readonly` 或 `in` 参数，则会发生编译器错误。但是，当一个方法具有 `ref`、`ref readonly`、`in` 或 `out` 参数，另一个方法具有值传递的参数时，则可以重载方法。在其他要求签名匹配的情况下 (如隐藏或重写)，`in`、`ref`、`ref readonly` 和 `out` 是签名的一部分，相互之间不匹配。

当一个参数具有上述修饰符之一时，相应的实参可以具有兼容的修饰符：

- 参数 `ref` 的实参必须包含 `ref` 修饰符。
- 参数 `out` 的实参必须包含 `out` 修饰符。
- 参数 `in` 的实参可以选择性包含 `in` 修饰符。如果在实参上使用 `ref` 修饰符，则编译器会发出警告。
- `ref readonly` 参数的实参应包含 `in` 或 `ref` 修饰符，但不能同时包含两者。如果两个修饰符均未包含，编译器会发出警告。

在使用这些修饰符时，它们描述了参数的使用方式：

- `ref` 表示该方法可以读取或写入参数的值。
- `out` 表示该方法设置参数的值。
- `ref readonly` 表示该方法可以读取但无法写入参数的值。参数应按引用传递。
- `in` 表示该方法可以读取但无法写入参数的值。参数将按引用或通过临时变量传递。

属性不是变量，而是方法，不能传递给 `ref` 参数。

不能在以下类型的方法中使用参数修饰符：

- 异步方法，通过使用 `async` 修饰符定义。
- 迭代器方法，包括 `yield return` 或 `yield break` 语句。

扩展方法还对以下参数关键字的使用有所限制：

- 不能对扩展方法的第一个参数使用 `out` 关键字。
- 当参数不是 `struct` 或者是不被约束为结构的泛型类型时，不能对扩展方法的第一个参数使用 `ref` 关键字。
- 除非第一个参数是 `struct` ，否则无法使用 `ref readonly` 和 `in` 关键字。
- 即使约束为结构，也不能对任何泛型类型使用 `ref readonly` 和 `in` 关键字。

###### 3.1.5.2.3.1 ref 参数修饰符

如果要使用 `ref` 参数，方法定义和调用方法都必须显式使用 `ref` 关键字。除了在进行 COM 调用时，调用方法可忽略 `ref`。

传递给 `ref` 参数的参数必须先经过初始化，然后才能传递。

###### 3.1.5.2.3.2 out 参数修饰符

如果要使用 `out` 参数，方法定义和调用方法都必须显式使用 `out` 关键字。

作为 `out` 参数传递的变量在传入方法调用之前无需进行初始化。但是，被调用的方法需要在返回之前赋值。

析构方法使用 `out` 修饰符声明其参数以返回多个值。其他方法可以返回包含多个返回值的值元组。

必须先在单独的语句中声明变量，然后才能将其作为 `out` 参数传递。还可以在方法调用的参数列表而不是单独的变量声明中声明 `out` 变量。`out` 变量使代码更简洁可读，还能防止在方法调用之前无意中向该变量赋值。

###### 3.1.5.2.3.3 ref readonly 修饰符

方法声明中必须存在 `ref readonly` 修饰符。调用站点上的修饰符是可选的，可以使用 `in` 或 `ref` 修饰符。`ref readonly` 修饰符在调用站点上无效。在调用站点中使用的修饰符有助于描述参数的特征。只有当参数是变量且可写时，才能使用 `ref`。只有当参数是变量时，才能使用 `in`。参数可能是可写的或只读的。如果参数不是变量，而是表达式，则不能添加任何一个修饰符。例如，在下面方法中使用 `ref readonly` 修饰符来指示出于性能原因，应按引用传递大型结构：

```c#
public static void ForceByRef(ref readonly OptionStruct thing)
{
    // elided
}
```

可以使用 `ref` 或 `in` 修饰符来调用该方法。如果省略修饰符，编译器会发出警告。当参数是表达式而不是变量时，不能添加 `in` 或 `ref` 修饰符，因此应禁止显示警告：

```c#
ForceByRef(in options);
ForceByRef(ref options);
ForceByRef(options); // Warning! variable should be passed with `ref` or `in`
ForceByRef(new OptionStruct()); // Warning, but an expression, so no variable to reference
```

如果变量是 `readonly` 变量，则必须使用 `in` 修饰符。如果改用 `ref` 修饰符，编译器将发出错误。

`ref readonly` 修饰符表示该方法要求参数是变量，而不是非变量的表达式。非变量的表达式包括常量、方法返回值和属性。如果参数不是变量，编译器会发出警告。

###### 3.1.5.2.3.4 in 参数修饰符

在方法声明中 `in` 修饰符是必需的，但在调用站点中不需要。

`in` 修饰符允许编译器为参数创建一个临时变量，并传递对该参数的只读引用。当必须转换参数、从参数类型进行隐式转换或者参数是非变量的值时，编译器始终会创建一个临时变量。例如，当参数是文本值或从属性访问器返回的值时。如果 API 要求按引用传递参数时，应选择 `ref readonly` 修饰符而不是 `in` 修饰符。

使用 `in` 参数定义的方法可能会获得性能优化。某些 `struct` 类型参数可能很大，在紧凑的循环或者关键代码路径中调用方法时，复制这些结构的成本很高。方法声明 `in` 参数以指定参数可以按引用安全传递，因为所调用的方法不修改该参数的状态。按引用传递这些参数可以避免 (可能产生的) 高昂的复制成本。在调用站点显式添加 `in` 修饰符以确保参数是按引用传递，而不是按值传递。

显式使用 `in` 有以下两个效果：

- 在调用站点指定 `in` 会强制编译器选择使用匹配的 `in` 参数定义的方法。否则，如果两种方法唯一的区别在于是否存在 `in`，则按值重载的匹配度会更高。
- 指定 `in` 可以表明按引用传递参数的意图。与 `in` 一起使用的参数必须表示一个可以直接引用的位置。`out` 和 `ref` 参数的相同常规规则适用：不能使用常数、普通属性或者其他生成值的表达式。否则，在调用站点省略 `in` 会通知编译器可以创建临时变量，并按只读引用传递到方法。编译器创建临时变量来克服 `in` 参数的一些限制：
  - 临时变量允许将编译时常数作为 `in` 参数。
  - 临时变量允许使用属性或 `in` 参数的其他表达式。
  - 存在从实参类型到参数类型的隐式转换时，临时变量允许使用实参。

以下代码验证了这些规则：

```c#
static void Method(in int argument)
{
    // implementation removed
}

Method(5); // OK, temporary variable created.
Method(5L); // CS1503: no implicit conversion from long to int
short s = 0;
Method(s); // OK, temporary int created with the value 0
Method(in s); // CS1503: cannot convert from in short to in int
int i = 42;
Method(i); // passed by readonly reference
Method(in i); // passed by readonly reference, explicitly using `in`
```

然后，假设有另一种按值传递参数的方法。结果的变化如以下代码所示：

```c#
static void Method(int argument)
{
    // implementation removed
}

static void Method(in int argument)
{
    // implementation removed
}

Method(5); // Calls overload passed by value
Method(5L); // CS1503: no implicit conversion from long to int
short s = 0;
Method(s); // Calls overload passed by value.
Method(in s); // CS1503: cannot convert from in short to in int
int i = 42;
Method(i); // Calls overload passed by value
Method(in i); // passed by readonly reference, explicitly using `in`
```

只有最后一个是按引用传递参数的方法调用。

##### 3.1.5.2.4 params 修饰符

在方法声明中的 `params` 关键字后不允许有任何其他参数，并且在方法声明中只允许有一个 `params` 关键字。

`params` 参数的声明类型必须是集合类型。可识别的集合类型包括：

- 一维数组类型 `T[]`。其中，元素类型为 `T`。
- 范围类型：
  - `System.Span<T>`
  - `System.ReadOnlySpan<T>`
- 具有可访问创建方法及相应元素类型的类型。[创建方法](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/collection-expressions#collection-builder) 使用与 [集合表达式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/collection-expressions) 相同的属性进行标识。
- 实现 `System.Collections.Generic.IEnumerable<T>` 的结构或类类型，其中：
  - 该类型具有可以不带参数调用的构造函数，并且该构造函数至少与声明成员一样可访问。
  - 类型具有实例 (而不是扩展) 方法 `Add`，其中：
    - 可以使用单个值参数调用该方法。
    - 如果方法是泛型方法，则可以从参数推断类型参数。
    - 该方法至少与声明成员一样可访问。此处，元素类型是该类型的迭代类型。
- 接口类型：
  - `System.Collections.Generic.IEnumerable<T>`
  - `System.Collections.Generic.IReadOnlyCollection<T>`
  - `System.Collections.Generic.IReadOnlyList<T>`
  - `System.Collections.Generic.ICollection<T>`
  - `System.Collections.Generic.IList<T>`

在 C# 13 之前，参数必须是一维数组。

当使用 `params` 参数调用方法时，可以传入：

- 数组元素类型的参数的逗号分隔列表。
- 指定类型的参数的集合。
- 无参数。如果未发送任何参数，则 `params` 列表的长度为 0。

#### 3.1.5.3 隐式类型化局部变量

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/implicitly-typed-local-variables

可以声明局部变量而无需提供显式类型。[`var`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/declarations#implicitly-typed-local-variables) 关键字指示编译器通过初始化语句右侧的表达式来推断变量的类型。推断类型可以是内置类型、匿名类型、用户定义类型或者 .NET 类库中定义的类型。另外，还可以 [隐式类型化数组](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/arrays#implicitly-typed-arrays)。

`var` 关键字并不意味着 "变体"，并且并不指示变量是松散类型或是后期绑定。它只表示由编译器确定并分配最适合的类型。

在以下上下文中，可以使用 `var` 关键字：

- 在局部变量 (在方法范围内声明的变量) 上。
- 在 `for` 初始化语句中。
- 在 `foreach` 初始化语句中。
- 在 `using` 语句中。

以下限制适用于隐式类型化变量声明：

- 仅当局部变量在相同语句中进行声明和初始化时，才能使用 `var`。变量不能初始化为 `null`，也不能初始化为方法组或匿名函数。
- `var` 不能在类范围内对字段使用。
- 使用 `var` 声明的变量不能在初始化表达式中使用。例如，此表达式是合法的：`int i = (i = 20);`，但是此表达式会生成编译时错误：`var i = (i = 20);`。
- 不能在相同语句中初始化多个隐式类型化变量。
- 如果一种名为 `var` 的类型处于范围内，则 `var` 关键字会解析为该类型名称，不会被视为隐式类型化局部变量声明的一部分。

带 `var` 关键字的隐式类型只能应用于本地方法范围内的变量。隐式类型不可用于类字段，因为 C# 编译器在处理代码时会遇到逻辑悖论：编译器需要知道字段的类型，但它在分析赋值表达式前无法确定类型，而表达式在不知道类型的情况下无法进行计算。

使用 `var` 有助于简化代码，但是它的使用应该限制在需要使用它的情况下，或者在它可以使代码更易于读取的情况下。

#### 3.1.5.4 本地函数

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/local-functions

本地函数是一种嵌套在另一个成员中的类型的方法。只能从其包含成员中调用本地函数。可以在以下位置中声明和调用本地函数：

- 方法 (尤其是迭代器方法和异步方法)
- 构造函数
- 属性访问器
- 事件访问器
- 匿名方法
- Lambda 表达式
- 终结器
- 其他本地函数

但是，不能在 expression-bodied 成员中声明本地函数。

在某些情况下，可以使用 lambda 表达式实现本地函数也支持的功能。可在 [本地函数与 lambda 表达式](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/local-functions#local-functions-vs-lambda-expressions) 了解两者的差别。

本地函数可使代码意图明确。任何读取代码的人都可以看到，此方法不可调用，包含方法除外。对于团队项目，它们也使得其他开发人员无法直接从类或结构中的其他位置错误调用此方法。

#### 3.1.5.5 扩展方法

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods

通过扩展方法能够向现有类型 "添加" 方法，而无需创建新的派生类型、重新编译或者以其他方式修改原始类型。扩展方法是一种静态方法，但可以像扩展类型上的实例方法一样进行调用。对于使用 C#、F# 和 Visual Basic 编写的客户端代码，调用扩展方法与调用在类型中定义的方法没有明显区别。

最常见的扩展方法是 LINQ 标准查询运算符，它将查询功能添加到现有的 `System.Collections.IEnumerable` 和 `System.Collections.Generic.IEnumerable<T>` 类型。如果要使用标准查询运算符，应首先使用 `using System.Linq` 指令将它们置于范围中。然后，任何实现了 `IEnumerable<T>` 的类型看起来都具有 `GroupBy`、`OrderBy`、`Average` 等实例方法。在 `IEnumerable<T>` 类型的实例 (如 `List` 或 `Array`) 后键入 "dot" 时，可以在 IntelliSense 语句完成中看到这些附加方法。



### 3.1.6 事件

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/

类或对象可以通过事件向其他类或对象通知发生的相关事情。发送 (或引发) 事件的类称为 *发布者 (publisher)*，接收 (或处理) 事件的类称为 *订阅者 (subscribers)*。

事件具有以下属性：

- 发布者确定引发事件的时机，订阅者确定对事件作出的响应。
- 一个事件可以有多个订阅者。订阅者可以处理来自多个发布者的多个事件。
- 没有订阅者的事件永远不会引发。
- 事件通常用于表示用户操作，例如单击按钮或者图形用户界面中的菜单选项。
- 当事件具有多个订阅者时，引发该事件时会同步调用事件处理程序。
- 在 .NET 类库中，事件基于 [`EventHandler`](https://learn.microsoft.com/en-us/dotnet/api/system.eventhandler) 委托和 [`EventArgs`](https://learn.microsoft.com/en-us/dotnet/api/system.eventargs) 基类。

事件的声明方式与字段类似，区别是事件声明包括 [`event`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/event) 关键字，且类型必须是委托类型。

在声明事件成员的类中，事件的行为与委托类型的字段完全相同 (前提是事件不是抽象的，且不声明访问器)。字段存储对委托的引用，委托表示已添加到事件的事件处理程序。如果没有任何事件处理程序，则字段为 `null`。

客户端通过 `EventHandler` 响应事件。使用 `+=` 和 `-=` 运算符分别可以附加和删除 `EventHandler`。

对于需要控制事件的基础存储的高级方案，事件声明可以显式提供 [`add`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/add) 和 [`remove`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/remove) 访问器，这与属性的 `set` 访问器类似。

#### 3.1.6.1 区别委托和事件

> https://learn.microsoft.com/en-us/dotnet/csharp/distinguish-delegates-events

委托和事件的选择通常很困难，因为这两种语言特性很相似，事件甚至是使用委托的语言支持构建的。

它们都提供了一个后期绑定方案：在该方案中，组件通过调用仅在运行时识别的方法进行通信。它们都支持单个和多个订阅者方法，这称为单播和多播支持。二者均支持用于添加和删除处理程序的类似语法。最后，引发事件和调用委托使用完全相同的方法调用语法。它们甚至都支持与 `?.` 运算符一起使用的相同的 `Invoke()` 方法语法。

由于所有这些相似之处，很难确定什么时候该使用什么语法。

##### 3.1.6.1.1 监听事件是可选的

在确定要使用的语言特性时，最重要的考虑因素为是否必须具有附加的订阅者。如果代码必须调用订阅者提供的代码，则在需要实现回调时，应使用基于委托的设计。如果代码在不调用任何订阅者的情况下可以完成其所有工作，则应使用基于事件的设计。

##### 3.1.6.1.2 返回值需要委托

另一个考虑因素是委托方法所需的方法原型。用于事件的委托都具有 `void` 返回类型，还存在创建事件处理程序的习惯用法，这些事件处理程序通过修改事件参数对象的属性将信息传回到事件源。虽然这些习惯用法确实有效，但它们不像从方法返回值那样自然。

应注意，这两种试探法可能经常同时存在：如果委托方法会返回值，则可能会以某种方式影响算法。

##### 3.1.6.1.3 事件具有私有调用

包含事件的类以外的类只能添加和删除事件监听器，只有包含事件的类才能调用事件。事件通常是公共的类成员。相比之下，委托通常作为参数传递，并存储为私有的类成员 (如果被存储)。

##### 3.1.6.1.4 事件监听器通常具有较长的生命周期

事件监听器通常具有较长的生命周期的这一理由不太充分。但是，通常可能会发现，当事件源将在很长一段时间内引发事件时，基于事件的设计会更加自然。可以在许多系统上看到基于事件的 UX 控件设计示例。在订阅事件后，事件源可能会在程序的整个生命周期内引发事件。

这与许多基于委托的设计形成对比。在委托中，委托用作方法的参数，并且在该方法返回后不再使用委托。

##### 3.1.6.1.5 仔细评估

以上考虑因素并非固定不变的规则。相反，它们是有助于决定针对特定使用情况的最佳选择的指南。因为委托和事件类似，所以甚至可以将两者作为原型，并考虑使用更加自然的一种。两者均能很好地处理后期绑定方案，可从中选择最合适的一种。



### 3.1.7 运算符和表达式

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/

C# 提供了许多运算符，其中很多都受到内置类型的支持，可用于对这些类型的值执行基本操作。

这些运算符包括：

- [算术运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators)，对数值操作数执行算术运算。
- [比较运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/comparison-operators)，比较数值操作数。
- [布尔逻辑运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators)，对 `bool` 操作数执行逻辑运算。
- [位运算符和移位运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators)，对整数类型的操作数执行位运算或移位运算。
- [相等运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/equality-operators)，检查其操作数是否相等。

通常可以重载这些运算符，即可以为用户定义类型的操作数指定运算符行为。

最简单的 C# 表达式是文本 (例如整数和实数) 和变量名称。可以使用运算符将它们组合成复杂的表达式。运算符优先级和结合性决定了表达式中操作的执行顺序。可以使用括号更改由运算符优先级和结合性决定的计算顺序。

在下面的代码中，表达式的示例位于赋值的右侧：

```c#
int a, b, c;
a = 7;
b = a;
c = b++;
b = a + b * c;
c = a >= 100 ? b : c / 10;
a = (int)Math.Sqrt(b * b + c * c);

string s = "String literal";
char l = s[s.Length - 1];

List<int> numbers = [..collection];
b = numbers.FindLast(n => n > 1);
```

通常情况下，表达式会生成结果，并且可以包含在其他表达式中。`void` 方法调用是不生成结果的表达式的示例，它只能用作语句，如下面的示例所示：

```c#
Console.WriteLine("Hello, world!");
```

#### 3.1.7.1 运算符优先级

在包含多个运算符的表达式中，先按优先级较高的运算符计算，再按优先级较低的运算符计算。

下表按最高优先级到最低优先级的顺序列出 C# 运算符，表中每行的运算符的优先级相同：

| **运算符**                                                   | **类别或名称**                                               |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [x.y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#member-access-expression-), [f(x)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#invocation-expression-), [a[i\]](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#indexer-operator-), [`x?.y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-), [`x?[y\]`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-), [x++](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#increment-operator-), [x--](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#decrement-operator---), [x!](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-forgiving), [new](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/new-operator), [typeof](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#typeof-operator), [checked](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/checked-and-unchecked), [unchecked](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/checked-and-unchecked), [default](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/default), [nameof](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/nameof), [delegate](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/delegate-operator), [sizeof](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/sizeof), [stackalloc](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/stackalloc), [x->y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/pointer-related-operators#pointer-member-access-operator--) | 主要                                                         |
| [+x](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#unary-plus-and-minus-operators), [-x](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#unary-plus-and-minus-operators), [!x](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#logical-negation-operator-), [~x](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#bitwise-complement-operator-), [++x](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#increment-operator-), [--x](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#decrement-operator---), [^x](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#index-from-end-operator-), [(T)x](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#cast-expression), [await](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/await), [&x](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/pointer-related-operators#address-of-operator-), [*x](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/pointer-related-operators#pointer-indirection-operator-), [true and false](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/true-false-operators) | 一元                                                         |
| [x..y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#range-operator-) | 范围                                                         |
| [switch](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/switch-expression), [with](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/with-expression) | `switch` 和 `with` 表达式                                    |
| [x * y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#multiplication-operator-), [x / y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#division-operator-), [x % y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#remainder-operator-) | 乘法                                                         |
| [x + y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#addition-operator-), [x – y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#subtraction-operator--) | 加法                                                         |
| [x << y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#left-shift-operator-), [x >> y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#right-shift-operator-), [x >>> y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#unsigned-right-shift-operator-) | Shift                                                        |
| [x < y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/comparison-operators#less-than-operator-), [x > y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/comparison-operators#greater-than-operator-), [x <= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/comparison-operators#less-than-or-equal-operator-), [x >= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/comparison-operators#greater-than-or-equal-operator-), [is](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#is-operator), [as](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#as-operator) | 关系和类型测试                                               |
| [x == y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/equality-operators#equality-operator-), [x != y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/equality-operators#inequality-operator-) | 相等性                                                       |
| `x & y`                                                      | [布尔逻辑 AND](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#logical-and-operator-) 或 [按位逻辑 AND](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#logical-and-operator-) |
| `x ^ y`                                                      | [布尔逻辑 XOR](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#logical-exclusive-or-operator-) 或 [按位逻辑 XOR](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#logical-exclusive-or-operator-) |
| `x | y`                                                      | [布尔逻辑 OR](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#logical-or-operator-) 或 [按位逻辑 OR](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#logical-or-operator-) |
| [x && y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-) | 条件 AND                                                     |
| [x \|\| y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-or-operator-) | 条件 OR                                                      |
| [x ?? y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-coalescing-operator) | Null 合并运算符                                              |
| [c ? t : f](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-operator) | 条件运算符                                                   |
| [x = y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/assignment-operator), [x += y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#compound-assignment), [x -= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#compound-assignment), [x *= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#compound-assignment), [x /= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#compound-assignment), [x %= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#compound-assignment), [x &= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#compound-assignment), [x \|= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#compound-assignment), [x ^= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#compound-assignment), [x <<= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#compound-assignment), [x >>= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#compound-assignment), [x >>>= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#compound-assignment), [x ??= y](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-coalescing-operator), [=>](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-operator) | 赋值和 lambda 声明                                           |

#### 3.1.7.2 运算符结合性

当运算符的优先级相同时，运算符的结合性决定了运算的执行顺序：

- 左结合运算符按从左到右的顺序计算。除赋值运算符和 null 合并运算符外，所有二元运算符都是左结合运算符。例如，`a + b - c` 将计算为 `(a + b) - c`。
- 右结合运算符按从右到左的顺序计算。赋值运算符、null 合并运算符、lambda 和条件运算符 `?:` 是右结合运算符。例如，`x = y = z` 将计算为 `x = (y = z)`。

使用括号可以更改运算符结合性所施加的计算顺序：

```c#
int a = 13 / 5 / 2;
int b = 13 / (5 / 2);
Console.WriteLine($"a = {a}, b = {b}");  // output: a = 1, b = 6
```

#### 3.1.7.3 操作数计算

与运算符的优先级和结合性无关，从左到右计算表达式中的操作数。下面示例展示了运算符和操作数的计算顺序：

| **表达式**        | **计算顺序**        |
| :---------------- | :------------------ |
| `a + b`           | a, b, +             |
| `a + b * c`       | a, b, c, *, +       |
| `a / b + c * d`   | a, b, /, c, d, *, + |
| `a / (b + c) * d` | a, b, c, +, /, d, * |

通常，会计算所有运算符操作数。但是，某些运算符有条件地计算操作数。也就是说，此类运算符的最左侧操作数的值定义了是否应计算其他操作数，或计算其他哪些操作数。这些运算符包括条件逻辑 AND (`&&`) 和 OR (`||`) 运算符、null 合并运算符 `??` 和 `??=`、null 条件运算符 `?.` 和 `?[]` 以及条件运算符 `?:`。

#### 3.1.7.4 重载运算符

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/operator-overloading

用户定义的类型可以重载预定义的 C# 运算符。也就是说，当一个或两个操作数都是某类型时，此类型可提供操作的自定义实现。

使用 `operator` 关键字来声明运算符。运算符声明必须符合以下规则：

- 同时包含 `public` 和 `static` 修饰符。
- 一元运算符有一个输入参数。二元运算符有两个输入参数。在每种情况下，都至少有一个参数必须具有类型 `T` 或 `T?`，其中 `T` 是包含运算符声明的类型。

还可以使用 `operator` 关键字来定义自定义类型转换。

##### 3.1.7.4.1 可重载运算符

下表显示了可重载的运算符：

|                          **运算符**                          | **说明**                                                     |
| :----------------------------------------------------------: | :----------------------------------------------------------- |
| [`+x`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#unary-plus-and-minus-operators), [`-x`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#unary-plus-and-minus-operators), [`!x`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#logical-negation-operator-), [`~x`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#bitwise-complement-operator-), [`++`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#increment-operator-), [`--`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#decrement-operator---), [`true`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/true-false-operators), [`false`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/true-false-operators) | `true` 和 `false` 运算符必须一起重载。                       |
| [`x + y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#addition-operator-), [`x - y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#subtraction-operator--), [`x * y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#multiplication-operator-), [`x / y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#division-operator-), [`x % y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#remainder-operator-), [`x & y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#logical-and-operator-), [`x | y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#logical-or-operator-), [`x ^ y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#logical-exclusive-or-operator-), [`x << y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#left-shift-operator-), [`x >> y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#right-shift-operator-), [`x >>> y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#unsigned-right-shift-operator-) |                                                              |
| [`x == y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/equality-operators#equality-operator-), [`x != y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/equality-operators#inequality-operator-), [`x < y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/comparison-operators#less-than-operator-), [`x > y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/comparison-operators#greater-than-operator-), [`x <= y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/comparison-operators#less-than-or-equal-operator-), [`x >= y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/comparison-operators#greater-than-or-equal-operator-) | 必须按如下方式成对重载：`==` and `!=`, `<` and `>`, `<=` and `>=`. |

##### 3.1.7.4.2 不可重载运算符

下表显示了无法重载的运算符：

|                          **运算符**                          | **备选方法**                                                 |
| :----------------------------------------------------------: | :----------------------------------------------------------- |
| [`x && y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-), [`x || y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-or-operator-) | 重载 `true` 和 `false` 运算符以及 `&` 或 `|` 运算符。        |
| [`a[i\]`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#indexer-operator-), [`a?[i\]`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-) | 定义索引器。                                                 |
| [`(T)x`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#cast-expression) | 定义可由强制转换表达式执行的自定义类型转换。                 |
| [`+=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#compound-assignment), [`-=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#compound-assignment), [`*=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#compound-assignment), [`/=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#compound-assignment), [`%=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/arithmetic-operators#compound-assignment), [`&=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#compound-assignment), [`|=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#compound-assignment), [`^=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/boolean-logical-operators#compound-assignment), [`<<=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#compound-assignment), [`>>=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#compound-assignment), [`>>>=`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/bitwise-and-shift-operators#compound-assignment) | 重载相应的二元运算符。例如，重载二进制 `+` 运算符时，会隐式重载 `+=`。 |
| [`^x`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#index-from-end-operator-), [`x = y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/assignment-operator), [`x.y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#member-access-expression-), [`x?.y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-), [`c ? t : f`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-operator), [`x ?? y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-coalescing-operator), [`??= y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-coalescing-operator), [`x..y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#range-operator-), [`x->y`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/pointer-related-operators#pointer-member-access-operator--), [`=>`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-operator), [`f(x)`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/member-access-operators#invocation-expression-), [`as`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#as-operator), [`await`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/await), [`checked`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/checked-and-unchecked), [`unchecked`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/checked-and-unchecked), [`default`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/default), [`delegate`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/delegate-operator), [`is`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#is-operator), [`nameof`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/nameof), [`new`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/new-operator), [`sizeof`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/sizeof), [`stackalloc`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/stackalloc), [`switch`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/switch-expression), [`typeof`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#typeof-operator), [`with`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/with-expression) | 无。                                                         |



### 3.1.8 索引器

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/

索引器允许对类或结构的实例进行索引，就像数组一样。无需显式指定类型或实例成员，即可设置或检索索引值。索引器类似于属性，不同之处在于索引器的访问器带有参数。

索引器具有以下特性：

- 使用索引器可以用类似于数组的方式为对象建立索引。
- `get` 访问器用于返回值，`set` 访问器用于分配值。
- [`this`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/this) 关键字用于定义索引器。
- [`value`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/value) 关键字用于定义由 `set` 访问器分配的值。
- 索引器不必根据整数值进行索引，可自行决定如何定义特定的查找机制。
- 索引器可被重载。
- 索引器可以有多个形参，例如当访问二维数组时。

#### 3.1.8.1 属性和索引器之间的比较

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/indexers/comparison-between-properties-and-indexers

索引器与属性类似。除下表所示的差别外，对属性访问器定义的所有规则也适用于索引器访问器。

| **属性**                                   | **索引器**                                                   |
| :----------------------------------------- | :----------------------------------------------------------- |
| 允许将方法当作公共数据成员来调用方法。     | 允许使用对象本身的数组表示法来访问对象内部集合的元素。       |
| 通过简单名称访问。                         | 通过索引访问。                                               |
| 可以是静态成员或实例成员。                 | 必须是实例成员。                                             |
| 属性的 `get` 访问器没有任何参数。          | 索引器的 `get` 访问器具有与索引器相同的形参列表。            |
| 属性的 `set` 访问器包含隐式 `value` 参数。 | 索引器的 `set` 访问器具有与索引器相同的形参列表，并且还具有 `value` 参数。 |
| 通过自动实现的属性支持简短语法。           | 支持只读索引器的 expression-bodied 成员。                    |



### 3.1.9 构造函数

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/constructors

每当创建类或结构的实例时，将会调用其构造函数。类或结构可能具有采用不同参数的多个构造函数。使用构造函数，程序员能够设置默认值、限制实例化，并编写灵活易读的代码。

初始化新实例需要执行多个操作，这些操作按以下顺序进行：

1. 实例字段设置为 0。这通常由运行时来完成。
2. 字段初始值设定项运行。派生程度最高的类型的字段初始值设定项运行。
3. 基类型字段初始值设定项运行。以直接基类型开始，从每个基类型到 `System.Object` 的字段初始值设定项。
4. 基实例构造函数运行。以 [Object.Object](https://learn.microsoft.com/en-us/dotnet/api/system.object.-ctor) 开始，从每个基类到直接基类的任何实例构造函数。
5. 实例构造函数运行。该类型的实例构造函数运行。
6. 对象初始值设定项运行。如果表达式包含任何对象初始值设定项，后者会在实例构造函数运行后运行。对象初始值设定项按文本顺序运行。

初始化新实例时，将执行上述操作。如果 `struct` 的新实例设置为其 `default` 值，则所有实例字段都设置为 0。

如果静态构造函数尚未运行，静态构造函数会在任何实例构造函数操作执行之前运行。

与其他成员不同，实例构造函数不会被继承。类中只能包含实际上已在该类中声明的实例构造函数。如果没有为类提供实例构造函数，则会自动提供不含参数的空实例构造函数。

#### 3.1.9.1 构造函数语法

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/constructors#constructor-syntax

构造函数是一种方法，方法名称与其类型的名称相同。其方法签名仅包含可选访问修饰符、方法名称和其参数列表。构造函数不包含返回类型。

#### 3.1.9.2 使用构造函数

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/using-constructors

实例化类或结构时，将会调用其构造函数。构造函数与该类或结构具有相同名称，并且通常初始化新对象的数据成员。

不带任何参数的构造函数称为无参数构造函数。每当使用 `new` 运算符实例化对象且不为 `new` 提供任何参数时，会调用无参数构造函数。C# 12 引入了主构造函数。主构造函数指定为初始化新对象而必须提供的参数。

除非类是静态的，否则 C# 编译器将为无构造函数的类提供一个公共的无参数构造函数，以便该类可以实例化。

通过将构造函数设置为私有构造函数，可以阻止类被实例化。

结构类型的构造函数类似于类构造函数。使用 `new` 实例化结构类型时，将调用构造函数。将 `struct` 设置为其 `default` 值时，运行时会将结构中的所有内存初始化为 0。在 C# 10 之前，`structs` 不能包含显式无参数构造函数，因为编译器会自动提供一个。

类和结构都可以定义采用参数的构造函数，包括主构造函数。必须通过 `new` 语句或 `base` 语句调用带参数的构造函数。类和结构还可以定义多个构造函数，并且二者均无需定义无参数构造函数。

构造函数可以使用 `base` 关键字调用基类的构造函数。例如：

```c#
public class Manager : Employee
{
    public Manager(int annualSalary)
        : base(annualSalary)
    {
        //Add further instructions here.
    }
}
```

`base` 关键字可以带参数使用，也可以不带参数使用。构造函数的任何参数都可用作 `base` 的参数，或用作表达式的一部分。

在派生类中，如果不使用 `base` 关键字来显式调用基类构造函数，则将隐式调用无参数构造函数 (如果有)。下面的两个构造函数声明是等效的：

```c#
public Manager(int initialData)
{
    //Add further instructions here.
}
```

```c#
public Manager(int initialData)
    : base()
{
    //Add further instructions here.
}
```

如果基类没有提供无参数构造函数，派生类必须使用 `base` 显式调用基类构造函数。

构造函数可以使用 [`this`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/this) 关键字调用同一对象中的另一构造函数。和 `base` 一样，`this` 可带参数使用也可不带参数使用，构造函数中的任何参数都可用作 `this` 的参数，或者用作表达式的一部分。

可以将构造函数标记为 `public`、`private`、`protected`、`internal`、`protected internal` 或 `private protected`，这些访问修饰符定义类的用户构造该类的方式。

可以使用 `static` 关键字将构造函数声明为静态构造函数。在访问任何静态字段之前，都将自动调用静态构造函数，它们用于初始化静态类成员。

#### 3.1.9.3 静态构造函数

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/constructors#static-constructors
>
> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-constructors

实例构造函数是实现初始化类实例所需执行的操作的成员，而静态构造函数是实现在首次加载类时初始化类本身所需执行的操作的成员。

实例构造函数用于创建一个新对象。类或结构也可以具有静态构造函数，该静态构造函数初始化类型的静态成员。静态构造函数是无参数构造函数。如果未提供静态构造函数来初始化静态字段，C# 编译器会将静态字段初始化为其默认值，如 [C# 类型的默认值](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/default-values) 中所示。

静态构造函数用于初始化任何静态数据，或者执行仅需执行一次的特定操作。将在创建第一个实例或者引用任何静态成员之前自动调用静态构造函数。静态构造函数最多调用一次。

静态初始化包含多个操作，这些操作按以下顺序进行：

1. 将静态字段设置为 0。这通常由运行时来完成。
2. 静态字段初始值设定项运行。派生程度最高的类型的静态字段初始值设定项运行。
3. 基类型静态字段初始值设定项运行。以直接基类型开始，从每个基类型到 `System.Object` 的静态字段初始值设定项。
4. 基静态构造函数运行。以 Object.Object 开始，从每个基类到直接基类的任何静态构造函数。
5. 静态构造函数运行。该类型的静态构造函数运行。

[模块初始化表达式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/attributes/general#moduleinitializer-attribute) 可用于替代静态构造函数。

静态构造函数具有以下属性：

- 静态构造函数不使用访问修饰符，也不具有参数。
- 类或结构只能有一个静态构造函数。
- 静态构造函数不能继承或重载。
- 静态构造函数不能直接调用，并且仅应由 CLR 调用。静态构造函数会自动调用。
- 用户无法控制在程序中执行静态构造函数的时间。
- 静态构造函数是自动调用的。它在创建第一个实例或引用该类 (不是其基类) 中声明的任何静态成员之前初始化类。静态构造函数在实例构造函数之前运行。如果静态构造函数类中存在静态字段变量初始值设定项，它们将以在类声明中显示的文本顺序执行。初始值设定项紧接着执行静态构造函数之前运行。
- 如果未提供静态构造函数来初始化静态字段，会将所有静态字段初始化为其默认值。
- 如果静态构造函数引发异常，运行时将不会再次调用该函数，并且类型在应用程序域的生存期内将保持未初始化。大多数情况下，当静态构造函数无法实例化一个类型时，或者当静态构造函数中发生未经处理的异常时，将引发 [TypeInitializationException](https://learn.microsoft.com/en-us/dotnet/api/system.typeinitializationexception) 异常。对于未在源代码中显式定义的静态构造函数，故障排除可能需要检查 IL 代码。
- 静态构造函数的存在将防止添加 [BeforeFieldInit](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.typeattributes#system-reflection-typeattributes-beforefieldinit) 类型特性。这将限制运行时优化。
- 声明为 `static readonly` 的字段可能仅被分配为其声明的一部分或在静态构造函数中。如果不需要显式静态构造函数，应在声明时初始化静态字段，而不是通过静态构造函数，以实现更好的运行时优化。
- 运行时在单个应用程序域中多次调用静态构造函数。该调用是基于特定类型的类在锁定区域中进行的。静态构造函数的主体中不需要其他锁定机制。为了避免死锁的风险，不应阻止静态构造函数和初始值设定项中的当前线程。例如，不要等待任务、线程、等待句柄或事件，不要获取锁定，也不要执行阻止并行操作，如并行循环、`Parallel.Invoke` 和并行 LINQ 查询。



### 3.1.10 终结器

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/finalizers

终结器 (以前称为 *析构器 (destructor)*) 用于在垃圾回收器回收类实例时执行任何必要的最终清理操作。在大多数情况下，通过使用 [System.Runtime.InteropServices.SafeHandle](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.safehandle) 或派生类包装任何非托管句柄，可以免去编写终结器的过程。

终结器具有以下特性：

- 无法在结构中定义终结器，终结器仅用于类。
- 一个类只能有一个终结器。
- 不能继承或重载终结器。
- 不能手动调用终结器。可以自动调用终结器。
- 终结器不使用修饰符或参数。

终结器隐式调用对象基类上的 [Finalize](https://learn.microsoft.com/en-us/dotnet/api/system.object.finalize)。因此，对终结器的调用会隐式转换为以下代码：

```c#
protected override void Finalize()
{
    try
    {
        // Cleanup statements...
    }
    finally
    {
        base.Finalize();
    }
}
```

这种设计意味着，对继承链 (从派生程度最高到派生程度最低) 中的所有实例以递归方式调用 `Finalize` 方法。

注意，不应使用空的终结器。如果类包含终结器，会在 `Finalize` 队列中创建一个条目。该队列由垃圾回收器处理。当 GC 处理队列时，它会调用每个终结器。不必要的终结器 (包括空的终结器、仅调用基类终结器的终结器，或者仅调用条件性发出的方法的终结器) 会导致不必要的性能损失。

程序员无法控制调用终结器的时机，因为这由垃圾回收器决定。垃圾回收器检查应用程序不再使用的对象，如果它认为某个对象符合终止条件，则调用终结器 (如果有)，并回收用来存储此对象的内存。可以通过调用 [`Collect`](https://learn.microsoft.com/en-us/dotnet/api/system.gc.collect) 强制进行垃圾回收，但多数情况下应避免此调用，因为它可能会造成性能问题。

对于终结器是否在应用程序终止过程中运行，这特定于每个 [.NET 的实现](https://learn.microsoft.com/en-us/dotnet/standard/glossary#implementation-of-net)。在应用程序终止时，.NET Framework 会尽一切合理努力为尚未被执行垃圾回收的对象调用终结器，除非此类清理操作已被禁止 (例如，通过调用库方法 `GC.SuppressFinalize`)。.NET 5 (包括 .NET Core) 及更高版本不会在应用程序终止过程中调用终结器。可在 GitHub 问题 [dotnet/csharpstandard #291](https://github.com/dotnet/csharpstandard/issues/291) 了解详细信息。

如果需要在应用程序退出时确保清理操作能够可靠地执行，可以为 [System.AppDomain.ProcessExit](https://learn.microsoft.com/en-us/dotnet/api/system.appdomain.processexit#system-appdomain-processexit) 事件注册一个处理程序。该处理程序将确保在应用程序退出之前，为所有需要执行清理操作的对象调用 [IDisposable.Dispose()](https://learn.microsoft.com/en-us/dotnet/api/system.idisposable.dispose#system-idisposable-dispose) 或 [IAsyncDisposable.DisposeAsync()](https://learn.microsoft.com/en-us/dotnet/api/system.iasyncdisposable.disposeasync#system-iasyncdisposable-disposeasync)。由于不能直接调用 Finalize，而且也不能保证垃圾回收器在退出前调用了所有终结器，所以必须使用 `Dispose` 或 `DisposeAsync` 来确保资源得到释放。

#### 3.1.10.1 使用终结器释放资源

一般来说，对于开发人员，C# 所需的内存管理比不面向带垃圾回收的运行时的语言要少。这是因为 .NET 垃圾回收器会隐式管理对象的内存分配和释放。但是，如果应用程序封装非托管的资源，例如窗口、文件和网络连接，则应使用终结器释放这些资源。当对象符合终止条件时，垃圾回收器会运行对象的 `Finalize` 方法。

#### 3.1.10.2 显式释放资源

如果应用程序正在使用昂贵的外部资源，建议在垃圾回收器释放对象前显式释放资源。如果要释放资源，应从 [IDisposable](https://learn.microsoft.com/en-us/dotnet/api/system.idisposable) 接口实现 `Dispose` 方法，对对象执行必要的清理。这样可大大提高应用程序的性能。如果调用 `Dispose` 方法失败，那么即使拥有对资源的显式控制，终结器也会成为清除资源的一个保障。



### 3.1.11 抽象类、密封类及类成员

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/abstract-and-sealed-classes-and-class-members

使用 [`abstract`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/abstract) 关键字可以创建不完整且必须在派生类中实现的类和类成员。

使用 [`sealed`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/sealed) 关键字可以防止继承以前标记为 [`virtual`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/virtual) 的类或某些类成员。

#### 3.1.11.1 抽象类和类成员

通过在类定义前面放置关键字 `abstract`，可以将类声明为抽象类。

抽象类不能实例化。抽象类的用途是提供一个可供多个派生类共享的通用基类定义。例如，类库可以定义一个抽象类，将其用作多个类库函数的参数，并要求使用该库的程序员通过创建派生类来提供自己的类实现。

抽象类也可以定义抽象方法，可通过将关键字 `abstract` 添加到方法的返回类型的前面来实现。例如：

```c#
public abstract class A
{
    public abstract void DoWork(int i);
}
```

抽象方法没有实现，所以方法定义后面是分号，而不是常规的方法块。抽象类的派生类必须实现所有抽象方法。当抽象类从基类继承虚方法时，抽象类可以使用抽象方法重写该虚方法。例如：

```c#
// compile with: -target:library
public class D
{
    public virtual void DoWork(int i)
    {
        // Original implementation.
    }
}

public abstract class E : D
{
    public abstract override void DoWork(int i);
}

public class F : E
{
    public override void DoWork(int i)
    {
        // New implementation.
    }
}
```

如果将 `virtual` 方法声明为 `abstract`，则该方法对于从抽象类继承的所有类而言仍然是虚方法。继承抽象方法的类无法访问方法的原始实现，因此在上面的代码示例中，类 F 上的 `DoWork` 无法调用类 D 上的 `DoWork`。通过这种方式，抽象类可强制派生类向虚拟方法提供新的方法实现。

#### 3.1.11.2 密封类和类成员

通过在类定义前面放置关键字 `sealed`，可以将类声明为密封类。

密封类不能用作基类。因此，它也不能是抽象类。

密封类禁止派生。由于密封类从不用作基类，所以有些运行时优化可以略微提高密封类成员的调用速度。

在对基类的虚成员进行重写的派生类上，方法、索引器、属性或事件可以将该成员声明为密封成员。在用于以后的派生类时，这将取消成员的虚效果。可以通过在类成员声明中将 `sealed` 关键字置于 [`override`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/override) 关键字前面来实现。例如：

```c#
public class D : C
{
    public sealed override void DoWork() { }
}
```



### 3.1.12 静态类和静态类成员

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members

[`static`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/static) 类基本上与非静态类相同，但存在一个差别：静态类无法实例化。意思就是，无法使用 `new` 运算符创建类类型的变量。由于不存在任何实例变量，因此可以使用类名本身访问静态类的成员。

静态类可以用作只对输入参数进行操作并且不必获取或设置任何内部实例字段的方法集的方便容器。

与所有类类型的情况一样，加载引用静态类的程序时，.NET 运行时会加载该类的类型信息。程序无法确切指定类被加载的时间。但是，可以保证它会加载，并在程序中首次引用类之前初始化其字段并调用其静态构造函数。静态构造函数只调用一次，在程序所驻留的应用程序域的生命周期内，静态类会保留在内存中。

静态类具有以下特性：

- 只包含静态成员。
- 无法实例化。
- 会进行密封。
- 不能包含实例构造函数。

因此，创建静态类基本上与创建只包含静态成员和私有构造函数的类相同。私有构造函数可防止类进行实例化。使用静态类的优点是编译器可以进行检查，以确保不会意外地添加任何实例成员。编译器会保证此类的实例无法创建。

静态类会进行密封，因此不能继承。它们不能继承任何类或接口 (除了 `Object`)。静态类不能包含实例构造函数。但是，它们可以包含静态构造函数。如果非静态类包含了需要进行有意义的初始化的静态成员，则它也应该定义一个静态构造函数。

#### 3.1.12.1 静态成员

非静态类可以包含静态方法、字段、属性或事件。即使不存在类的任何实例，也可对类调用静态成员。静态成员始终按类名 (而不是实例名称) 进行访问。静态成员只有一个副本存在 (与创建的类的实例数无关)。静态方法和属性无法在其包含类型中访问非静态字段和事件，它们无法访问任何对象的实例变量，除非在方法参数中显式传递。

更典型的做法是声明具有一些静态成员的非静态类 (而不是将整个类都声明为静态)。静态字段的两个常见用途是保留实例化的对象数的计数，或是存储必须在所有实例间共享的值。

静态方法可以进行重载，但不能进行重写，因为它们属于类，而不属于类的任何实例。

虽然字段不能声明为 `static const`，但是 `const` 字段在其行为方面本质上是静态的。它属于类型，而不属于类型的实例。因此，可以使用用于静态字段的相同 `ClassName.MemberName` 表示法来访问 `const` 字段，而无需进行对象实例化。

C# 不支持静态局部变量 (即在方法范围中声明的变量)。

可以在成员的返回类型之前使用 `static` 关键字来声明静态类成员。

在首次访问静态成员之前以及在调用构造函数 (如果有) 之前，会初始化静态成员。如果要访问静态类成员，应使用类的名称指定成员的位置。

如果类包含静态字段，则提供在类加载时初始化它们的静态构造函数。

对静态方法的调用会采用 CIL 生成调用指令，而对实例方法的调用会生成 `callvirt` 指令，该指令还会检查是否存在 null 对象引用。但是在大多数时候，两者之间的性能差异并不显著。



## 3.2 表达式

表达式是在操作数和运算符的基础之上构造而成。表达式的运算符指明了向操作数应用的运算。运算符包括 `+`、`-`、`*`、`/` 和 `new` 等，而操作数包括文本、字段、局部变量和表达式等。

如果某个表达式包含多个运算符，则运算符的优先顺序控制各个运算符的计算顺序。例如，表达式 `x + y * z` 相当于计算 `x + (y * z)`，因为 `*` 运算符的优先级高于 `+` 运算符。

如果操作数两边的两个运算符的优先级相同，那么运算符的结合性决定了运算的执行顺序：

- 除了赋值运算符和 null 合并运算符之外，所有二元运算符均为左结合运算符，即从左向右执行运算。例如，`x + y + z` 将计算为 `(x + y) + z`。
- 赋值运算符、null 合并 `??` 和 `??=` 运算符和条件运算符 `?:` 为右结合运算符，即从右向左执行运算。例如，`x = y = z` 将计算为 `x = (y = z)`。

可以使用括号控制优先级和结合性。例如，`x + y * z` 先计算 `y` 乘 `z`，并将结果与 `x` 相加，而 `(x + y) * z` 则先计算 `x` 加 `y`，然后将结果与 `z` 相乘。



## 3.3 语句

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/statements

程序执行的操作采用语句表达。常见的操作包括声明变量、赋值、调用方法、循环访问集合，以及根据给定条件分支到一个或另一个代码块。语句在程序中的执行顺序称为 "控制流" 或者 "执行流"。根据程序对运行时所收到的输入的响应，在程序每次运行时控制流可能有所不同。

语句可以是以分号结尾的单行代码，也可以是语句块中的一系列单行语句。语句块括在括号 `{}` 中，并且可以包含嵌套块。



### 3.3.1 语句的类型

C# 中的各种语句类型及其关联的关键字如下表所示：

| **类别**                                                     | **C# 关键字/说明**                                           |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [声明语句](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/statements#declaration-statements) | 声明语句引入新的变量或常量。变量声明可以选择为变量赋值。在常量声明中必须赋值。 |
| [表达式语句](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/statements#expression-statements) | 用于计算值的表达式语句必须在变量中存储该值。                 |
| 选择语句                                                     | 选择语句用于根据一个或多个指定条件分支到不同的代码段，其中包括：<br />[if](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/selection-statements#the-if-statement)<br />[switch](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/selection-statements#the-switch-statement) |
| 迭代语句                                                     | 迭代语句用于遍历集合，或者重复执行同一组语句直到满足指定的条件，其中包括：<br />[do](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/iteration-statements#the-do-statement)<br />[for](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/iteration-statements#the-for-statement)<br />[foreach](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/iteration-statements#the-foreach-statement)<br />[while](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/iteration-statements#the-while-statement) |
| 跳转语句                                                     | 跳转语句将控制转移给另一代码段，其中包括：<br />[break](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/jump-statements#the-break-statement)<br />[continue](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/jump-statements#the-continue-statement)<br />[goto](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/jump-statements#the-goto-statement)<br />[return](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/jump-statements#the-return-statement)<br />[yield](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/yield) |
| 异常处理语句                                                 | 异常处理语句用于从运行时发生的异常情况正常恢复，其中包括：<br />[throw](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/exception-handling-statements#the-throw-statement)<br />[try-catch](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/exception-handling-statements#the-try-catch-statement)<br />[try-finally](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/exception-handling-statements#the-try-finally-statement)<br />[try-catch-finally](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/exception-handling-statements#the-try-catch-finally-statement) |
| [`checked` 和 `unchecked` 语句](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/checked-and-unchecked) | `checked` 和 `unchecked` 语句用于指定将结果存储在变量中、但该变量过小而不能容纳结果值时，是否允许整型数值运算导致溢出。 |
| `await` 语句                                                 | 如果用 [`async`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/async) 修饰符标记方法，则可以使用该方法中的 [`await`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/await) 运算符。在控制到达异步方法的 `await` 表达式时，控制将返回到调用方，该方法中的进程将挂起，直到等待的任务完成为止。任务完成后，可以在方法中恢复执行。 |
| `yield return` 语句                                          | 迭代器对集合执行自定义迭代，如列表或数组。迭代器使用 [`yield return`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/yield) 语句返回元素，每次返回一个。到达 `yield return` 语句时，会记住当前在代码中的位置。下次调用迭代器时，将从该位置重新开始执行。 |
| `fixed` 语句                                                 | fixed 语句禁止垃圾回收器重定位可移动的变量。可在 [fixed](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/fixed) 了解详细信息。 |
| `lock` 语句                                                  | lock 语句用于限制一次仅允许一个线程访问代码块。可在 [lock](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/lock) 了解详细信息。 |
| 带标签的语句                                                 | 可以为语句指定一个标签，然后使用 [`goto`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/jump-statements#the-goto-statement) 关键字跳转到该带标签的语句。 |
| [空语句](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/statements#the-empty-statement) | 空语句只含一个分号。不执行任何操作，可以在需要语句但不需要执行任何操作的地方使用。 |

# 4. 面向对象的编程

> https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/types
>
> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/

C# 作为 **面向对象 (Object-oriented)** 的语言，支持 **封装 (Encapsulation)**、**继承 (Inheritance)** 和 **多态性 (Polymorphism)** 这些概念。

封装有时称为面向对象编程的第一支柱或原则。类或结构可以指定自己的每个成员对外部代码的可访问性。可以隐藏不打算在类或程序集外部使用的方法和变量，以限制编码错误或者恶意攻击发生的可能性。

类 (但不包括结构) 支持继承的概念。派生自另一个类 (称为基类) 的类自动包含基类的所有 `public`、`protected` 和 `internal` 成员 (构造函数和终结器除外)。

可以将类声明为 `abstract`，意味着类的一个或多个方法没有实现代码。尽管抽象类无法直接实例化，但可以作为提供缺少实现代码的其他类的基类。类还可以声明为 `sealed`，以阻止其他类继承。



## 4.1 对象

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/objects

类或结构定义的作用类似于蓝图，指定该类型可以进行哪些操作。从本质上说，对象是按照此蓝图分配和配置的内存块。程序可以创建同一个类的多个对象。对象也称为实例，可以存储在命名变量中，也可以存储在数组或集合中。使用这些变量来调用对象方法及访问对象公共属性的代码称为客户端代码。在 C# 之类的面向对象语言中，一个典型的程序由动态交互的多个对象组成。



### 4.1.1 结构实例与类实例

由于类是引用类型，因此类对象的变量引用该对象在托管堆上的地址。如果将同一类型的第二个变量分配给第一个变量，则两个变量都引用该地址的对象。

类的实例是使用 [`new` 运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/new-operator) 创建的。

由于结构是值类型，因此结构对象的变量具有整个对象的副本。结构的实例也可以使用 `new` 运算符来创建，但这不是必需的，如下面的示例所示：

```c#
using System;

namespace Example
{
    public struct Person
    {
        public string Name;
        public int Age;
        public Person(string name, int age)
        {
            Name = name;
            Age = age;
        }
    }

    public class Application
    {
        static void Main()
        {
            // Create  struct instance and initialize by using "new".
            // Memory is allocated on thread stack.
            Person p1 = new Person("Alex", 9);
            Console.WriteLine("p1 Name = {0} Age = {1}", p1.Name, p1.Age);

            // Create  new struct object. Note that  struct can be initialized
            // without using "new".
            Person p2 = p1;

            // Assign values to p2 members.
            p2.Name = "Spencer";
            p2.Age = 7;
            Console.WriteLine("p2 Name = {0} Age = {1}", p2.Name, p2.Age);

            // p1 values remain unchanged because p2 is  copy.
            Console.WriteLine("p1 Name = {0} Age = {1}", p1.Name, p1.Age);
        }
    }
    /*
        Output:
        p1 Name = Alex Age = 9
        p2 Name = Spencer Age = 7
        p1 Name = Alex Age = 9
    */
}
```

在上面的示例中，`p1` 和 `p2` 的内存在线程栈上进行分配，该内存随声明它的类型或方法一起回收。这就是在赋值时复制结构的一个原因。相比之下，当对类实例对象的所有引用都超出范围时，为该类实例分配的内存将由 CLR 自动回收 (垃圾回收)。无法像在 C++ 中那样明确地销毁类对象。

CLR 中高度优化了托管堆上内存的分配和释放。在大多数情况下，在堆上分配类实例与在堆栈上分配结构实例在性能成本上没有显著的差别。



### 4.1.2 对象标识与值相等性

在比较两个对象是否相等时，首先必须明确是想知道两个变量是否表示内存中的同一对象，还是想知道这两个对象的一个或多个字段的值是否相等。如果要对值进行比较，则必须考虑这两个对象是值类型 (结构) 的实例，还是引用类型 (类、委托、数组) 的实例。

- 如果要确定两个类实例是否引用内存中的同一位置 (这意味着它们具有相同的标识)，可以使用静态的 [`Object.Equals`](https://learn.microsoft.com/en-us/dotnet/api/system.object.equals) 方法 (System.Object 是所有值类型和引用类型的隐式基类，其中包括用户定义的结构和类)。
- 如果要确定两个结构实例中的实例字段是否具有相同的值，可以使用 [`ValueType.Equals`](https://learn.microsoft.com/en-us/dotnet/api/system.valuetype.equals) 方法。由于所有结构都隐式继承自 `System.ValueType`，因此可以直接在对象上调用该方法。`System.ValueType` 的 `Equals` 实现在某些情况下使用装箱和反射。
- 如果要确定两个类实例中字段的值是否相等，可以使用 `Object.Equals` 方法或 [`==` 运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/equality-operators#equality-operator-)。但是，只有类通过重写或重载提供关于那种类型对象的 "相等" 含义的自定义时，才能使用它们。类也可能实现 [`IEquatable<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.iequatable-1) 接口或者 [`IEqualityComparer<t>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.iequalitycomparer-1) 接口，这两个接口都提供可用于测试值相等性的方法。

#### 4.1.2.1 相等性比较

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons

有时需要比较两个值是否相等。在某些情况下，需要测试的是 *值相等性 (value equality)*，也称为 *等效性 (equivalence)*，这意味着两个变量包含的值相等。在其他情况下，必须确定两个变量是否引用内存中的同一基础对象，这种类型的相等性称为 *引用相等性 (reference equality)* 或 *标识 (identity)*。

##### 4.1.2.1.1 引用相等性

引用相等性指两个对象引用均引用同一基础对象。

可以使用 [`Object.ReferenceEquals`](https://learn.microsoft.com/en-us/dotnet/api/system.object.referenceequals) 方法来确定两个引用是否引用同一对象。

引用相等性的概念仅适用于引用类型。由于在将值类型的实例赋给变量时将产生值的副本，因此值类型对象无法具有引用相等性。因此，永远不会有两个未装箱结构引用内存中的同一位置。此外，如果使用 `Object.ReferenceEquals` 比较两个值类型，结果将始终为 `false`，即使对象中包含的值都相同也是如此。这是因为会将每个变量装箱到单独的对象实例中。

##### 4.1.2.1.2 值相等性

值相等性指两个对象包含相同的一个或多个值。对于基元值类型 (例如 `int` 或 `bool`)，可以直接使用 `==` 运算符测试值相等性。

对于大多数其他类型，针对值相等性的测试较为复杂，因为它需要用户了解类型对值相等性的定义方式。对于具有多个字段或属性的类和结构，值相等性的定义通常指所有字段或属性都具有相同的值。对记录来说，值相等性是指如果记录类型的两个变量类型相匹配，且所有属性和字段值都一致，那么记录类型的两个变量是相等的。

但是，并不要求类型中的所有字段都相等，也可以只需子集相等即可。比较不具所有权的类型时，应确保明确了解相等性对于该类型是如何定义的。

##### 4.1.2.1.3 浮点值的值相等性

由于二进制计算机上的浮点算法不精确，因此浮点值 (`double` 和 `float`) 的相等比较会出现问题。可在 [System.Double](https://learn.microsoft.com/en-us/dotnet/api/system.double) 了解相关信息。



## 4.2 继承

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/inheritance

继承 (以及封装和多态性) 是面向对象编程的三个主要特征之一。通过继承，可以创建新类，以便重用、扩展和修改在其他类中定义的行为。其成员被继承的类称为 *基类 (base class)*，继承这些成员的类称为 *派生类 (derived class)*。派生类只能有一个直接基类。但是，继承是可传递的。如果 `ClassC` 派生自 `ClassB`，并且 `ClassB` 派生自 `ClassA`，则 `ClassC` 将继承在 `ClassB` 和 `ClassA` 中声明的成员。

从概念上讲，派生类是基类的专门化。例如，如果有一个基类 `Animal`，则可以有一个名为 `Mammal` 的派生类，以及另一个名为 `Reptile` 的派生类。`Mammal` 是 `Animal`，`Reptile` 也是 `Animal`，但每个派生类表示基类的不同专门化。

接口声明可以为其成员定义默认实现。这些实现通过派生接口和实现这些接口的类来继承。

在定义要从其他类派生的类时，派生类会隐式获得基类的所有成员 (除了其构造函数和终结器)。派生类可以重用基类中的代码，而无需重新实现。可以在派生类中添加更多成员。派生类扩展了基类的功能。



### 4.2.1 抽象方法和虚方法

基类将方法声明为 `virtual` 时，派生类可以使用其自己的实现重写该方法，重写将用到关键字 `override`。

如果基类将成员声明为 `abstract`，则必须在直接继承自该类的任何非抽象类中重写该方法。如果派生类本身是抽象的，则它会继承抽象成员而不会实现它们。

抽象和虚拟成员是多态性 (面向对象编程的第二个主要特征) 的基础。



### 4.2.2 抽象基类

使用关键字 `abstract` 定义的抽象类，不能通过使用 new 运算符来直接实例化。只有当一个新类派生自该类时，才能使用抽象类。

抽象类可以包含一个或多个本身声明为 `abstract` 的方法签名。这些签名指定参数和返回值，但没有任何实现 (即没有方法体)。

抽象类不必包含抽象成员。但是，如果类包含抽象成员，则类本身必须声明为抽象。本身不抽象的派生类必须为来自抽象基类的任何抽象方法提供实现。



### 4.2.3 接口

接口是定义一组成员的引用类型。实现该接口的所有类和结构都必须实现这组成员。接口可以为其中任意成员或全部成员定义默认实现。类可以实现多个接口，即使它只能派生自单个直接基类。

接口用于为类定义特定功能，这些功能不一定具有 "is a" 关系。例如，`System.IEquatable<T>` 接口可以由任何类或结构实现，以确定该类型的两个对象是否等效 (由该类型定义等效性)。`System.IEquatable<T>` 不表示基类和派生类之间存在的同一种 "is a" 关系。



### 4.2.4 防止进一步派生

类可以通过将自己或者自己的成员声明为 `sealed`，来防止其他类继承自该类或者继承自其任何成员。



### 4.2.5 派生类隐藏基类的成员

派生类可以通过使用相同名称和签名声明成员来隐藏基类成员。

`new` 修饰符可以用于显式指示成员不应作为基类成员的重写。使用 `new` 不是必需的，但如果未使用 `new`，则会产生编译器警告。



## 4.3 多态性

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/object-oriented/polymorphism

多态性常被视为自封装和继承之后，面向对象编程的第三个支柱。*Polymorphism (多态性)* 是一个希腊词，指的是 "多种形态"。多态性具有两个截然不同的方面：

- 在运行时，在方法参数和集合或数组等位置，派生类的对象可以作为基类的对象处理。在出现这种多态性时，该对象的声明类型不再与运行时类型相同。
- 基类可以定义并实现 `virtual` 方法，派生类可以重写这些方法，即派生类提供自己的定义和实现。在运行时，客户端代码调用该方法，CLR 查找对象的运行时类型，并调用虚方法的重写方法。可以在源代码中调用基类的方法，执行该方法的派生类版本。

在 C# 中，每个类型都是多态的，因为包括用户定义类型在内的所有类型都继承自 `System.Object`。



### 4.3.1 虚拟成员

当派生类从基类继承时，它包括基类的所有成员。基类中声明的所有行为都是派生类的一部分。这使派生类的对象能够被视为基类的对象。访问修饰符 (`public`、`protected`、`private` 等) 确定是否可以从派生类实现访问这些成员。通过虚拟方法，设计者可以选择不同的派生类行为：

- 派生类可以重写基类中的虚拟成员，并定义新行为。
- 派生类可以继承最接近的基类方法而不重写该方法，保留现有的行为，但允许进一步派生的类重写方法。
- 派生类可以定义这些成员的新的非虚拟实现，以隐藏基类实现。

仅当基类成员声明为 `virtual` 或 `abstract` 时，派生类才能重写基类成员。派生成员必须使用 `override` 关键字显式指示该方法将参与虚调用。

字段不能是虚拟的，只有方法、属性、事件和索引器才可以是虚拟的。当派生类重写某个虚拟成员时，即使该派生类的实例被当作基类的实例访问，也会调用该成员。

虚方法和属性允许派生类扩展基类，而无需使用方法的基类实现。接口提供了另一种方式来定义一个或多个方法，其实现留给派生类。



### 4.3.2 使用新成员隐藏基类成员

如果希望派生类具有与基类成员同名的成员，则可以使用 [`new`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/new-modifier) 关键字来隐藏基类成员。`new` 关键字放置在要替换的类成员的返回类型之前。

通过将派生类的实例强制转换为基类的实例，可以从客户端代码访问隐藏的基类成员。



### 4.3.3 阻止派生类重写虚拟成员

无论在虚拟成员和最初声明虚拟成员的类之间已声明了多少个类，虚拟成员都是虚拟的。如果类 `A` 声明了一个虚拟成员，类 `B` 从 `A` 派生，类 `C` 从类 `B` 派生，则不管类 `B` 是否为虚拟成员声明了重写，类 `C` 都会继承该虚拟成员，并可以重写它。例如：

```c#
public class A
{
    public virtual void DoWork() { }
}
public class B : A
{
    public override void DoWork() { }
}
```

派生类可以通过将重写声明为 `sealed` 来停止虚拟继承。停止继承需要在类成员声明中的 `override` 关键字前面放置 `sealed` 关键字。例如：

```c#
public class C : B
{
    public sealed override void DoWork() { }
}
```

在上面的示例中，方法 `DoWork` 对从 `C` 派生的任何类都不再是虚拟方法。即使它们转换为类型 `B` 或类型 `A`，它对于 `C` 的实例仍然是虚拟的。通过使用 `new` 关键字，密封的方法可以由派生类替换，如下面的示例所示：

```c#
public class D : C
{
    public new void DoWork() { }
}
```

在此情况下，如果在 `D` 中使用类型为 `D` 的变量调用 `DoWork`，被调用的将是新的 `DoWork`。但是，如果使用类型为 `C`、`B` 或 `A` 的变量访问 `D` 的实例，对 `DoWork` 的调用将遵循虚拟继承的规则，即把这些调用传送到类 `C` 的 `DoWork` 实现。



### 4.3.4 从派生类访问基类虚拟成员

已替换或重写某个方法或属性的派生类仍然可以使用 [`base`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/base) 关键字来访问基类的该方法或属性。

# 5. 模式匹配

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/functional/pattern-matching
>
> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns

*模式匹配 (Pattern matching)* 是一种测试表达式是否具有特定特征的技术。C# 模式匹配提供更简洁的语法，用于测试表达式并在表达式匹配时采取措施。`is` 表达式支持通过模式匹配来测试表达式并有条件地为该表达式的结果声明一个新变量。`switch` 表达式允许根据表达式的第一个匹配模式执行操作。这两个表达式支持丰富的模式词汇。

可以使用 `is` 表达式、`switch` 语句和 `switch` 表达式将输入表达式与任意数量的特征进行匹配。C# 支持多种模式，包括声明、类型、常量、关系、属性、列表、var 和弃元。可以使用布尔逻辑关键字 `and`、`or` 和 `not` 组合模式。

以下 C# 表达式和语句支持模式匹配：

- [is 表达式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/is)
- [switch 语句](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/selection-statements#the-switch-statement)
- [switch 表达式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/switch-expression)

在这些构造中，可以将输入表达式与以下任一模式进行匹配：

- [声明模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#declaration-and-type-patterns)：用于检查表达式的运行时类型，如果匹配成功，则将表达式结果分配给声明的变量。
- [类型模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#declaration-and-type-patterns)：用于检查表达式的运行时类型。
- [常量模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#constant-pattern)：用于测试表达式结果是否等于指定常量。
- [关系模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#relational-patterns)：用于将表达式结果与指定常量进行比较。
- [逻辑模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#logical-patterns)：用于测试表达式是否与模式的逻辑组合匹配。
- [属性模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#property-pattern)：用于测试表达式的属性或字段是否与嵌套模式匹配。
- [位置模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#positional-pattern)：用于解构表达式结果并测试结果值是否与嵌套模式匹配。
- [var 模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#var-pattern)：用于匹配任何表达式并将其结果分配给声明的变量。
- [弃元模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#discard-pattern)：用于匹配任何表达式。
- [列表模式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#list-patterns)：测试序列元素是否与相应的嵌套模式匹配。在 C# 11 中引入。

逻辑、属性、位置和列表模式都是递归模式。也就是说，它们可以包含嵌套模式。

# 6. 类型转换

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/types/casting-and-type-conversions

由于 C# 是在编译时静态类型化的，因此变量在声明后就无法再次声明，或者无法分配另一种类型的值，除非该类型可以隐式转换为变量的类型。

将类型的值复制到其他类型的变量或方法参数中，这样的操作称为 *类型转换 (type conversions)*。

在 C# 中可以执行以下几种类型转换：

- **隐式转换**

  由于这种转换始终会成功且不会导致数据丢失，因此无需使用任何特殊语法。例如：从较小整数类型到较大整数类型的转换，或者从派生类到基类的转换。

- **显式转换 (强制转换)**

  必须使用强制转换表达式才能执行显式转换。在转换中可能丢失信息时或在出于其他原因转换可能不成功时，必须进行强制转换。例如：从数值到精度较低或范围较小的类型的转换，或者从基类实例到派生类的转换。

- **用户定义的转换**

  用户定义的转换使用可自行定义的特殊方法，以支持在不具有基类和派生类关系的自定义类型之间实现显式和隐式转换。

- **使用帮助程序类进行转换**

  如果要在非兼容类型 (如整数和 [`System.DateTime`](https://learn.microsoft.com/en-us/dotnet/api/system.datetime) 对象，或者十六进制字符串和字节数组) 之间转换，可以使用 [`System.BitConverter`](https://learn.microsoft.com/en-us/dotnet/api/system.bitconverter) 类、[`System.Convert`](https://learn.microsoft.com/en-us/dotnet/api/system.convert) 类和内置数值类型的 `Parse` 方法 (如 [`Int32.Parse`](https://learn.microsoft.com/en-us/dotnet/api/system.int32.parse))。



## 6.1 隐式转换

对于内置数值类型，如果要存储的值无需截断或四舍五入即可适应变量，则可以进行隐式转换。

对于整型类型，这意味着源类型的范围是目标类型范围的正确子集。例如：`long` 类型的变量 (64 位整数) 能够存储 `int` (32 位整数) 可存储的任何值，因此 `int` 类型可以隐式转换为 `long` 类型。

可在 [隐式数值转换](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/numeric-conversions#implicit-numeric-conversions) 找到关于所有隐式数值转换的完整列表。

对于引用类型，隐式转换始终存在于从一个类转换为该类的任何一个直接或间接的基类或接口的情况。由于派生类始终包含基类的所有成员，因此不必使用任何特殊语法。



## 6.2 显式转换

如果进行转换可能会导致信息丢失，则编译器会要求执行显式转换，显式转换也称为 *强制转换 (cast)*。强制转换是显式告知编译器以下信息的一种方式：打算进行转换且知道可能会发生数据丢失，或者知道强制转换有可能在运行时失败。如果要执行强制转换，应在要转换的值或变量前面的括号中指定要强制转换到的类型。

可在 [显式数值转换](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/numeric-conversions#explicit-numeric-conversions) 找到关于支持的显式数值转换的完整列表。

对于引用类型，如果需要从基类型转换为派生类型，则必须进行显式强制转换。

引用类型之间的强制转换操作不会更改基础对象的运行时类型，它只更改用作对该对象引用的值的类型。



### 6.2.1 强制转换表达式

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast#cast-expression

形式为 `(T)E` 的强制转换表达式将表达式 `E` 的结果显式转换为类型 `T`。如果不存在从类型 `E` 到类型 `T` 的显式转换，则发生编译时错误。

在运行时，显式转换可能不会成功，强制转换表达式可能会引发异常。



## 6.3 运行时的类型转换异常

在某些引用类型转换中，编译器无法确定强制转换是否会有效。正确进行编译的强制转换操作有可能在运行时失败，可能引发 [`InvalidCastException`](https://learn.microsoft.com/en-us/dotnet/api/system.invalidcastexception) 之类的异常。



## 6.4 用户定义的显式和隐式转换运算符

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/user-defined-conversion-operators

用户定义类型可以定义从一个类型到另一个类型的自定义隐式或显式转换。隐式转换无需调用特殊语法，并且可以在各种情况 (例如，在赋值和方法调用中) 下发生。预定义的 C# 隐式转换始终成功，且永远不会引发异常。用户定义隐式转换也应如此。如果自定义转换可能会引发异常或丢失信息，应将其定义为显式转换。

`is` 和 `as` 运算符不考虑用户定义的转换。使用强制转换表达式来调用用户定义的显式转换。

`operator` 和 `implicit` 或 `explicit` 关键字分别用于定义隐式转换或显式转换。定义转换的类型必须是该转换的源类型或目标类型。两种用户定义类型之间的转换可以在这两种类型中的任意一种中定义。



## 6.5 类型测试

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/type-testing-and-cast

C# 提供 `is`、`as`、`typeof` 运算符来执行类型检查或类型转换。

`is` 运算符用于检查表达式结果的运行时类型是否与给定类型兼容。`as` 运算符用于将表达式显式转换为给定类型 (如果其运行时类型与该类型兼容)。强制转换表达式执行到目标类型的显式转换。`typeof` 运算符用于获取某个类型的 `System.Type` 实例。



### 6.5.1 is 运算符

`is` 运算符检查表达式结果的运行时类型是否与给定类型兼容。`is` 运算符还会根据某个模式测试表达式结果。`is` 运算符不会考虑用户定义的转换。

具有类型测试 `is` 运算符的表达式具有以下形式：

```c#
E is T
```

其中 `E` 是返回一个值的表达式，`T` 是类型或类型参数的名称。`E` 不得为匿名方法或 Lambda 表达式。

如果表达式结果为非 null 并且满足以下任一条件，则 `is` 运算符将返回 `true`：

- 表达式结果的运行时类型为 `T`。
- 表达式结果的运行时类型派生自类型 `T`、实现接口 `T`，或者存在从其到 `T` 的另一种隐式引用转换。
- 表达式结果的运行时类型是基础类型为 `T` 且 `Nullable<T>.HasValue` 为 `true` 的可为 null 值类型。
- 存在从表达式结果的运行时类型到类型 `T` 的装箱或取消装箱转换。

#### 6.5.1.1 使用模式匹配进行类型测试

`is` 运算符可以根据某个模式测试表达式结果。



### 6.5.2 as 运算符

`as` 运算符将表达式结果显式转换为给定的引用或可为 null 值的类型。如果无法进行转换，则 `as` 运算符返回 `null`。与强制转换表达式不同，`as` 运算符永远不会引发异常。

`as` 运算符的表达式具有以下形式：

```c#
E as T
```

其中，`E` 为返回值的表达式，`T` 为类型或类型参数的名称。其与以下表达式生成相同的结果：

```c#
E is T ? (T)(E) : (T)null
```

这两种表达式的不同之处在于 `E` 只计算一次。

`as` 运算符仅考虑引用、可为 null、装箱和取消装箱转换。不能使用 `as` 运算符执行用户定义的转换。

需要将 `as` 表达式的结果与 `null` 进行比较，以检查转换是否成功。也可以使用 `is` 运算符测试转换是否成功，如果成功，则将其结果分配给新变量。



### 6.5.3 typeof 运算符

`typeof` 运算符用于获取某个类型的 `System.Type` 实例。`typeof` 运算符的实参必须是类型或类型形参的名称。

`typeof` 运算符的参数不能是需要元数据注释的类型。例如：

- `dynamic`
- `string?`
- 任何可为 null 的引用类型

这些类型不会直接表示在元数据中。这些类型包括描述基础类型的属性。在这两种情况下，都可以使用基础类型。可以使用 `object` 来代替 `dynamic`，可以使用 `string` 来代替 `string?`。

还可以使用具有未绑定泛型类型的 `typeof` 运算符。未绑定泛型类型的名称必须包含适当数量的逗号，且此数量小于类型参数的数量。例如：

```c#
Console.WriteLine(typeof(Dictionary<,>));
// Output:
// System.Collections.Generic.Dictionary`2[TKey,TValue]
```

表达式不能为 `typeof` 运算符的参数。如果要获取表达式结果的运行时类型的 `System.Type` 实例，应使用 [`Object.GetType`](https://learn.microsoft.com/en-us/dotnet/api/system.object.gettype) 方法。

#### 6.5.3.1 使用 typeof 运算符进行类型测试

可以使用 `typeof` 运算符来检查表达式结果的运行时类型是否与给定的类型完全匹配。下面示例显示了使用 `typeof` 运算符和 `is` 运算符执行的类型检查之间的差异：

```c#
public class Animal { }

public class Giraffe : Animal { }

public static class TypeOfExample
{
    public static void Main()
    {
        object b = new Giraffe();
        Console.WriteLine(b is Animal);  // output: True
        Console.WriteLine(b.GetType() == typeof(Animal));  // output: False

        Console.WriteLine(b is Giraffe);  // output: True
        Console.WriteLine(b.GetType() == typeof(Giraffe));  // output: True
    }
}
```



### 6.5.4 运算符的可重载性

`is`、`as` 和 `typeof` 运算符无法进行重载。

用户定义的类型不能重载 `()` 运算符，但可以定义可由强制转换表达式执行的自定义类型转换。

# 7. 装箱和取消装箱

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/types/boxing-and-unboxing

C# 采用统一的类型系统，因此任意类型的值都可视为 `object`。每种 C# 类型都直接或间接地派生自 `object` 类类型，而 `object` 是所有类型的最终基类。只需将值视为类型 `object`，即可将引用类型的值视为对象。

**装箱 (Boxing)** 是将值类型转换为 `object` 类型或由此值类型实现的任何接口类型的过程。CLR 对值类型进行装箱时，会将值包装在 `System.Object` 实例中并将其存储在托管堆中。**取消装箱 (Unboxing)** 将从对象中提取值类型。装箱是隐式的，而取消装箱是显式的。装箱和取消装箱的概念是类型系统 C# 统一视图的基础，其中任何类型的值都可以被视为一个对象。

通过执行装箱和取消装箱，可以将值类型的值视为对象。例如：

```c#
int i = 123;
object o = i;    // Boxing
int j = (int)o;  // Unboxing
```

在上面代码中，`int` 值被转换成 `object`，然后又恢复成 `int`。

当值类型的值分配给 `object` 对象引用时，会分配一个 "箱" 来保存此值。该箱是引用类型的实例，值会被复制到该箱。当 `object` 引用被显式转换成值类型时，将检查引用的 `object` 是否是具有正确值类型的箱。如果检查成功，则会将箱中的值复制到值类型。



## 7.1 性能

> https://learn.microsoft.com/en-us/dotnet/framework/performance/performance-tips#boxing-and-unboxing

相对于简单的赋值而言，装箱和取消装箱过程需要进行大量的计算。对值类型进行装箱时，必须分配并构造一个新对象。取消装箱所需的强制转换也需要进行大量的计算，只是程度较轻。

如果值类型必须被频繁装箱，那么在这些情况下最好避免使用值类型 (例如在 `System.Collections.ArrayList` 之类的非泛型集合类中)。可以通过使用泛型集合 (例如 `System.Collections.Generic.List`) 来避免装箱值类型。装箱和取消装箱的过程需要进行大量的计算。对值类型进行装箱时，必须创建一个全新的对象。这可能比简单的引用赋值用时最多长 20 倍。取消装箱的过程所需时间可达赋值操作的 4 倍。



## 7.2 装箱

装箱用于在垃圾回收堆中存储值类型。装箱是值类型到 `object` 类型或者此值类型所实现的任何接口类型的隐式转换。对值类型进行装箱会在堆中分配一个新的对象，并将值类型的值复制到新对象中，新创建的引用类型变量将持有对新对象的引用。

以下代码对变量 `i` 隐式应用了装箱操作：

```c#
int i = 123;
// Boxing copies the value of i into object o.
object o = i;
```

上面代码的结果是在堆上创建对象引用 `o`，而在堆上则引用 `int` 类型的值。该值是赋给变量 `i` 的值类型值的一个副本。以下装箱转换图说明了 `i` 和 `o` 这两个变量之间的差异：

![boxing-operation-i-o-variables](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/types/media/boxing-and-unboxing/boxing-operation-i-o-variables.gif)

还可以像下面的示例一样执行显式装箱，但显式装箱从来不是必需的：

```c#
int i = 123;
object o = (object)i;  // explicit boxing
```



## 7.3 取消装箱

取消装箱是从 `object` 类型到值类型或从接口类型到实现该接口的值类型的显式转换。

取消装箱操作包括：

- 检查对象实例，以确保它是给定值类型的装箱值。
- 将该值从实例复制到值类型变量中。

在下面代码中进行了装箱和取消装箱两种操作：

```c#
int i = 123;      // a value type
object o = i;     // boxing
int j = (int)o;   // unboxing
```

下图展示了上面代码的结果：

![unboxing-conversion-operation](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/types/media/boxing-and-unboxing/unboxing-conversion-operation.gif)

要在运行时成功取消装箱值类型，被取消装箱的项必须是对一个对象的引用，该对象是先前通过装箱该值类型的实例创建的。尝试取消装箱 `null` 会导致 `NullReferenceException`。尝试取消装箱对不兼容值类型的引用会导致 `InvalidCastException`。

# 8. 析构元组和其他类型

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/functional/deconstruct

元组提供了一种从方法调用中检索多个值的轻量级方法。但是，一旦检索到元组，就必须处理它的各个元素。按元素逐个操作比较麻烦，如下面示例所示，`QueryCityData` 方法返回一个三元组，并通过单独的操作将其每个元素分配给一个变量：

```c#
public class Example
{
    public static void Main()
    {
        var result = QueryCityData("New York City");

        var city = result.Item1;
        var pop = result.Item2;
        var size = result.Item3;

         // Do something with the data.
    }

    private static (string, int, double) QueryCityData(string name)
    {
        if (name == "New York City")
            return (name, 8175133, 468.48);

        return ("", 0, 0);
    }
}
```

从对象检索多个字段和属性值可能同样麻烦：必须按成员逐个将字段或属性值分配给变量。

可以在单个 **析构 (Deconstruct)** 操作中，从元组检索多个元素或者从对象检索多个字段、属性和计算值。如果要析构元组，应将其元素分配给各个变量。当析构对象时，应将选定的值分配给各个变量。



## 8.1 元组

C# 提供内置的元组析构支持，可在单个操作中解包一个元组中的所有项。用于析构元组的常规语法与用于定义元组的语法相似：将要向其分配元素的变量放在赋值语句左侧的括号中。例如，以下语句将四元组的元素分配给 4 个单独的变量：

```c#
var (name, address, city, zip) = contact.GetAddressInfo();
```

有 3 种方法可用于析构元组：

- 可以在括号内显式声明每个字段的类型。下面示例使用此方法来析构由 `QueryCityData` 方法返回的三元组：

  ```c#
  public static void Main()
  {
      (string city, int population, double area) = QueryCityData("New York City");
  
      // Do something with the data.
  }
  ```

- 可以使用 `var` 关键字，以便 C# 推断每个变量的类型。将 `var` 关键字放在括号外。下面示例在析构由 `QueryCityData` 方法返回的三元组时使用类型推断：

  ```c#
  public static void Main()
  {
      var (city, population, area) = QueryCityData("New York City");
  
      // Do something with the data.
  }
  ```

  还可以在括号内将 `var` 关键字单独与任一或全部变量的声明结合使用：

  ```c#
  public static void Main()
  {
      (string city, var population, var area) = QueryCityData("New York City");
  
      // Do something with the data.
  }
  ```

- 最后，可以将元组析构到已声明的变量中。

  ```c#
  public static void Main()
  {
      string city = "Raleigh";
      int population = 458880;
      double area = 144.8;
  
      (city, population, area) = QueryCityData("New York City");
  
      // Do something with the data.
  }
  ```

即使元组中的每个字段都具有相同的类型，也不能在括号外使用特定类型。这样做会生成编译器错误 CS8136："Deconstruction 'var (...)' form disallows a specific type for 'var'."。

必须将元组的每个元素分配给一个变量。如果省略任何元素，编译器将生成错误 CS8132："Can't deconstruct a tuple of 'x' elements into 'y' variables."。



## 8.2 使用弃元的元组元素

析构元组时，通常只需要关注某些元素的值。可以利用 C# 对 [弃元](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/functional/discards) 的支持，弃元是一种仅能写入的变量，且其值将被忽略。在赋值中，通过下划线字符 `_` 指定弃元。可以弃元任意数量的值，且均由单个弃元 `_` 表示。



## 8.3 用户定义类型

除了 [record](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/functional/deconstruct#record-types) 和 [DictionaryEntry](https://learn.microsoft.com/en-us/dotnet/api/system.collections.dictionaryentry.deconstruct) 类型，C# 不提供析构非元组类型的内置支持。但是，用户作为类、结构或接口的创建者，可通过实现一个或多个 `Deconstruct`方法来析构该类型的实例。该方法返回 `void`，且要析构的每个值由方法签名中的 `out` 参数指示。例如，下面的 `Person` 类的 `Deconstruct` 方法返回名字、中间名和姓氏：

```c#
public void Deconstruct(out string fname, out string mname, out string lname)
```

然后，可以使用与下面代码类似的赋值来析构名为 `p` 的 `Person` 类的实例：

```c#
var (fName, mName, lName) = p;
```

可以重载 `Deconstruct` 方法。

具有相同数量参数的多个 `Deconstruct` 方法是不明确的。在定义 `Deconstruct` 方法时，必须小心使用不同数量的参数或 "arity"。在重载解析过程中，无法区分具有相同数量参数的 `Deconstruct` 方法。



## 8.4 使用弃元的用户定义类型

就像使用元组一样，也可以使用弃元来忽略 `Deconstruct` 方法返回的选定项。每个弃元由一个名为 `_` 的变量定义，单个析构操作可包含多个弃元。



## 8.5 用户定义类型的扩展方法

如果没有创建类、结构或接口，仍可以通过实现一个或多个 `Deconstruct` 扩展方法来析构该类型的对象，以返回所需值。



## 8.6 系统类型的扩展方法

为了方便起见，某些系统类型提供 `Deconstruct` 方法。例如，[`System.Collections.Generic.KeyValuePair<TKey,TValue>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.keyvaluepair-2) 类型提供此功能。当迭代 [`System.Collections.Generic.Dictionary<TKey,TValue>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2) 时，每个元素都是 `KeyValuePair<TKey, TValue>`，并且可以析构。



## 8.7 record 类型

使用两个或多个位置参数声明记录类型时，编译器将为 `record` 声明中的每个位置参数创建一个带有 `out` 参数的 `Deconstruct` 方法。

# 9. 异常和异常处理

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/exceptions/

C# 语言的异常处理功能有助于处理在程序运行期间发生的任何意外或异常情况。异常处理功能使用 `try`、`catch` 和 `finally` 关键字来尝试执行可能失败的操作、在确定合理的情况下处理故障，以及在事后清除资源。CLR、.NET、第三方库、应用程序代码都可生成异常。异常是使用 `throw` 关键字创建而成。

在许多情况下，异常并不是由代码直接调用的方法抛出，而是由调用堆栈中再往下的另一方法抛出。如果发生这种异常，CLR 会展开堆栈，同时针对特定异常类型查找包含 `catch` 代码块的方法，并执行它找到的首个此类 `catch` 代码块。如果在调用堆栈中找不到相应的 `catch` 代码块，将会终止进程并向用户显示消息。

异常具有以下属性：

- 异常是最终全都派生自 `System.Exception` 的类型。
- 在可能抛出异常的语句周围使用 `try` 代码块。
- 在 `try` 代码块中出现异常后，控制流会跳转到调用堆栈中任意位置上的首个相关异常处理程序。在 C# 中，`catch` 关键字用于定义异常处理程序。
- 如果给定的异常没有对应的异常处理程序，那么程序会停止执行，并显示错误消息。
- 除非可以处理异常并让应用程序一直处于已知状态，否则不捕获异常。如果捕获了 `System.Exception`，应使用 `catch` 代码块末尾的 `throw` 关键字重新抛出异常。
- 如果 `catch` 代码块定义异常变量，可以用它来详细了解所发生的异常类型。
- 使用 `throw` 关键字，程序可以显式生成异常。
- 异常对象包含错误详细信息，如调用堆栈的状态和错误的文本说明。
- 即使引发异常，`finally` 代码块中的代码仍会执行。使用 `finally` 代码块可以释放资源。例如，关闭在 `try` 代码块中打开的任何流或文件。
- .NET 中的托管异常在 Win32 结构化异常处理机制的基础之上实现。



## 9.1 编译器生成的异常

> https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/exceptions/compiler-generated-exceptions

当基本操作失败时，.NET 运行时会自动引发一些异常。下表列出了这些异常及其错误条件：

| **异常**                                                     | **描述**                                                     |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [ArithmeticException](https://learn.microsoft.com/en-us/dotnet/api/system.arithmeticexception) | 算术运算期间出现的异常的基类，例如 `DivideByZeroException` 和 `OverflowException`。 |
| [ArrayTypeMismatchException](https://learn.microsoft.com/en-us/dotnet/api/system.arraytypemismatchexception) | 当数组无法存储给定元素时引发，因为元素的实际类型与数组的实际类型不兼容。 |
| [DivideByZeroException](https://learn.microsoft.com/en-us/dotnet/api/system.dividebyzeroexception) | 尝试将整数值除以 0 时引发。                                  |
| [IndexOutOfRangeException](https://learn.microsoft.com/en-us/dotnet/api/system.indexoutofrangeexception) | 当尝试对数组进行索引时，如果索引小于 0 或超出数组边界，则引发此异常。 |
| [InvalidCastException](https://learn.microsoft.com/en-us/dotnet/api/system.invalidcastexception) | 从基类型显式转换为接口或派生类型在运行时失败时引发。         |
| [NullReferenceException](https://learn.microsoft.com/en-us/dotnet/api/system.nullreferenceexception) | 尝试引用值为 `null` 的对象时引发。                           |
| [OutOfMemoryException](https://learn.microsoft.com/en-us/dotnet/api/system.outofmemoryexception) | 尝试使用 `new` 运算符分配内存失败时引发。此异常表示可用于 CLR 的内存已用尽。 |
| [OverflowException](https://learn.microsoft.com/en-us/dotnet/api/system.overflowexception) | 当 `checked` 上下文中的算术运算溢出时引发。                  |
| [StackOverflowException](https://learn.microsoft.com/en-us/dotnet/api/system.stackoverflowexception) | 当执行堆栈因有过多待处理的方法调用而耗尽时引发。通常表示非常深的递归或无限的递归。 |
| [TypeInitializationException](https://learn.microsoft.com/en-us/dotnet/api/system.typeinitializationexception) | 当静态构造函数引发异常并且没有兼容的 `catch` 子句来捕获异常时引发。 |

# 10. 集合

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/collections

.NET 运行时提供了许多集合类型，用于存储和管理相关对象的组。一些集合类型 (例如 [`System.Array`](https://learn.microsoft.com/en-us/dotnet/api/system.array)、[`System.Span<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.span-1) 和 [`System.Memory<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.memory-1)) 可使用 C# 语言识别。此外，类似 [`System.Collections.Generic.IEnumerable<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1) 的接口可使用枚举集合元素的语言来识别。

集合提供灵活的方式来使用对象组。可以按以下特征对不同的集合进行分类：

- **元素访问**：可以枚举每个集合以按顺序访问每个元素。一些集合可以通过 *索引 (index)* (元素在有序集合中的位置) 来访问元素。最常见的示例是 [`System.Collections.Generic.List<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.list-1)。其他集合可以按 *键 (key)* 来访问元素，其中 *值 (value)* 与单个键相关联。最常见的示例是 [`System.Collections.Generic.Dictionary<TKey,TValue>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2)。可以根据应用访问元素的方式在这些集合类型之间进行选择。
- **性能配置文件**：每个集合都有不同的性能配置文件，可用于添加元素、查找元素或移除元素等操作。可以根据应用中最常用的操作来选取集合类型。
- **动态增长和收缩**：大多数集合支持动态添加或移除元素。需要注意的是，`System.Array`、`System.Span<T>` 和 `System.Memory<T>` 不支持。

除了这些特征之外，运行时还提供专用集合，这些集合可阻止添加或移除元素，或修改集合的元素。其他专用集合为多线程应用中的并发访问提供安全性。

可以在 [.NET API 参考](https://learn.microsoft.com/en-us/dotnet/api/?term=collection) 中找到所有的集合类型。另外，还可以在 [常用集合类型](https://learn.microsoft.com/en-us/dotnet/standard/collections/commonly-used-collection-types) 和 [选择集合类](https://learn.microsoft.com/en-us/dotnet/standard/collections/selecting-a-collection-class) 了解更多信息。

数组由 `System.Array` 表示，并受 C# 语言语法支持。此语法为数组变量提供了更简洁的声明。

`System.Span<T>` 属于 `ref struct` 类型，它可以提供一系列元素的快照，而无需复制这些元素。编译器强制实施安全规则，以确保在它引用的序列不再存在于作用域内之后无法访问 `Span`。它用于许多 .NET API 以提高性能。`System.Memory<T>` 可在无法使用 `ref struct` 类型时提供类似的行为。

从 C# 12 开始，可以使用 [集合表达式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/collection-expressions) 初始化所有集合类型。



## 10.1 可索引集合

可索引集合是一个可以使用其索引访问每个元素的集合。索引是序列中在它之前的元素数。因此，按索引 `0` 引用的元素是第一个元素，索引 `1` 则是第二个元素，依此类推。`System.Collections.Generic.List<T>` 是最常见的可索引集合。



## 10.2 键值对集合

`System.Collections.Generic.Dictionary<TKey,TValue>` 是最常见的字典集合。使用字典集合，可通过使用每个元素的键访问集合中的元素。每次对字典的添加都包含一个值和与其关联的键。



## 10.3 迭代器

迭代器用于对集合执行自定义迭代。迭代器可以是一种方法，或者是一个 `get` 访问器。迭代器使用 `yield return` 语句返回集合的每一个元素，每次返回一个元素。

通过使用 `foreach` 语句调用迭代器。`foreach` 循环的每次迭代都会调用迭代器。迭代器中到达 `yield return` 语句时，会返回一个表达式，并保留当前在代码中的位置。下次调用迭代器时，将从该位置重新开始执行。



## 10.4 LINQ 和集合

可以使用 **语言集成查询 (Language-Integrated Query, LINQ)** 来访问集合。LINQ 查询提供筛选、排序和分组功能。可在 [LINQ](https://learn.microsoft.com/en-us/dotnet/csharp/linq/) 了解相关信息。



## 10.5 集合和数据结构

> https://learn.microsoft.com/en-us/dotnet/standard/collections/

相似的数据在作为集合而存储和操作时通常可以得到更高效地处理。可以使用 `System.Array` 类或者 [System.Collections](https://learn.microsoft.com/en-us/dotnet/api/system.collections)、[System.Collections.Generic](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic)、[System.Collections.Concurrent](https://learn.microsoft.com/en-us/dotnet/api/system.collections.concurrent) 和 [System.Collections.Immutable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable) 命名空间中的类来添加、删除和修改集合中的任一单个元素或某个范围的元素。

有两种主要的集合类型：泛型集合和非泛型集合。泛型集合在编译时是类型安全的。因此，泛型集合通常能提供更好的性能。构造泛型集合时，它们接受类型形参，并在向该集合添加项或从该集合删除项时无需在 `Object` 类型间来回转换。非泛型集合将项存储为 `Object`，需要强制转换。但是，也可能会看到在较旧的代码中有非泛型集合。

自 .NET Framework 4 起，System.Collections.Concurrent 命名空间中的集合可提供高效的线程安全操作，以便从多个线程访问集合项。System.Collections.Immutable 命名空间 ([NuGet 包](https://www.nuget.org/packages/System.Collections.Immutable)) 中的不可变集合类本质上就是线程安全的，因为操作在原始集合的副本上进行且不能修改原始集合。



### 10.5.1 选择集合

一般情况下，应使用泛型集合。下表介绍了一些常用的集合方案和可用于这些方案的集合类：

| **需求**                                                     | **泛型集合**                                                 | **非泛型集合**                                               | **线程安全或不可变集合**                                     |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 将项存储为键值对以通过键进行快速查找                         | [`Dictionary<TKey,TValue>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2) | [`Hashtable`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.hashtable) (根据键的哈希代码组织的键值对的集合) | [`ConcurrentDictionary<TKey,TValue>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.concurrent.concurrentdictionary-2)、[`ReadOnlyDictionary<TKey,TValue>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.objectmodel.readonlydictionary-2)、[`ImmutableDictionary<TKey,TValue>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable.immutabledictionary-2) |
| 按索引访问项                                                 | [`List<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.list-1) | [`Array`](https://learn.microsoft.com/en-us/dotnet/api/system.array)、[`ArrayList`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.arraylist) | [`ImmutableList<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable.immutablelist-1)、[`ImmutableArray`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable.immutablearray) |
| 使用项先进先出 (FIFO)                                        | [`Queue<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.queue-1) | [`Queue`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.queue) | [`ConcurrentQueue<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.concurrent.concurrentqueue-1)、[`ImmutableQueue<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable.immutablequeue-1) |
| 使用数据后进先出 (LIFO)                                      | [`Stack<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.stack-1) | [`Stack`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.stack) | [`ConcurrentStack<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.concurrent.concurrentstack-1)、[`ImmutableStack<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable.immutablestack-1) |
| 按顺序访问项                                                 | [`LinkedList<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.linkedlist-1) | 无建议                                                       | 无建议                                                       |
| 删除集合中的项或者向集合添加项时接收通知。(实现 [`INotifyPropertyChanged`](https://learn.microsoft.com/en-us/dotnet/api/system.componentmodel.inotifypropertychanged) 和 [`INotifyCollectionChanged`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.specialized.inotifycollectionchanged)) | [`ObservableCollection<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.objectmodel.observablecollection-1) | 无建议                                                       | 无建议                                                       |
| 已排序的集合                                                 | [`SortedList<TKey,TValue>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.sortedlist-2) | [`SortedList`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.sortedlist) | [`ImmutableSortedDictionary<TKey,TValue>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable.immutablesorteddictionary-2)、[`ImmutableSortedSet<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable.immutablesortedset-1) |
| 数学函数的一个集                                             | [`HashSet<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.hashset-1)、[`SortedSet<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.sortedset-1) | 无建议                                                       | [`ImmutableHashSet<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable.immutablehashset-1)、[`ImmutableSortedSet<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable.immutablesortedset-1) |



### 10.5.2 集合的算法复杂性

选择集合类时，应该考虑可能牺牲的性能。使用下表来参考各种可变集合类型在算法复杂性方面与其对应不可变对应项的比较情况。不可变的集合类型性能通常较低，但却提供了不可变性，这通常是一种非常有效的优点。

| **可变**                  | **摊销**   | **最坏情况**                    | **不可变**                         | **复杂度** |
| :------------------------ | :--------- | :------------------------------ | :--------------------------------- | :--------- |
| `Stack<T>.Push`           | O(1)       | O(`n`)                          | `ImmutableStack<T>.Push`           | O(1)       |
| `Queue<T>.Enqueue`        | O(1)       | O(`n`)                          | `ImmutableQueue<T>.Enqueue`        | O(1)       |
| `List<T>.Add`             | O(1)       | O(`n`)                          | `ImmutableList<T>.Add`             | O(log `n`) |
| `List<T>.Item[Int32]`     | O(1)       | O(1)                            | `ImmutableList<T>.Item[Int32]`     | O(log `n`) |
| `List<T>.Enumerator`      | O(`n`)     | O(`n`)                          | `ImmutableList<T>.Enumerator`      | O(`n`)     |
| `HashSet<T>.Add`, lookup  | O(1)       | O(`n`)                          | `ImmutableHashSet<T>.Add`          | O(log `n`) |
| `SortedSet<T>.Add`        | O(log `n`) | O(`n`)                          | `ImmutableSortedSet<T>.Add`        | O(log `n`) |
| `Dictionary<T>.Add`       | O(1)       | O(`n`)                          | `ImmutableDictionary<T>.Add`       | O(log `n`) |
| `Dictionary<T>` lookup    | O(1)       | O(1)，或者从严格意义上为 O(`n`) | `ImmutableDictionary<T>` lookup    | O(log `n`) |
| `SortedDictionary<T>.Add` | O(log `n`) | O(`n` log `n`)                  | `ImmutableSortedDictionary<T>.Add` | O(log `n`) |

可以使用 `for` 循环或 `foreach` 循环来有效地枚举 `List<T>`。但是，由于其索引器的 O(log `n`) 时间，`ImmutableList<T>` 在 `for` 循环内的效果较差。使用 `foreach` 循环枚举 `ImmutableList<T>` 很有效，因为 `ImmutableList<T>` 使用二进制树来存储其数据，而不是像 `List<T>` 那样使用简单数组。数组可以非常快速地编入索引，但必须向下遍历二进制树，直到找到具有所需索引的节点。

此外，`SortedSet<T>` 与 `ImmutableSortedSet<T>` 的复杂性相同。这是因为它们都使用了二进制树。当然，显著的差异在于 `ImmutableSortedSet<T>` 使用不可变的二进制树。由于 `ImmutableSortedSet<T>` 还提供了一个允许变化的 [`System.Collections.Immutable.ImmutableSortedSet.Builder`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.immutable.immutablesortedset-1.builder) 类，因此可以同时实现不可变性和保障性能。

# 11. 迭代器

> https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/iterators

*迭代器 (Iterator)* 可用于逐步迭代集合，例如列表和数组。

迭代器方法或 `get` 访问器可以对集合执行自定义迭代。迭代器方法使用 `yield return` 语句返回元素，每次返回一个。到达 `yield return` 语句时，会记住当前在代码中的位置。下次调用迭代器函数时，将从该位置重新开始执行。

通过 `foreach` 语句或 LINQ 查询从客户端代码中使用迭代器。

在下面示例中，`foreach` 循环的首次迭代导致 `SomeNumbers` 迭代器方法继续执行，直至到达第一个 `yield return` 语句。此次迭代返回的值为 3，并保留当前在迭代器方法中的位置。在循环的下次迭代中，迭代器方法的执行将从其暂停的位置继续，直至到达 `yield return` 语句后才会停止。此次迭代返回的值为 5，并再次保留当前在迭代器方法中的位置。当到达迭代器方法的结尾时，循环便已完成。

```c#
static void Main()
{
    foreach (int number in SomeNumbers())
    {
        Console.Write(number.ToString() + " ");
    }
    // Output: 3 5 8
    Console.ReadKey();
}

public static System.Collections.IEnumerable SomeNumbers()
{
    yield return 3;
    yield return 5;
    yield return 8;
}
```

迭代器方法或 `get` 访问器的返回类型可以是 [`IEnumerable`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.ienumerable)、[`IEnumerable<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1)、[`IEnumerator`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.ienumerator) 或 [`IEnumerator<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerator-1)。

可以使用 `yield break` 语句来终止迭代。



## 11.1 语法信息

迭代器可用作一种方法，或一个 `get` 访问器。不能在事件、实例构造函数、静态构造函数或静态终结器中使用迭代器。

必须存在从 `yield return` 语句中的表达式类型到迭代器返回的 `IEnumerable<T>` 类型参数的隐式转换。

在 C# 中，迭代器方法不能有任何 `in`、`ref` 或 `out` 参数。

在 C# 中，`yield` 不是保留字，只有在 `return` 或 `break` 关键字之前使用时才有特殊含义。



## 11.2 技术实现

即使将迭代器编写成方法，编译器也会将其转换为实际上是状态机的嵌套类。只要客户端代码中的 `foreach` 循环继续，此类就会跟踪迭代器的位置。

如果要查看编译器执行的操作，可以使用 Ildasm.exe 工具查看为迭代器方法生成的公共中间语言代码。

为类或结构创建迭代器时，不必实现整个 `IEnumerator` 接口。编译器检测到迭代器时，会自动生成 `IEnumerator` 或 `IEnumerator<T>` 接口的 `Current`、`MoveNext` 和 `Dispose` 方法。

在 `foreach` 循环 (或对 `IEnumerator.MoveNext` 的直接调用) 的每次后续迭代中，下一个迭代器代码体都会在上一个 `yield return` 语句之后恢复，然后继续下一个 `yield return` 语句，直至到达迭代器体的结尾，或直至遇到 `yield break` 语句。

迭代器不支持 [`IEnumerator.Reset`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.ienumerator.reset) 方法。如果要从头开始重新迭代，必须获取新的迭代器。在迭代器方法返回的迭代器上调用 `Reset` 会引发 [`NotSupportedException`](https://learn.microsoft.com/en-us/dotnet/api/system.notsupportedexception)。



## 11.3 迭代器的使用

需要使用复杂代码填充列表序列时，使用迭代器可以保持 `foreach` 循环的简单性。当需要执行以下操作时，这可能很有用：

- 在第一次 `foreach` 循环迭代之后，修改列表序列。
- 避免在 `foreach` 循环的第一次迭代之前完全加载大型列表。一个例子是用于加载一批表格行的分页提取。另一个例子是 [`EnumerateFiles`](https://learn.microsoft.com/en-us/dotnet/api/system.io.directoryinfo.enumeratefiles) 方法，该方法在 .NET 中实现迭代器。
- 将列表的生成封装在迭代器中。在迭代器方法中，可以生成该列表，然后在循环中产出每个结果。



## 11.4 关于 foreach

### 11.4.1 使用 foreach 执行循环访问

> https://learn.microsoft.com/en-us/dotnet/csharp/iterators#iterating-with-foreach

枚举集合非常简单：使用 `foreach` 关键字枚举集合，从而为集合中的每个元素执行一次嵌入语句：

```c#
foreach (var item in collection)
{
    Console.WriteLine(item?.ToString());
}
```

如果要循环访问集合中的所有内容，只需使用 `foreach` 语句。`foreach` 语句依赖于 .NET Core 库中定义的两个泛型接口，才能生成循环访问集合所需的代码：`IEnumerable<T>` 和 `IEnumerator<T>`。这两种接口还具备相应的非泛型接口：`IEnumerable` 和 `IEnumerator`。

异步生成序列时，可以使用 `await foreach` 语句异步使用此序列：

```c#
await foreach (var item in asyncSequence)
{
Console.WriteLine(item?.ToString());
}
```

如果序列是 [`System.Collections.Generic.IEnumerable<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1)，则使用 `foreach`。如果序列是 [`System.Collections.Generic.IAsyncEnumerable<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.iasyncenumerable-1)，则使用 `await foreach`。在后一种情况下，序列是异步生成的。



### 11.4.2 深入了解 foreach

> https://learn.microsoft.com/en-us/dotnet/csharp/iterators#deeper-dive-into-foreach

`foreach` 语句可扩展为使用 `IEnumerable<T>` 和 `IEnumerator<T>` 接口的标准用语，以便循环访问集合中的所有元素。还可以最大限度减少开发人员因未正确管理资源所造成的错误。

编译器将上面的第一个示例中显示的 `foreach` 循环转换为类似于此构造的内容：

```c#
IEnumerator<int> enumerator = collection.GetEnumerator();
while (enumerator.MoveNext())
{
    var item = enumerator.Current;
    Console.WriteLine(item.ToString());
}
```

编译器生成的确切代码更复杂一些，用于处理 `GetEnumerator()` 返回的对象实现 `IDisposable` 接口的情况。完整扩展生成的代码更类似如下：

```c#
{
    var enumerator = collection.GetEnumerator();
    try
    {
        while (enumerator.MoveNext())
        {
            var item = enumerator.Current;
            Console.WriteLine(item.ToString());
        }
    }
    finally
    {
        // dispose of enumerator.
    }
}
```

编译器将上面的异步示例转换为类似于此构造的内容：

```c#
{
    var enumerator = collection.GetAsyncEnumerator();
    try
    {
        while (await enumerator.MoveNextAsync())
        {
            var item = enumerator.Current;
            Console.WriteLine(item.ToString());
        }
    }
    finally
    {
        // dispose of async enumerator.
    }
}
```

枚举器的释放方式取决于 `enumerator` 类型的特征。在常规同步情况下，`finally` 子句扩展为：

```c#
finally
{
   (enumerator as IDisposable)?.Dispose();
}
```

常规异步情况扩展为：

```c#
finally
{
    if (enumerator is IAsyncDisposable asyncDisposable)
        await asyncDisposable.DisposeAsync();
}
```

但是，如果 `enumerator` 的类型为已密封类型，并且不存在从类型 `enumerator` 到 `IDisposable` 或 `IAsyncDisposable` 的隐式转换，则 `finally` 子句扩展为一个空白块：

```c#
finally
{
}
```

如果存在从类型 `enumerator` 到 `IDisposable` 的隐式转换，并且 `enumerator` 是不可为 null 的值类型，则 `finally` 子句扩展为：

```c#
finally
{
   ((IDisposable)enumerator).Dispose();
}
```

在使用上无需记住所有这些细节，`foreach` 语句会处理所有这些细微差别。编译器会为所有这些构造生成正确的代码。

# 12. 特性

> https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/features#attributes

在 C# 中，可以将用户定义类型的声明性信息附加到程序实体，并在运行时检索此类信息。程序通过定义和使用 **特性 (Attributes)** 来指定此声明性信息。

所有特性类都派生自 .NET 库提供的 [`System.Attribute`](https://learn.microsoft.com/en-us/dotnet/api/system.attribute) 基类。

特性的应用方式为：在相关声明前的方括号内指定特性的名称以及任意自变量。如果特性的名称以 Attribute 结尾，那么可以在引用特性时省略这部分名称。

可以在运行时使用反射来读取和操纵特性定义的元数据。使用这种方法请求获取特定特性，便会调用特性类的构造函数，返回生成的特性实例。如果是通过属性提供其他信息，那么在特性实例返回前，这些属性会设置为给定值。



## 12.1 概述

> https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection-and-attributes/

特性提供了一种将元数据或声明性信息与代码 (程序集、类型、方法、属性等) 相关联的强大方法。将特性与程序实体关联后，可以使用称为反射的技术在运行时查询该特性。

特性具有以下属性：

- 特性将元数据添加到程序中。元数据是有关程序中定义的类型的信息。所有 .NET 程序集都包含一组指定的元数据，用于描述程序集中定义的类型和类型成员。可以添加自定义特性来指定所需的任何其他信息。
- 可以将一个或多个特性应用于整个程序集、模块或较小的程序元素 (例如类和属性)。
- 特性可以像方法和属性一样接受参数。
- 程序可以使用反射检查自己的元数据或其他程序中的元数据。



### 12.1.1 使用特性

特性可以放置在几乎任何声明上，但是特定的特性可能会限制其有效的声明类型。在 C# 中，可以通过将特性名称放在方括号 `[]` 中，将其放在适用实体的声明上方来指定特性。

按照惯例，所有特性名称都以单词 "Attribute" 结尾，以将其与 .NET 库中的其他项目区分开来。但是，在代码中使用特性时无需指定其后缀。例如，`[DllImport]` 相当于 `[DllImportAttribute]`，但 `DllImportAttribute` 是 .NET 类库中特性的实际名称。

#### 12.1.1.1 特性的参数

很多特性都有参数，这些参数可以是位置参数、未命名参数或者命名参数。任何位置参数都必须按特定顺序指定，并且不能省略。命名参数是可选的，可以按任意顺序指定。位置参数首先指定。例如，以下 3 个特性是等效的：

```c#
[DllImport("user32.dll")]
[DllImport("user32.dll", SetLastError=false, ExactSpelling=false)]
[DllImport("user32.dll", ExactSpelling=false, SetLastError=false)]
```

在上面示例中，第一个参数 (DLL 名称) 是位置参数，始终位于最前面，而其他参数是命名参数。在这种情况下，两个命名参数默认为 `false`，因此可以省略。位置参数对应于特性构造函数的参数。命名或可选参数对应于特性的属性或字段。

#### 12.1.1.2 特性的目标

特性的目标是特性所应用的实体。例如，特性可能应用于类、特定方法或整个程序集。默认情况下，特性应用于其后的元素。但是也可以明确标识。例如，特性是应用于方法、其参数还是其返回值。

如果要明确标识特性的目标，应使用以下语法：

```c#
[target : attribute-list]
```

下表列出可能的目标值：

| **目标值** | **应用于**                                  |
| :--------- | :------------------------------------------ |
| `assembly` | 整个程序集                                  |
| `module`   | 当前程序集模块                              |
| `field`    | 类或结构中的字段                            |
| `event`    | 事件                                        |
| `method`   | 方法或 `get` 和 `set` 属性访问器            |
| `param`    | 方法参数或 `set` 属性访问器参数             |
| `property` | 属性                                        |
| `return`   | 方法、属性索引器或 `get` 属性访问器的返回值 |
| `type`     | 结构、类、接口、枚举或委托                  |

可以指定字段目标值以将特性应用于为自动实现的属性创建的支持字段。

下面的示例展示了如何将特性应用于程序集和模块：

```c#
using System;
using System.Reflection;
[assembly: AssemblyTitleAttribute("Production assembly 4")]
[module: CLSCompliant(true)]
```

下面的示例展示了如何在 C# 中将特性应用于方法、方法参数和方法返回值：

```c#
// default: applies to method
[ValidatedContract]
int Method1() { return 0; }

// applies to method
[method: ValidatedContract]
int Method2() { return 0; }

// applies to parameter
int Method3([ValidatedContract] string contract) { return 0; }

// applies to return value
[return: ValidatedContract]
int Method4() { return 0; }
```



### 12.1.2 特性的常见用途

以下是代码中特性的一些常见用途：

- 使用 Web 服务中的 `WebMethod` 特性来标记方法，以指示该方法应可通过 SOAP 协议调用。可在 [WebMethodAttribute](https://learn.microsoft.com/en-us/dotnet/api/system.web.services.webmethodattribute) 了解相关信息。

- 描述如何在与本地代码交互操作时编组方法参数。可在 [MarshalAsAttribute](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.marshalasattribute) 了解相关信息。

- 描述类、方法和接口的 COM 属性。

- 使用 [DllImportAttribute](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.dllimportattribute) 类调用非托管代码。

- 使用标题、版本、说明或商标来描述程序集。

- 描述要序列化类的哪些成员以实现持久性。

- 描述如何在类成员和 XML 节点之间进行映射以进行 XML 序列化。

- 描述方法的安全要求。

- 指定用于强制实施安全性的特征。

- 控制即时 (JIT) 编译器的优化，使代码易于调试。

- 获取有关方法调用者的信息。



## 12.2 自定义特性

> https://learn.microsoft.com/en-us/dotnet/standard/attributes/writing-custom-attributes

自定义特性直接或间接派生自 `System.Attribute` 类。与传统类一样，自定义特性包含用于存储和检索数据的方法。

创建自定义特性的主要步骤如下：

- 应用 AttributeUsageAttribute
- 声明特性类
- 声明构造函数
- 声明属性



### 12.2.1 应用 AttributeUsageAttribute

自定义特性的声明以 [`System.AttributeUsageAttribute`](https://learn.microsoft.com/en-us/dotnet/api/system.attributeusageattribute) 特性开头，定义特性类的一些主要特征。例如，可以指定其他类是否可以继承该特性，或者此特性可以应用到哪些元素。

以下代码是 `AttributeUsageAttribute` 的使用示例：

```c#
[AttributeUsage(AttributeTargets.All, Inherited = false, AllowMultiple = true)]
```

`AttributeUsageAttribute` 包含以下 3 个成员，它们对创建自定义特性非常重要：AttributeTargets、Inherited 和 AllowMultiple。

#### 12.2.1.1 AttributeTargets 成员

上面的示例指定的是 [AttributeTargets.All](https://learn.microsoft.com/en-us/dotnet/api/system.attributetargets#system-attributetargets-all)，表示此特性可应用于所有程序元素。还可以指定 [AttributeTargets.Class](https://learn.microsoft.com/en-us/dotnet/api/system.attributetargets#system-attributetargets-class) 和 [AttributeTargets.Method](https://learn.microsoft.com/en-us/dotnet/api/system.attributetargets#system-attributetargets-method)，前者表示特性仅适用于一个类，后者表示特性仅可应用于一种方法。所有程序元素都可以通过这种方式使用自定义特性来标记，以对其进行描述。

还可以传递多个 [AttributeTargets](https://learn.microsoft.com/en-us/dotnet/api/system.attributetargets) 值。

下面代码示例指定可以将自定义特性应用于任何类或方法：

```c#
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method)]
```

#### 12.2.1.2 Inherited 属性

[AttributeUsageAttribute.Inherited](https://learn.microsoft.com/en-us/dotnet/api/system.attributeusageattribute.inherited) 属性指明要对其应用特性的类的派生类能否继承此特性。此属性使用 `true` (默认值) 或 `false` 标志。在下面的示例中，`MyAttribute` 的默认 Inherited 值为 `true`，而 `YourAttribute` 的 Inherited 值为 `false`：

```c#
// This defaults to Inherited = true.
public class MyAttribute : Attribute
{
    //...
}

[AttributeUsage(AttributeTargets.Method, Inherited = false)]
public class YourAttribute : Attribute
{
    //...
}
```

然后，将这两个特性应用于基类 `MyClass` 中的方法：

```c#
public class MyClass
{
    [MyAttribute]
    [YourAttribute]
    public virtual void MyMethod()
    {
        //...
    }
}
```

最后，从基类 `YourClass` 中继承类 `MyClass`。方法 `MyMethod` 显示 `MyAttribute`，但不显示 `YourAttribute`：

```c#
public class YourClass : MyClass
{
    // MyMethod will have MyAttribute but not YourAttribute.
    public override void MyMethod()
    {
        //...
    }
}
```

#### 12.2.1.3 AllowMultiple 属性

[AttributeUsageAttribute.AllowMultiple](https://learn.microsoft.com/en-us/dotnet/api/system.attributeusageattribute.allowmultiple) 属性指明元素能否包含特性的多个实例。如果设置为 `true`，则允许多个实例。如果设置为 `false` (默认值)，那么只允许一个实例。

在下面示例中，`MyAttribute` 的默认 AllowMultiple 值为 `false`，而 `YourAttribute` 的值为 `true`：

```c#
//This defaults to AllowMultiple = false.
public class MyAttribute : Attribute
{
}

[AttributeUsage(AttributeTargets.Method, AllowMultiple = true)]
public class YourAttribute : Attribute
{
}
```

当应用这些特性的多个实例时，`MyAttribute` 会生成编译器错误。下面代码示例显示了 `YourAttribute` 的有效用法以及 `MyAttribute`的无效用法：

```c#
public class MyClass
{
    // This produces an error.
    // Duplicates are not allowed.
    [MyAttribute]
    [MyAttribute]
    public void MyMethod()
    {
        //...
    }

    // This is valid.
    [YourAttribute]
    [YourAttribute]
    public void YourMethod()
    {
        //...
    }
}
```

如果 AllowMultiple 属性和 Inherited 属性都设置为 `true`，从另一个类继承的类可以继承一个特性，并具有在同一个子类中应用相同特性的另一个实例。如果 AllowMultiple 设置为 `false`，则父类中的所有特性的值将被子类中同一特性的新实例覆盖。



### 12.2.2 声明特性类

在应用 `AttributeUsageAttribute` 以后，开始定义特性的细节。特性类的声明类似于传统类的声明，如以下代码所示：

```c#
[AttributeUsage(AttributeTargets.Method)]
public class MyAttribute : Attribute
{
    // . . .
}
```

此特性的定义说明了以下几点：

- 特性类必须声明为公共类。
- 按照约定，特性类的名称以单词 "Attribute" 结束。尽管没有要求，但仍建议执行此约定以保证可读性。在应用特性时，可以选择是否包含单词 "Attribute"。
- 所有特性类必须直接或间接从 `System.Attribute` 类继承。



### 12.2.3 声明构造函数

类似于传统类，特性是通过构造函数初始化的。下面的代码示例显示了典型的特性构造函数，此公共构造函数采用一个参数，并设置一个等于其值的成员变量：

```c#
public MyAttribute(bool myvalue)
{
    this.myvalue = myvalue;
}
```

可以重载此构造函数以适应值的各种组合。如果自定义特性类还定义了属性 ，则在初始化该特性时可以使用命名参数和定位参数的组合。通常情况下，将所有必选的参数定义为定位参数，将所有可选的参数定义为命名参数。在这种情况下，没有必需的参数就无法初始化特性。其他所有参数都是可选参数。



### 12.2.4 声明属性

如果想要定义一个命名参数，或者提供一种简单的方法来返回由特性存储的值，可以声明属性。应将特性的属性声明为公共的实体，此公共实体包含将返回的数据类型的描述。定义用于保存属性值的变量，并将此变量与 `get` 和 `set` 方法相关联。

下面代码示例显示了如何在特性中实现一个简单属性：

```c#
public bool MyProperty
{
    get {return this.myvalue;}
    set {this.myvalue = value;}
}
```



## 12.3 检索存储在特性中的信息

> https://learn.microsoft.com/en-us/dotnet/standard/attributes/retrieving-information-stored-in-attributes

检索自定义特性的过程非常简单。首先，声明要检索的特性实例。然后，使用 [`Attribute.GetCustomAttribute`](https://learn.microsoft.com/en-us/dotnet/api/system.attribute.getcustomattribute) 方法，将新特性初始化为要检索的特性的值。在初始化新特性后，可使用其属性来获取值。

需要注意的是，此处描述的是如何检索加载到执行上下文中的代码的特性。如果需要为仅反射上下文中加载的代码检索特性，必须使用 [`CustomAttributeData`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.customattributedata) 类。可在 [How to: Load Assemblies into the Reflection-Only Context](https://learn.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/how-to-load-assemblies-into-the-reflection-only-context) 了解详细信息。



## 12.4 特性与泛型

> https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection-and-attributes/generics-and-attributes

特性可以按照与非泛型类型相同的方式应用于泛型类型。但是，只能将特性应用于 *开放式泛型类型 (open generic types)* 和 *封闭式构造泛型类型 (closed constructed generic types)*，而不能应用于 *部分构造泛型类型 (partially constructed generic types)*。开放式泛型类型是未指定任何类型参数的类型，例如：`Dictionary<TKey, TValue>`。封闭式构造泛型类型指定了所有类型参数，例如：`Dictionary<string, object>`。部分构造泛型类型指定了一些 (但不是全部) 类型参数，例如：`Dictionary<string, TValue>`。

例如，创建一个自定义特性 `CustomAttribute`：

```c#
class CustomAttribute : Attribute
{
    public object? info;
}
```

该特性可以引用开放式泛型类型：

```c#
public class GenericClass1<T> { }

[CustomAttribute(info = typeof(GenericClass1<>))]
class ClassA { }
```

通过使用适当数量的逗号指定多个类型参数。例如，以下示例中的 `GenericClass2` 具有两个类型参数：

```c#
public class GenericClass2<T, U> { }

[CustomAttribute(info = typeof(GenericClass2<,>))]
class ClassB { }
```

特性也可以引用封闭式构造泛型类型：

```c#
public class GenericClass3<T, U, V> { }

[CustomAttribute(info = typeof(GenericClass3<int, double, string>))]
class ClassC { }
```

但是，引用了泛型类型参数的特性会导致编译时错误：

```c#
[CustomAttribute(info = typeof(GenericClass3<int, T, string>))]  //Error CS0416
class ClassD<T> { }
```

从 C# 11 开始，泛型类型可以从 `System.Attribute` 继承：

```c#
public class CustomGenericAttribute<T> : Attribute { }  //Requires C# 11
```

如果要在运行时获取有关泛型类型或类型参数的信息，可以使用 System.Reflection 命名空间中的类型。

# 13. 反射

## 13.1 概述

> https://learn.microsoft.com/en-us/dotnet/fundamentals/reflection/reflection

[System.Reflection](https://learn.microsoft.com/en-us/dotnet/api/system.reflection) 命名空间中的类与 [`System.Type`](https://learn.microsoft.com/en-us/dotnet/api/system.type) 可用于获取有关加载的程序集和其中定义的类型的信息，如类、接口和值类型 (即结构和枚举)。可以使用反射在运行时创建、调用和访问类型实例。

程序集包含模块、模块包含类型，而类型包含成员。反射提供封装程序集、模块和类型的对象。可以使用反射动态地创建类型的实例，将类型绑定到现有对象，或者从现有对象中获取类型。然后，可以调用类型的方法或访问其字段和属性。

反射的典型用法如下所示：

- 使用 [`Assembly`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.assembly) 来定义和加载程序集，加载程序集清单中列出的模块，以及在此程序集中定位一个类型并创建一个它的实例。
- 使用 [`Module`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.module) 发现信息，如包含模块的程序集和模块中的类。还可以获取所有全局方法或模块上定义的其它特定的非全局方法。
- 使用 [`ConstructorInfo`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.constructorinfo) 发现信息，如名称、参数、访问修饰符、构造函数的实现详细信息 (如 `abstract` 或 `virtual`)。使用 `Type` 的 [`GetConstructors`](https://learn.microsoft.com/en-us/dotnet/api/system.type.getconstructors) 或者 [`GetConstructor`](https://learn.microsoft.com/en-us/dotnet/api/system.type.getconstructor) 方法来调用特定构造函数。
- 使用 [`MethodInfo`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodinfo) 发现信息，如名称、返回类型、参数、访问修饰符、方法的实现详细信息 (如 `abstract` 或 `virtual`)。使用 `Type` 的 [`GetMethods`](https://learn.microsoft.com/en-us/dotnet/api/system.type.getmethods) 或 [`GetMethod`](https://learn.microsoft.com/en-us/dotnet/api/system.type.getmethod) 方法来调用特定方法。
- 使用 [`FieldInfo`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.fieldinfo) 发现信息，如名称、访问修饰符、一个字段的实现详细信息 (如 `static`)，并获取或设置字段值。
- 使用 [`EventInfo`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.eventinfo) 发现信息 (如名称、事件处理程序的数据类型、自定义特性、声明类型以及事件的反射的类型)，并添加或删除事件处理程序。
- 使用 [`PropertyInfo`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.propertyinfo) 发现信息 (如名称、数据类型、声明类型，反射的类型和属性的只读或可写状态)，并获取或设置属性值。
- 使用 [`ParameterInfo`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.parameterinfo) 发现信息，如参数的名称、数据类型、参数是输入参数还是输出参数以及参数在方法签名中的位置。
- 在 [`MetadataLoadContext`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.metadataloadcontext) 或仅反射上下文 (.NET Framework) 中工作时使用 [`CustomAttributeData`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.customattributedata) 可以发现有关自定义特性的信息。`CustomAttributeData` 可用于检查特性，而无需创建它们的实例。

[System.Reflection.Emit](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.emit) 命名空间的类提供一种专用形式的反射，可用于在运行时生成类型。

还可以使用反射来创建类型浏览器，它使用户能够选择类型，然后查看有关这些类型的信息。

反射还有其它用途。JScript 等语言的编译器使用反射来构造符号表。[System.Runtime.Serialization](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.serialization) 命名空间中的类使用反射来访问数据并确定要保存哪些字段。[System.Runtime.Remoting](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.remoting) 命名空间中的类通过序列化间接使用反射。



### 13.1.1 反射中的运行时类型

反射提供了一些类 (如 `Type` 和 `MethodInfo`)，用于表示类型、成员、参数和其它代码实体。但是，使用反射时并不直接使用这些类，其中大部分类都是抽象的。相反，使用的是由 CLR 提供的类型。

例如，使用 `typeof` 运算符获取 `Type` 对象时，该对象实际上是 `RuntimeType`。`RuntimeType` 派生自 `Type`，并提供所有抽象方法的实现。

这些运行时类是 `internal`。它们没有与其基类分开记录，因为它们的行为由基类文档来描述。



## 13.2 查看类型信息

> https://learn.microsoft.com/en-us/dotnet/fundamentals/reflection/viewing-type-information

`System.Type` 类是反射的中心。当反射提出请求时，CLR 为已加载的类型创建 `Type`。可以使用 `Type` 对象的方法、字段、属性和嵌套类来查找该类型的任何信息。

使用 [`Assembly.GetType`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.assembly.gettype) 或 [`Assembly.GetTypes`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.assembly.gettypes) 从尚未加载的程序集中获取 `Type` 对象，传入所需类型的名称。使用 [`Type.GetType`](https://learn.microsoft.com/en-us/dotnet/api/system.type.gettype) 从已加载的程序集中获取 `Type` 对象。使用 [`Module.GetType`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.module.gettype) 和 [`Module.GetTypes`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.module.gettypes) 获取模块 `Type` 对象。

一旦获取 `Type` 对象，可通过多种方式来查看该类型成员的相关信息。例如，可调用 [`Type.GetMembers`](https://learn.microsoft.com/en-us/dotnet/api/system.type.getmembers) 方法查找所有类型的成员，获取一组描述当前类型各成员的 [`MemberInfo`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.memberinfo) 对象。

还可以使用 `Type` 类上的方法来检索按名称指定的一个或多个构造函数、方法、事件、字段或属性的相关信息。例如，[`Type.GetConstructor`](https://learn.microsoft.com/en-us/dotnet/api/system.type.getconstructor) 封装当前类的特定构造函数。

如果有 `Type`，可使用 [`Type.Module`](https://learn.microsoft.com/en-us/dotnet/api/system.type.module) 属性获取一个封装含该类型的模块的对象。使用 [`Module.Assembly`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.module.assembly) 属性查找一个封装含该模块的程序集的对象。可以获取直接使用 [`Type.Assembly`](https://learn.microsoft.com/en-us/dotnet/api/system.type.assembly) 属性封装类型的程序集。



## 13.3 反射与泛型

> https://learn.microsoft.com/en-us/dotnet/fundamentals/reflection/reflection-and-generic-types
>

从反射的角度来说，泛型类型和普通类型之间的区别在于泛型类型具有与之关联的一组类型形参 (如果是泛型类型定义) 或类型实参 (如果是构造类型)。泛型方法和普通方法也有着与上述一样的区别。

有两个关键点可了解反射如何处理泛型类型和方法：

- 泛型类型定义和泛型方法定义的类型参数由 `Type` 类的实例表示。

  当 `Type` 对象表示泛型类型参数时，`Type` 的很多属性和方法具有不同的行为，可在 [IsAutoClass](https://learn.microsoft.com/en-us/dotnet/api/system.type.isautoclass) 和 [DeclaringType](https://learn.microsoft.com/en-us/dotnet/api/system.type.declaringtype) 了解这些差异。此外，某些成员仅在 `Type` 对象表示泛型类型参数时有效，可在 [GetGenericTypeDefinition](https://learn.microsoft.com/en-us/dotnet/api/system.type.getgenerictypedefinition) 了解详细信息。

- 如果 `Type` 的实例表示泛型类型，则它包含表示类型形参 (对于泛型类型定义) 或类型实参 (对于构造类型) 的类型数组。这同样适用于表示泛型方法的 `MethodInfo` 类的实例。

反射提供 `Type` 和 `MethodInfo` 的方法，用于访问类型参数的数组并确定 `Type` 的实例是表示类型形参还是表示实际类型。

另外，可在 [Generics and reflection](https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection-and-attributes/generics-and-reflection) 了解关于泛型和反射的更多信息。



### 13.3.1 检查泛型类型和泛型方法

当使用反射来检查未知类型 (由 `Type` 的实例表示) 时，应使用 [`IsGenericType`](https://learn.microsoft.com/en-us/dotnet/api/system.type.isgenerictype) 属性来确定未知类型是否为泛型。如果类型是泛型，它会返回 `true` 。

同样，当检查未知方法 (由 `MethodInfo` 类的实例表示) 时，应使用 [`IsGenericMethod`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodbase.isgenericmethod) 属性来确定此方法是否为泛型。



### 13.3.2 检查泛型类型定义和泛型方法定义

使用 [`IsGenericTypeDefinition`](https://learn.microsoft.com/en-us/dotnet/api/system.type.isgenerictypedefinition) 属性来确定 `Type` 对象是否表示泛型类型定义，并使用 [`IsGenericMethodDefinition`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodbase.isgenericmethoddefinition) 方法来确定 `MethodInfo` 是否表示泛型方法定义。

泛型类型定义和泛型方法定义是一些从中创建可实例化类型的模板。.NET 库中的泛型类型 (如 `Dictionary<TKey, TValue>`) 是泛型类型定义。



### 13.3.3 检查泛型类型或方法是开放式还是封闭式

如果已用可实例化类型替换了泛型类型或方法的所有类型参数 (包括所有封闭类型的所有类型参数)，则泛型类型或方法是封闭式的。只有当泛型类型是封闭式时，才能创建泛型类型的实例。如果类型为开放式，[`Type.ContainsGenericParameters`](https://learn.microsoft.com/en-us/dotnet/api/system.type.containsgenericparameters) 属性将返回 `true`。对于方法，[`MethodBase.ContainsGenericParameters`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodbase.containsgenericparameters) 方法执行相同的功能。



### 13.3.4 生成封闭式泛型类型

当拥有泛型类型或方法的定义之后，可使用 [`MakeGenericType`](https://learn.microsoft.com/en-us/dotnet/api/system.type.makegenerictype) 方法来创建封闭式泛型类型，或者使用 [`MakeGenericMethod`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodinfo.makegenericmethod) 方法来为封闭式泛型方法创建 `MethodInfo`。

#### 13.3.4.1 获取泛型类型或方法定义

如果拥有的是开放式泛型类型或方法 (非泛型类型或方法定义)，则无法创建它的实例，也无法提供缺少的类型形参。必须具有泛型类型或方法定义。使用 [`GetGenericTypeDefinition`](https://learn.microsoft.com/en-us/dotnet/api/system.type.getgenerictypedefinition) 方法来获取泛型类型定义，或者使用 [`GetGenericMethodDefinition`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodinfo.getgenericmethoddefinition) 方法来获取泛型方法定义。

例如，如果有一个表示 `Dictionary<int, string>` 的 `Type` 对象并且想要创建类型 `Dictionary<string, MyClass>`，则可以使用 `GetGenericTypeDefinition` 方法来获取表示 `Dictionary<TKey, TValue>` 的 `Type`，然后使用 `MakeGenericType` 方法来生成表示 `Dictionary<int, MyClass>` 的 `Type`。



### 13.3.5 检查类型实参和类型形参

使用 [`Type.GetGenericArguments`](https://learn.microsoft.com/en-us/dotnet/api/system.type.getgenericarguments) 方法来获取 `Type` 对象的数组 (表示泛型类型的类型形参或类型实参)，并使用 [`MethodInfo.GetGenericArguments`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodinfo.getgenericarguments) 方法为泛型方法执行相同的操作。

当知道 `Type` 对象表示类型形参之后，即可借助反射来回答很多其他问题。可以确定类型形参的源、位置以及约束。

#### 13.3.5.1 类型形参或类型实参

如果要确定数组的某个特定元素是类型形参还是类型实参，可使用 [`IsGenericParameter`](https://learn.microsoft.com/en-us/dotnet/api/system.type.isgenericparameter) 属性。如果元素是类型形参，则 `IsGenericParameter` 属性为 `true` 。

在不是泛型类型定义的情况下，泛型类型也可以是开放式，此时它同时具有类型实参和类型形参。例如，在下面的代码中，类 `D` 派生自通过将 `D` 的第一个类型参数替换为 `B`的第二个类型参数来创建的类型：

```c#
class B<T, U> {}
class D<V, W> : B<int, V> {}
```

当获取了表示 `D<V, W>` 的 `Type` 对象，并使用 [`BaseType`](https://learn.microsoft.com/en-us/dotnet/api/system.type.basetype) 属性来获取其基类型，则产生的 `type B<int, V>` 是开放式的，但它不是泛型类型定义。

#### 13.3.5.2 泛型参数的源

泛型类型形参可能来自要检查的类型、封闭类型或者泛型方法。可以按以下方式确定泛型类型形参的源：

- 首先，使用 [`DeclaringMethod`](https://learn.microsoft.com/en-us/dotnet/api/system.type.declaringmethod) 属性来确定类型形参是否来自泛型方法。如果属性值不是空引用，则源是泛型方法。
- 如果源不是泛型方法，则使用 [`DeclaringType`](https://learn.microsoft.com/en-us/dotnet/api/system.type.declaringtype) 属性来确定泛型类型形参所属的泛型类型。

如果类型形参属于泛型方法，则 `DeclaringType` 属性返回声明泛型方法的类型，这是不相关的类型。

#### 13.3.5.3 泛型参数的位置

在极少数情况下，必须确定类型形参在其声明类的类型形参列表中的位置。例如，假设当前拥有上面示例中的 `Type` 类型的 `B<int, V>` 对象。`GetGenericArguments` 方法提供类型实参的列表，在检查 `V` 时即可使用 `DeclaringMethod` 和 `DeclaringType` 属性来发现它的来源位置。然后，可以使用 [`GenericParameterPosition`](https://learn.microsoft.com/en-us/dotnet/api/system.type.genericparameterposition) 属性来确定它在定义它的类型形参列表中的位置。在此示例中，`V` 在定义它的类型形参列表中位于位置 0。

#### 13.3.5.4 基类型和接口约束

使用 [`GetGenericParameterConstraints`](https://learn.microsoft.com/en-us/dotnet/api/system.type.getgenericparameterconstraints) 方法来获取类型形参的基类型约束和接口约束。数组的元素顺序并不重要。如果元素是接口类型，则它表示接口约束。

#### 13.3.5.5 泛型参数特性

[`GenericParameterAttributes`](https://learn.microsoft.com/en-us/dotnet/api/system.type.genericparameterattributes) 属性获取 [`GenericParameterAttributes`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.genericparameterattributes) 值，该值指示类型形参的方差 (协变或逆变) 和特殊约束。

##### 13.3.5.5.1 协变和逆变

如果要确定类型形参是协变还是逆变，应将 [`GenericParameterAttributes.VarianceMask`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.genericparameterattributes#system-reflection-genericparameterattributes-variancemask) 掩码应用到 `GenericParameterAttributes` 属性返回的 `GenericParameterAttributes` 值。如果结果为 [`GenericParameterAttributes.None`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.genericparameterattributes#system-reflection-genericparameterattributes-none)，则类型形参不变。

##### 13.3.5.5.2 特殊约束

如果要确定类型形参的特殊约束，应将 [`GenericParameterAttributes.SpecialConstraintMask`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.genericparameterattributes#system-reflection-genericparameterattributes-specialconstraintmask) 掩码应用到 `GenericParameterAttributes` 属性返回的 `GenericParameterAttributes` 值。如果结果为 `GenericParameterAttributes.None`，则没有任何特殊约束。可以将类型形参约束为引用类型、不可为 null 的类型以及具有无参数构造函数。



### 13.3.6 固定协定

关于泛型类型反射中常用术语的固定条件表格，可参阅 [Type.IsGenericType](https://learn.microsoft.com/en-us/dotnet/api/system.type.isgenerictype)。关于与泛型方法相关的其他术语，可参阅 [MethodBase.IsGenericMethod](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.methodbase.isgenericmethod)。



## 13.4 反射与特性

> https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/reflection-and-attributes/accessing-attributes-by-using-reflection
>
> https://learn.microsoft.com/en-us/dotnet/fundamentals/reflection/accessing-custom-attributes

通过使用反射，可以检索通过自定义特性定义的信息。

用于查询执行上下文中的特性的主要反射方法是 [MemberInfo.GetCustomAttributes](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.memberinfo.getcustomattributes) 和 [Attribute.GetCustomAttributes](https://learn.microsoft.com/en-us/dotnet/api/system.attribute.getcustomattributes)。

这些方法有很多重载版本，可在 [MemberInfo](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.memberinfo) 和 [Attribute](https://learn.microsoft.com/en-us/dotnet/api/system.attribute) 了解详细信息。

# 14. .NET 中的程序集

> https://learn.microsoft.com/en-us/dotnet/standard/assembly/

**程序集 (Assembly)** 构成了 .NET 应用程序的部署、版本控制、重用、激活范围和安全权限的基本单元。程序集是为协同工作而生成的类型和资源的集合，这些类型和资源构成了一个逻辑功能单元。

程序集采用可执行文件 (*.exe*) 或动态链接库文件 (*.dll*) 的形式，是 .NET 应用程序的构建基块，它们向 CLR 提供了了解类型实现所需的信息。

在 .NET 和 .NET Framework 中，可以从一个或多个源代码文件生成程序集。在 .NET Framework 中，程序集可以包含一个或多个模块。因此，大型项目可以采用以下规划：由多个开发者单独开发各源代码文件或模块，最后整合所有这些内容以创建一个程序集。

程序集具有以下属性：

- 程序集以 `.exe` 或 `.dll` 文件的形式实现 。
- 对于面向 .NET Framework 的库，可以通过将程序集放入 [全局程序集缓存 (GAC)](https://learn.microsoft.com/en-us/dotnet/framework/app-domains/gac)，在应用程序之间共享程序集。必须先对程序集进行强命名，然后才能将它们包含到 GAC 中。
- 只有在需要时，才会将程序集加载到内存中。如果未使用程序集，则不会加载。也就是说，使用程序集，可以在大型项目中高效管理资源。
- 可以使用反射，以编程方式获取程序集的相关信息。
- 可以加载一个程序集，使用 .NET 和 .NET Framework 中的 [`MetadataLoadContext`](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.metadataloadcontext) 类来检查该程序集。MetadataLoadContext 取代了 [Assembly.ReflectionOnlyLoad](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.assembly.reflectiononlyload) 方法。



## 14.1 程序集与模块

> https://stackoverflow.com/questions/9271805/net-module-vs-assembly
>
> https://stackoverflow.com/questions/645728/what-is-a-module-in-net

在 .NET 中，程序集和模块之间的区别在于模块不包含 *manifest*。

模块是程序集中代码的逻辑集合。程序集可以有多个模块，每个模块可以用不同的 .NET 语言编写。

程序集包含模块，模块包含类，类包含函数。



## 14.2 加载程序集

> https://learn.microsoft.com/en-us/dotnet/api/system.reflection.assembly
>
> https://www.cnblogs.com/xuefeng1982/archive/2009/11/09/1598956.html

`Assembly` 提供了 3 类方法用于加载程序集，分别是 `Assembly.Load` 方法、`Assembly.LoadFile` 方法和 `Assembly.LoadFrom` 方法。可在 [Assembly](https://learn.microsoft.com/en-us/dotnet/api/system.reflection.assembly) 了解详细信息。

# 技巧

## 1. 获取当前使用的 C# 版本

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/configure-language-version

在代码中添加 `#error version` (区分大小写)，可以使编译器报告编译器错误 CS8304，显示的消息中包含正在使用的编译器版本和当前选择的语言版本。可在 [#error (C# Reference)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/preprocessor-directives#error-and-warning-information) 了解更多信息。


# TODO

- https://learn.microsoft.com/en-us/dotnet/csharp/advanced-topics/performance/
- https://learn.microsoft.com/en-us/dotnet/standard/design-guidelines/
- https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/identifier-names
- https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions
- https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/object-and-collection-initializers
- https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/generics/differences-between-cpp-templates-and-csharp-generics
- https://learn.microsoft.com/en-us/dotnet/standard/threading/managed-threading-basics
- https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/unmanaged
- https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/
- https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/attributes/general
- https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/covariance-contravariance/
- https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/strings/
- https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/tokens/interpolated

