---
title: C# 不安全代码
date: 2023-04-20
updated: 2024-08-15
permalink: /csharp/unsafe-code/
categories:
- [C#]
---

# 1. 不安全代码

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/unsafe-code

编写的大部分 C# 代码都是可验证的安全代码。可验证的安全代码是指 .NET 工具可验证代码是否安全。通常，安全代码不会直接使用指针访问内存，也不会分配原始内存，而是创建托管对象。

C# 支持 [`unsafe`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/unsafe) 上下文，可以在其中编写不可验证的代码。在 `unsafe` 上下文中，代码可使用指针、分配和释放内存块，以及使用函数指针调用方法。C# 中的不安全代码不一定是危险的，它只是其安全性不可验证的代码。

不安全代码具有以下属性：

- 可将方法、类型和代码块定义为不安全。
- 在某些情况下，通过移除数组绑定检查，不安全代码可提高应用程序的性能。
- 调用需要指针的本地函数时，需使用不安全代码。
- 使用不安全代码将引发安全风险和稳定性风险。
- 必须使用 [AllowUnsafeBlocks](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/compiler-options/language#allowunsafeblocks) 编译器选项来编译包含不安全块的代码。



<!-- more -->

## 1.1 指针类型

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/unsafe-code#pointer-types

在不安全的上下文中，类型除了是值类型或引用类型外，还可以是指针类型。

指针类型声明采用下列形式之一：

```c#
type* identifier;
void* identifier; // 允许但不推荐
```

在指针类型中的 `*` 之前指定的类型被称为 **referent 类型**。只有 [非托管类型](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/unmanaged-types) 可以是 referent 类型。

指针类型不从 `object` 继承，并且指针类型与 `object` 之间不存在转换。此外，装箱和取消装箱不支持指针。但是，可以在不同的指针类型之间以及在指针类型和整型之间进行转换。

当在同一声明中声明多个指针时，星号 (`*`) 仅与基础类型一起写入，而不是用作每个指针名称的前缀。例如：

```c#
int* p1, p2, p3;   // Ok
int *p1, *p2, *p3;   // Invalid in C#
```

指针不能指向引用或者包含引用的结构，因为无法对对象引用进行垃圾回收，即使有指针指向它也是如此。垃圾回收器并不跟踪是否有任何类型的指针指向对象。

`MyType*` 类型的指针变量的值为 `MyType` 类型的变量的地址。

下面是指针类型声明的示例：

- `int* p`：`p` 是指向整数的指针。
- `int** p`：`p` 是指向整数的指针的指针。
- `int*[] p`：`p` 是指向整数的指针的一维数组。
- `char* p`：`p` 是指向字符的指针。
- `void* p`：`p` 是指向未知类型的指针。

指针间接寻址运算符 `*` 可用于访问位于指针变量所指向的位置的内容。例如：

```c#
int* myVariable;
```

表达式 `*myVariable` 表示在 `int` 中包含的地址处找到的 `myVariable` 变量。

无法对 `void*` 类型的指针应用间接寻址运算符。但是，可以使用强制转换将 void 指针转换为任何其他指针类型，反之亦然。

指针可以为 `null`。将间接寻址运算符应用于 null 指针将导致由实现定义的行为。

在方法之间传递指针会导致未定义的行为。考虑一种方法，该方法通过 `in`、`out` 或 `ref` 参数或作为函数结果返回一个指向局部变量的指针。如果已在 *固定块 (fixed block)* 中设置指针，则它指向的变量不再是固定的。

下表列出了可在不安全的上下文中对指针执行的运算符和语句：

| **运算符/语句**                                              | **使用**                     |
| :----------------------------------------------------------- | :--------------------------- |
| `*`                                                          | 执行指针间接寻址。           |
| `->`                                                         | 通过指针访问结构的成员。     |
| `[]`                                                         | 为指针建立索引。             |
| `&`                                                          | 获取变量的地址。             |
| `++` 和 `--`                                                 | 递增和递减指针。             |
| `+` 和 `-`                                                   | 执行指针算法。               |
| `==`、`!=`、`<`、`>`、`<=` 和 `>=`                           | 比较指针。                   |
| [stackalloc](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/stackalloc) | 在堆栈上分配内存。           |
| [fixed 语句](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/fixed) | 临时固定变量以便找到其地址。 |

可在 [与指针相关的运算符](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/pointer-related-operators) 了解更多信息。

任何指针类型都可以隐式转换为 `void*` 类型。可以为任何指针类型分配值 `null`。可以使用强制转换表达式将任何指针类型显式转换为任何其他指针类型。也可以将任何整数类型转换为指针类型，或将任何指针类型转换为整数类型。这些转换需要显式转换。



## 1.2 函数指针

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/unsafe-code#function-pointers

C# 提供 `delegate` 类型来定义安全函数指针对象。调用委托时，需要实例化从 `System.Delegate` 派生的类型并对其 `Invoke` 方法进行虚拟方法调用。该虚拟调用使用 IL 指令 `callvirt`。在性能关键的代码路径中，使用 IL 指令 `calli` 效率更高。

可以使用 `delegate*` 语法定义函数指针。编译器将使用 `calli` 指令来调用函数，而不是实例化 `delegate` 对象并调用 `Invoke`。

以下代码声明了两种方法，它们使用 `delegate` 或 `delegate*` 来组合两个类型相同的对象。第一种方法使用 [`System.Func<T1,T2,TResult>`](https://learn.microsoft.com/en-us/dotnet/api/system.func-3) 委托类型，第二种方法使用具有相同参数和返回类型的 `delegate*` 声明：

```c#
public static T Combine<T>(Func<T, T, T> combinator, T left, T right) => 
    combinator(left, right);

public static T UnsafeCombine<T>(delegate*<T, T, T> combinator, T left, T right) => 
    combinator(left, right);
```

以下代码显示如何声明静态本地函数并使用指向该本地函数的指针调用 `UnsafeCombine` 方法：

```c#
static int localMultiply(int x, int y) => x * y;
int product = UnsafeCombine(&localMultiply, 3, 4);
```

上面的代码示例说明了有关作为函数指针使用的函数的几个规则：

- 函数指针只能在 `unsafe` 上下文中声明。
- 只能在 `unsafe` 上下文中调用采用 `delegate*` (或返回 `delegate*`) 的方法。
- 只可在 `static` 函数上使用 `&` 运算符获取函数的地址，此规则适用于成员函数和本地函数。

此语法与声明 `delegate` 类型和使用指针具有相似之处：`delegate` 上的后缀 `*` 表示声明是函数指针，将方法组分配给函数指针时，`&` 表示操作采用方法的地址。

可以使用关键字 `managed` 和 `unmanaged` 为 `delegate*` 指定调用约定。另外，对于 `unmanaged` 函数指针，可以指定调用约定。

下面的声明显示了每个示例：第一个声明使用 `managed` 调用约定，这是默认值；后面四个使用 `unmanaged` 调用约定。每个声明都指定以下某个 ECMA 335 调用约定：`Cdecl`、`Stdcall`、`Fastcall` 或 `Thiscall`。最后的声明使用 `unmanaged` 调用约定，指示 CLR 选择平台的默认调用约定。CLR 将在运行时选择调用约定。

```c#
public static T ManagedCombine<T>(delegate* managed<T, T, T> combinator, T left, T right) =>
    combinator(left, right);
public static T CDeclCombine<T>(delegate* unmanaged[Cdecl]<T, T, T> combinator, T left, T right) =>
    combinator(left, right);
public static T StdcallCombine<T>(delegate* unmanaged[Stdcall]<T, T, T> combinator, T left, T right) =>
    combinator(left, right);
public static T FastcallCombine<T>(delegate* unmanaged[Fastcall]<T, T, T> combinator, T left, T right) =>
    combinator(left, right);
public static T ThiscallCombine<T>(delegate* unmanaged[Thiscall]<T, T, T> combinator, T left, T right) =>
    combinator(left, right);
public static T UnmanagedCombine<T>(delegate* unmanaged<T, T, T> combinator, T left, T right) =>
    combinator(left, right);
```



## 1.3 unsafe

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/unsafe

`unsafe` 关键字表示不安全上下文，该上下文是任何涉及指针的操作所必需的。

可以在类型或成员的声明中使用 `unsafe` 修饰符。因此，类型或成员的整个正文范围均被视为不安全上下文。以下面使用 `unsafe` 修饰符声明的方法为例：

```c#
unsafe static void FastCopy(byte[] src, byte[] dst, int count)
{
    // Unsafe context: can use pointers here.
}
```

不安全上下文的范围从参数列表扩展到方法的结尾，因此也可以在以下参数列表中使用指针：

```c#
unsafe static void FastCopy ( byte* ps, byte* pd, int count ) {...}
```

还可以使用不安全块从而能够使用该块内的不安全代码。例如：

```c#
unsafe
{
    // Unsafe context: can use pointers here.
}
```

如果要编译不安全代码，必须指定 AllowUnsafeBlocks 编译器选项。不能通过 CLR 验证不安全代码。



## 1.4 stackalloc 表达式

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/stackalloc

`stackalloc` 表达式在栈上分配内存块。当该方法返回时，将自动丢弃在方法执行期间创建的已分配堆栈内存块。不能显式释放使用 `stackalloc` 分配的内存。堆栈中分配的内存块不受垃圾回收器的影响，也不必通过 `fixed` 语句固定。

可以将 `stackalloc` 表达式的结果分配给以下任一类型的变量：

- [`System.Span<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.span-1) 或 [`System.ReadOnlySpan<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.readonlyspan-1)。例如：

  ```c#
  int length = 3;
  Span<int> numbers = stackalloc int[length];
  for (var i = 0; i < length; i++)
  {
      numbers[i] = i;
  }
  ```

  将堆栈中分配的内存块分配给 `Span<T>` 或 `ReadOnlySpan<T>` 变量时，不必使用 `unsafe` 上下文。

  建议尽可能使用 `Span<T>` 或 `ReadOnlySpan<T>` 类型来处理堆栈中分配的内存。

- 指针类型。例如：

  ```c#
  unsafe
  {
      int length = 3;
      int* numbers = stackalloc int[length];
      for (var i = 0; i < length; i++)
      {
          numbers[i] = i;
      }
  }
  ```
  
  在使用指针类型时必须使用 `unsafe` 上下文。
  
  对于指针类型，只能在局部变量声明中使用 `stackalloc` 表达式来初始化变量。

堆栈上可用的内存量存在限制。如果在堆栈上分配过多的内存，会引发 [`StackOverflowException`](https://learn.microsoft.com/en-us/dotnet/api/system.stackoverflowexception)。为了避免这种情况，应遵循以下规则：

- 限制使用 `stackalloc` 分配的内存量。例如，如果预期的缓冲区大小低于特定限制，则在堆栈上分配内存；否则，使用所需长度的数组。

- 避免在循环内使用 `stackalloc`。在循环外分配内存块，然后在循环内重用它。

新分配的内存的内容未定义。在使用之前应对其进行初始化。例如，可以使用 [`Span<T>.Clear`](https://learn.microsoft.com/en-us/dotnet/api/system.span-1.clear) 方法将所有项设置为 `T` 类型的默认值。

可以使用数组初始值设定项语法来定义新分配的内存的内容。下面的示例显示了执行此操作的各种方法：

```c#
Span<int> first = stackalloc int[3] { 1, 2, 3 };
Span<int> second = stackalloc int[] { 1, 2, 3 };
ReadOnlySpan<int> third = stackalloc[] { 1, 2, 3 };

// Using collection expressions:
Span<int> fourth = [1, 2, 3];
ReadOnlySpan<int> fifth = [1, 2, 3];
```

在表达式 `stackalloc T[E]` 中，`T` 必须是非托管类型，并且 `E` 的计算结果必须为非负 int 值。当使用 [集合表达式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/collection-expressions) 语法来初始化范围时，如果编译器没有违反 ref 安全性，编译器可能会对范围使用堆栈分配的存储。



## 1.5 fixed 语句

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/fixed

`fixed` 语句可以防止 [垃圾回收器](https://learn.microsoft.com/en-us/dotnet/standard/garbage-collection/) 重新定位可移动变量，并声明指向该变量的指针。固定变量的地址在语句的持续时间内不会更改。只能在相应的 `fixed` 语句中使用声明的指针。声明的指针是只读的，无法修改。

可以在堆栈上分配内存，在这种情况下，内存不受垃圾回收的约束，因此不需要固定。为此，需要使用 `stackalloc` 表达式。

还可以使用 `fixed` 关键字声明 [固定大小的缓冲区](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/unsafe-code#fixed-size-buffers)。

