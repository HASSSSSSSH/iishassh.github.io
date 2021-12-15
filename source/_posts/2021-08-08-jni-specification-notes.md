---
title: JNI 规范笔记
date: 2021-08-08 17:44:16
categories:
- Java
---



<!-- TODO: 关于 JavaVM 和 JNIEnv -->

<!-- TODO: 一些常用的 JNI 函数 -->

<!-- TODO: 本地代码调用 Java 方法 -->

<!-- TODO: 局部引用和全局引用 -->

<!-- TODO: 线程 -->

<!-- TODO: 异常处理 -->

<!-- TODO: System.loadLibrary 的加载过程? JNI_OnLoad 的调用过程? -->

<!-- TODO: 是否有加载本地库的其他方式? -->

<!-- TODO: 静态注册和动态注册的本地方法查找过程? 什么是 "让 runtime 使用 dlsym 进行动态查找"? -->

<!-- TODO: 为什么 c++ 需要写 "extern "C""? -->

<!-- TODO: JNIEXPORT? JNICALL? -->

<!-- TODO: reinterpret_cast 是什么? -->



# 1. 前言

**JNI (Java Native Interface)** 定义了运行在虚拟机中的 Java 代码与本地代码 (使用 C, C++ 等语言编写) 交互的方式。

JNI 规范可在 [Java Native Interface Specification](https://docs.oracle.com/javase/7/docs/technotes/guides/jni/spec/jniTOC.html) 查阅，接下来对规范中的部分内容做些笔记。



<!-- more -->

# 2. JNI 的数据类型



## 2.1 基本类型

下面列出 Java 基本类型与本地类型的对应关系：

| Java 类型 | 本地类型 | 说明         |
| --------- | -------- | ------------ |
| boolean   | jboolean | 无符号 8 位  |
| byte      | jbyte    | 有符号 8 位  |
| char      | jchar    | 无符号 16 位 |
| short     | jshort   | 有符号 16 位 |
| int       | jint     | 有符号 32 位 |
| long      | jlong    | 有符号 64 位 |
| float     | jfloat   | 32 位        |
| double    | jdouble  | 64 位        |
| void      | void     | N/A          |



## 2.2 引用类型

JNI 包含了许多对应于不同 Java 对象的引用类型，这些引用类型的组织结构如图所示：

![JNI-reference-types-hierarchy](2021-08-08-jni-specification-notes/JNI-reference-types-hierarchy.gif)

在 C 中，所有的引用类型都被定义成和类型 `jobject` 一样：

```c
typedef void*           jobject;
typedef jobject         jclass;
typedef jobject         jstring;
typedef jobject         jarray;
typedef jarray          jobjectArray;
...
```

在 C++ 中，JNI 引入了一个空类 `_jobject` 来增加类型之间的继承关系：

```c++
class _jobject {};
class _jclass : public _jobject {};
class _jstring : public _jobject {};
class _jarray : public _jobject {};
class _jobjectArray : public _jarray {};
...

typedef _jobject*       jobject;
typedef _jclass*        jclass;
typedef _jstring*       jstring;
typedef _jarray*        jarray;
typedef _jobjectArray*  jobjectArray;
...
```



## 2.3 类型签名

JNI 使用的是 Java 虚拟机的类型签名，如下表所示：

| 类型签名                    | Java 类型  |
| --------------------------- | ---------- |
| Z                           | boolean    |
| B                           | byte       |
| C                           | char       |
| S                           | short      |
| I                           | int        |
| J                           | long       |
| F                           | float      |
| D                           | double     |
| V                           | void       |
| L *fully-qualified-class* ; | 全限定类名 |
| [ *type*                    | *type*[]   |
| ( *arg-types* ) *ret-type*  | 方法类型   |



### 2.3.1 例子

对于 Java 方法：

```java
long f(int n, String s, int[] arr);
```

其对应的类型签名为：

```
(ILjava/lang/String;[I)J
```



# 3. 本地方法的命名和参数



## 3.1 命名

一般地，本地方法的名称由以下几部分组成：

- 前缀 `Java_`
- 全限定类名
- 下划线 `_` 分隔符
- 方法名
- 对于重载的本地方法，需要加上两个下划线 `__`，然后加上方法的参数签名

同时，为了确保所有 Unicode 字符都转换为有效的 C 函数名称，应使用下划线 `_` 来替代全限定类名中的斜杠 `/`。

下面列出 Unicode 字符的转换关系：

| **转义字符序列** | 表示                     |
| ---------------- | ------------------------ |
| _0XXXX           | 一个 Unicode 字符 `XXXX` |
| _1               | 字符 `_`                 |
| _2               | 在签名中的字符 `;`       |
| _3               | 在签名中的字符 `[`       |



## 3.2 参数

- 本地方法的第一个参数固定为 JNI 接口指针，指针类型是 `JNIEnv`。关于 `JNIEnv` 有以下几个要点：
  - `JNIEnv` 具有指向函数表的指针，通过 `JNIEnv` 可以调用大部分的 JNI 函数。
  - `JNIEnv` 是线程私有的，无法在线程之间共享一个 `JNIEnv`。

- 第二个参数的类型为 `jobject`。如果 Java 代码上定义的本地方法是非静态方法，那么 `jobject` 是对该 Java 对象的引用；如果 Java 代码上定义的本地方法是静态方法，那么 `jobject` 是对该 Java 类对象的引用。

- 其余的参数对应于 Java 方法上的参数。



## 3.3 例子

Java 代码中，在 com/example 包的 Main 类定义了本地方法：

```java
native int hello();
```

则在 C/C++ 中，其对应的本地方法为：

```c
jint Java_com_example_Main_hello(JNIEnv * env, jobject obj);
```

如果在 Main 类中还定义了 hello 的重载方法：

```java
native int hello(int n, String s, int [] arr);
```

那么在 C/C++ 中，其对应的本地方法为：

```c
jint Java_com_example_Main_hello__ILjava_lang_String_2_3I(JNIEnv * env, jobject obj, jint i, jstring s, jintArray arr);
```

而且，对于原本定义的 Java 本地方法：

```java
native int hello();
```

其对应的 C/C++ 本地方法需要改为：

```c
jint Java_com_example_Main_hello__(JNIEnv * env, jobject obj);
```



# 4. 加载本地方法

在 Java 代码中调用 `System.loadLibrary` 方法，可以从本地库中加载本地方法。

一般地，如果一个类定义了本地方法，那么可以将 `System.loadLibrary` 方法的调用放置于类的静态代码块中，该代码块会随着类的初始化而执行。

例如：

```java
package pkg;

class Cls {

     native double f(int i, String s);

     static {
         System.loadLibrary("XXXX");
     }
}
```

其中 `"XXXX"` 表示目标本地库的名称。



# 5. 注册本地方法

按照[章节三](#3-本地方法的命名和参数)的规范，便可以将 Java 类中的本地方法和使用本地代码 (C/C++) 编写的函数建立起对应关系，但是这种方式会使得方法的命名较为复杂。除了这种方式以外，JNI 还提供了显式注册本地方法的方式。



## 5.1 显式注册本地方法

JNI 提供了 `RegisterNatives` 函数，可用于显式地注册本地方法，其函数原型为 `jint RegisterNatives(JNIEnv *env, jclass clazz, const JNINativeMethod *methods, jint nMethods)`。

现在来分析函数的入参：

- **env**  是 JNI 接口指针，指针类型为 JNIEnv。

- **clazz** 的类型为 jclass，是目标本地方法所在  Java 类的类对象。

- **methods** 是一个 JNINativeMethod 指针，用于指定要注册的本地方法。其中 JNINativeMethod 是一个结构体，其定义如下：

  ```c
  typedef struct {
  
      char *name;
  
      char *signature;
  
      void *fnPtr;
  
  } JNINativeMethod;
  ```

  - 结构体成员 **name** 是本地方法的名称。

  - 结构体成员 **signature** 是本地方法的[类型签名](#2-3-类型签名)。

  - 结构体成员 **fnPtr** 是一个函数指针，指向本地方法的实现。此函数指针名义上应当有如下的函数签名：

    ```c
    ReturnType (*fnPtr)(JNIEnv *env, jobject objectOrClass, ...);
    ```

- **nMethods** 表示本地方法的数量，即参数 methods 所包含 JNINativeMethod 的数量。

通过显式注册的本地方法，其方法名不再需要按照 [[3.1]](#3-1-命名) 的规则进行命名。

下面给出显式注册本地方法的例子。



### 5.1.1 例子

Java 代码中，在 com/example 包的 Main 类定义了本地方法：

```java
native void nativeInit();

native String nativeGetName(byte[] data, String str);
```

同时，使用 C 实现了本地方法：

```c
static void nativeInit(JNIEnv* env, jobject obj) {
    ...
}

static jstring nativeGetName(JNIEnv* env, jobject obj, jobjectArray data, jstring str) {
    ...
}
```

由于 C 实现的本地方法并未按照 JNI 规范进行命名，此时需要显式注册本地方法。

当本地库被加载时 (例如：在 Java 上调用 `System.loadLibrary` 方法)，Java 虚拟机会调用 `JNI_OnLoad` 函数，此时是注册本地方法的一个好的时机。因此可以通过实现 `JNI_OnLoad` 函数，在函数中调用 `RegisterNatives` 注册本地方法：

```c
JNIEXPORT jint JNI_OnLoad(JavaVM* vm, void* reserved) {
    JNIEnv* env;
    // 获取 JNI 接口指针以调用 JNI 函数
    if ((*vm)->GetEnv(vm, (void**) (&env), JNI_VERSION_1_6) != JNI_OK) {
        return JNI_ERR;
    }

    // 获取 Java 类对象
    jclass class = (*env)->FindClass(env, "com/example/Main");

    // 指定要注册的本地方法
    static const JNINativeMethod methods[] = {
            {"nativeInit",    "()V",                                      (void*) (nativeInit)},
            {"nativeGetName", "([BLjava/lang/String;)Ljava/lang/String;", (void*) (nativeGetName)},
    };

    // 调用 RegisterNatives 函数注册本地方法
    int rc = (*env)->RegisterNatives(env, class, methods, sizeof(methods) / sizeof(JNINativeMethod));
    if (rc != JNI_OK)
        return rc;

    // 返回需要的 JNI 版本
    return JNI_VERSION_1_6;
}
```



# 参考

 [JNI tips](https://developer.android.com/training/articles/perf-jni)

