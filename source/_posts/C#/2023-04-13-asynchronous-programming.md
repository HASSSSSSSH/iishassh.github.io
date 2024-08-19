---
title: C# 异步编程
date: 2023-04-13
updated: 2024-08-15
permalink: /csharp/asynchronous-programming/
categories:
- [C#]
---

# 1. 异步编程

> https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/

[任务异步编程模型 (Task asynchronous programming model)](https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/task-asynchronous-programming-model) 提供了对异步代码的抽象。可以像平常一样将代码编写为一系列语句。可以阅读该代码，就好像每个语句在下一个语句开始之前完成一样。编译器执行许多转换，因为其中一些语句可能会开启工作并返回表示正在进行的工作的 [Task](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task)。



<!-- more -->

## 1.1 异步编程场景

> https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-scenarios

如果需要 I/O 绑定 (例如从网络请求数据、访问数据库或读取和写入到文件系统)，则需要利用异步编程。还可以使用 CPU 绑定代码 (例如执行成本高昂的计算)，对编写异步代码而言，这是一个不错的方案。

C# 拥有语言级别的异步编程模型，使得能够轻松编写异步代码，而无需应付回调或受限于支持异步的库。它遵循 [基于任务的异步模式 (Task-based asynchronous pattern)](https://learn.microsoft.com/en-us/dotnet/standard/asynchronous-programming-patterns/task-based-asynchronous-pattern-tap)。



### 1.1.1 异步模型概述

> https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-scenarios#overview-of-the-asynchronous-model

异步编程的核心是 `Task` 和 `Task<T>` 对象，这两个对象对异步操作建模。它们受关键字 `async` 和 `await` 的支持。

在大多数情况下模型十分简单：

- 对于 **I/O 绑定** 代码，等待一个在 `async` 方法中返回 `Task` 或 `Task<T>` 的操作。
- 对于 **CPU 绑定** 代码，等待一个使用 [`Task.Run`](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.run) 方法在后台线程启动的操作。



### 1.1.2 需了解的要点

> https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-scenarios#key-pieces-to-understand

- 异步代码可用于 I/O 绑定和 CPU 绑定代码，但在每个方案中有所不同。
- 异步代码使用 `Task<T>` 和 `Task`，它们是对后台所完成的工作进行建模的结构。
- `async` 关键字将方法转换为异步方法，这使得可以在其正文中使用 `await` 关键字。
- 应用 `await` 关键字后，它将挂起调用方法，并将控制权返还给调用方，直到等待的任务完成。
- 仅允许在异步方法中使用 `await`。



### 1.1.3 区分 CPU 绑定和 I/O 绑定工作

> https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-scenarios#recognize-cpu-bound-and-io-bound-work

确定所需执行的操作是 I/O 绑定或 CPU 绑定是关键，因为这会极大影响代码性能，并可能导致某些构造的误用。

以下是编写代码前应考虑的两个问题：

1. 代码是否会 "等待" 某些内容，例如数据库中的数据？

   如果答案为 "是"，则该工作是 I/O 绑定。

2. 代码是否要执行开销巨大的计算？

   如果答案为 "是"，则该工作是 CPU 绑定。

如果工作属于 I/O 绑定，应使用 `async` 和 `await` (而不使用 `Task.Run`)，不应使用任务并行库。

如果工作属于 CPU 绑定，并且重视响应能力，应使用 `async` 和 `await`，但在另一个线程上使用 `Task.Run` 生成工作。如果该工作同时适用于并发和并行，还应考虑使用 [任务并行库](https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/task-parallel-library-tpl)。



### 1.1.4 内部原理

> https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-scenarios#what-happens-under-the-covers

在 C# 方面，编译器将代码转换为状态机，它将跟踪类似以下内容：到达 `await` 时暂停执行以及后台作业完成时继续执行。

从理论上讲，这是 [异步的承诺模型](https://en.wikipedia.org/wiki/Futures_and_promises) 的实现。



## 1.2 async 和 await

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/async
>
> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/operators/await
>
> https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/task-asynchronous-programming-model#BKMK_AsyncandAwait

使用 `async` 修饰符可以将方法、lambda 表达式或者匿名方法指定为异步。如果对方法或表达式使用此修饰符，则其称为异步方法。

`await` 运算符暂停对其所属的 `async` 方法的求值，直到其操作数表示的异步操作完成。异步操作完成后，`await` 运算符将返回操作的结果 (如果有)。当 `await` 运算符应用到表示已完成操作的操作数时，它将立即返回操作的结果，而不会暂停其所属的方法。`await` 运算符不会阻止计算异步方法的线程。当 `await` 运算符暂停其所属的异步方法时，控制权将返回到方法的调用方。

只能在通过 `async` 关键字修饰的方法、lambda 表达式或匿名方法中使用 `await` 运算符。在异步方法中，不能在同步函数的本地主体、lock 语句块内以及不安全上下文中使用 `await` 运算符。

如果使用 `async` 修饰符将某种方法指定为异步方法，即启用以下两种功能：

- 标记的异步方法可以使用 `await` 来指定暂停点。`await` 运算符通知编译器异步方法：在等待的异步过程完成后才能继续通过该点。同时，控制返回至异步方法的调用方。

  异步方法在 `await` 表达式执行时暂停并不构成方法退出，只会导致 `finally` 代码块不运行。

- 标记的异步方法本身可以通过调用它的方法等待。

异步方法通常包含 `await` 运算符的一个或多个实例，但缺少 `await` 表达式也不会导致生成编译器错误。如果异步方法未使用 `await` 运算符标记暂停点，则该方法会作为同步方法执行，即使有 `async` 修饰符也不例外。编译器将为此类方法发布一个警告。



## 1.3 异步返回类型

> https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-return-types

异步方法可以具有以下返回类型：

- [`Task`](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task) (对于执行操作但不返回任何值的异步方法)
- [`Task<TResult>`](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task-1) (对于返回值的异步方法)
- `void` (对于事件处理程序)
- 任何具有可访问的 `GetAwaiter` 方法的类型。`GetAwaiter` 方法返回的对象必须实现 [`System.Runtime.CompilerServices.ICriticalNotifyCompletion`](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.compilerservices.icriticalnotifycompletion) 接口
- [`IAsyncEnumerable<T>`](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.iasyncenumerable-1) (对于返回异步流的异步方法)

异步方法无法声明 `in`、`ref` 或 `out` 参数，但可以调用包含此类参数的方法。同样，异步方法无法通过引用返回结果，但可以调用包含 `ref` 返回结果的方法。



### 1.3.1 Task 返回类型

不包含 `return` 语句的异步方法或包含不返回操作数的 `return` 语句的异步方法通常具有返回类型 `Task`。如果此类方法同步运行，它们将返回 `void`。如果在异步方法中使用 `Task` 返回类型，调用方法可以使用 `await` 运算符暂停调用方的完成，直至被调用的异步方法结束。



### 1.3.2 Task\<TResult> 返回类型

`Task<TResult>` 返回类型用于某种异步方法，此异步方法包含 `return` 语句，其中操作数是 `TResult`。



### 1.3.3 Void 返回类型

在异步事件处理程序中使用 `void` 返回类型，这需要 `void` 返回类型。对于事件处理程序以外的不返回值的方法，应返回 `Task`，因为无法对返回 `void` 的异步方法进行 `await` 操作。

此类方法的任何调用方都必须继续执行，而无需等待调用的异步方法完成，调用方必须独立于异步方法生成的任何值或异常。

`Void` 返回异步方法的调用方无法捕获从该方法引发的异常，此类未经处理异常有可能导致应用程序失败。如果返回 `Task` 或 `Task<TResult>` 的方法引发异常，则该异常存储在返回的任务中，等待任务时，将重新引发异常。应确保可以产生异常的任何异步方法都具有返回类型 `Task` 或 `Task<TResult>`，并确保会等待对方法的调用。



### 1.3.4 通用的异步返回类型和 ValueTask\<TResult>

异步方法可以返回具有返回 awaiter 类型实例的可访问 `GetAwaiter` 方法的所有类型。此外，`GetAwaiter` 方法返回的类型必须具有 [`System.Runtime.CompilerServices.AsyncMethodBuilderAttribute`](https://learn.microsoft.com/en-us/dotnet/api/system.runtime.compilerservices.asyncmethodbuilderattribute) 特性。

此功能与 [`awaitable` 表达式](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/expressions#12982-awaitable-expressions) 相辅相成，后者描述 `await` 操作数的要求。编译器可以使用通用异步返回类型生成返回不同类型的 `async` 方法。通用异步返回类型通过 .NET 库实现性能改进。`Task` 和 `Task<TResult>` 是引用类型，因此，性能关键路径中的内存分配会对性能产生负面影响，尤其当分配出现在紧凑循环中时。支持通用返回类型意味着可返回轻量值类型 (而不是引用类型)，从而避免额外的内存分配。

.NET 提供 [`System.Threading.Tasks.ValueTask`](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.valuetask-1) 结构作为返回任务的通用值的轻量实现。



### 1.3.5 使用 IAsyncEnumerable\<T> 的异步流

异步方法可能返回异步流，由 `IAsyncEnumerable<T>` 表示。异步流提供了一种方法，来枚举在具有重复异步调用的块中生成元素时从流中读取的项。



## 1.4 异步方法中的异常

> https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/exception-handling-statements#exceptions-in-async-and-iterator-methods
>
> https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/#asynchronous-exceptions

应用了 `await` 的已完成任务可能由于返回此任务的方法中存在未处理的异常而处于错误状态，对该任务应用 `await` 可以抛出异常。如果返回任务的异步进程被取消，任务也可能以取消状态结束。对已取消的任务应用 `await` 将抛出 `OperationCanceledException`。

当异步运行的代码抛出异常时，该异常存储在 `Task` 中。[`Task.Exception`](https://learn.microsoft.com/en-us/dotnet/api/system.threading.tasks.task.exception#system-threading-tasks-task-exception) 属性是一个 [`System.AggregateException`](https://learn.microsoft.com/en-us/dotnet/api/system.aggregateexception)，因为在异步工作期间可能会抛出多个异常。抛出的任何异常都会添加到 [`AggregateException.InnerExceptions`](https://learn.microsoft.com/en-us/dotnet/api/system.aggregateexception.innerexceptions#system-aggregateexception-innerexceptions) 集合中。如果该 `Exception` 属性为 `null`，则会创建一个新的 `AggregateException`，并且抛出的异常是集合中的第一项。

出现故障的任务最常见的情况是 `Exception` 属性只包含一个异常。当代码 `await` 故障任务时，将重新抛出 `AggregateException.InnerExceptions` 集合中的第一个异常。当程序可能出现多个异常时，可以检查代码中的 `Exception` 属性。



## 1.5 重要信息和建议

> https://learn.microsoft.com/en-us/dotnet/csharp/asynchronous-programming/async-scenarios#important-info-and-advice

对于异步编程，有一些细节需要注意，以防止意外行为。

- **`async` 方法需要在主体中有 `await` 关键字，否则方法将永不暂停！**

  这一点需要牢记在心。如果 `await` 未用在 `async` 方法的主体中，C# 编译器将生成一个警告，但此代码将会以类似普通方法的方式进行编译和运行。这种方式非常低效，因为由 C# 编译器为异步方法生成的状态机将不会完成任何任务。

- **添加 "Async" 作为编写的每个异步方法名称的后缀**

  这是 .NET 中的惯例，以便更为轻松地区分同步和异步方法。未由代码显式调用的某些方法 (如事件处理程序或 Web 控制器方法) 并不一定适用。由于它们未由代码显式调用，因此对其显式命名并不重要。

- **`async void` 应仅用于事件处理程序**

  `async void` 是允许异步事件处理程序工作的唯一方法，因为事件不具有返回类型，因此无法利用 `Task` 和 `Task<T>`。其他任何对 `async void` 的使用都不遵循 TAP 模型，且可能存在一定使用难度，例如：

  - `async void` 方法中引发的异常无法在该方法外部被捕获。
  - `async void` 方法很难测试。
  - `async void` 方法可能会导致不良副作用 (如果调用方不希望方法是异步的话)。

- **在 LINQ 表达式中使用异步 lambda 时应谨慎**

  LINQ 中的 Lambda 表达式使用延迟执行，这意味着代码可能在并不希望结束的时候停止执行。如果编写不正确，将阻塞任务引入其中时可能很容易导致死锁。此外，此类异步代码嵌套可能会对推断代码的执行带来更多困难。Async 和 LINQ 的功能都十分强大，但在结合使用两者时应尽可能小心。

- **采用非阻塞方式编写等待任务的代码**

  通过阻塞当前线程来等待 `Task` 完成的方法可能导致死锁和已阻止的上下文线程，且可能需要更复杂的错误处理方法。下表提供了关于如何以非阻塞方式处理等待任务的指南：

  | 使用以下方式...      | 而不是…                      | 当需要执行此操作... |
  | :------------------- | :--------------------------- | :------------------ |
  | `await`              | `Task.Wait` 或 `Task.Result` | 检索后台任务的结果  |
  | `await Task.WhenAny` | `Task.WaitAny`               | 等待任何任务完成    |
  | `await Task.WhenAll` | `Task.WaitAll`               | 等待所有任务完成    |
  | `await Task.Delay`   | `Thread.Sleep`               | 等待一段时间        |

- **如果可能，考虑使用 `ValueTask`**

  从异步方法返回 `Task` 对象可能在某些路径中导致性能瓶颈。`Task` 是引用类型，因此使用它意味着分配对象。如果使用 `async` 修饰符声明的方法返回缓存结果或以同步方式完成，那么额外的分配在代码的性能关键部分可能要耗费相当长的时间。如果这些分配发生在紧凑循环中，则成本会变高。

