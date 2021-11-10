---
title: 分析 init 进程的启动流程
date: 2021-07-10 16:28:09
categories:
- [Android]
---



> 本文基于源代码：android-security-10.0.0_r56



# 1. 前言

<!-- TODO: 关于 init 进程更好的概述 -->

Android 基于 Linux 内核，在内核启动过程中会创建出 init 进程。

init 进程是用户空间的第一个进程，进程的 pid = 1。

<!-- more -->



# 2. 启动流程

<!-- TODO: 总结 -->



## 2.1 init 进程的入口

<!-- TODO: 为什么是入口？ -->

<!-- TODO: init 就是一个普通的命令行程序, 位置在 /system/core/init？ -->

在 Android 9，init 进程的入口位于 `system/core/init/init.cpp` 中的 main 函数。

在 Android 10 之后，init 进程的入口改到了 `system/core/init/main.cpp` 中的 main 函数。

```c++
int main(int argc, char** argv) {
    ...

    if (!strcmp(basename(argv[0]), "ueventd")) {
        return ueventd_main(argc, argv);
    }

    if (argc > 1) {
        if (!strcmp(argv[1], "subcontext")) {
            android::base::InitLogging(argv, &android::base::KernelLogger);
            const BuiltinFunctionMap& function_map = GetBuiltinFunctionMap();

            return SubcontextMain(argc, argv, &function_map);
        }

        if (!strcmp(argv[1], "selinux_setup")) {
            return SetupSelinux(argv);
        }

        if (!strcmp(argv[1], "second_stage")) {
            return SecondStageMain(argc, argv);
        }
    }

    return FirstStageMain(argc, argv);
}
```

首先来了解其中使用到的两个库函数：`basename` 函数和 `strcmp` 函数。

- `basename` 的函数原型为 `char* basename(char* __path)`，可以根据给定的一个路径，返回文件名。例如：传入参数 "/system/bin/ueventd"，函数会返回 "ueventd"。
- `strcmp` 的函数原型为 `int strcmp(const char* __lhs, const char* __rhs)`，是一个字符串比较函数，返回值为整数。当两个字符串相等时，函数返回 0。

再来分析 main 函数：函数的主要工作是根据参数 `argc` 和 `argv`，选择对应的执行方式。如果参数未匹配成功，则默认调用 `FirstStageMain` 函数。



## 2.2 启动的第一阶段

<!-- TODO: mkdir, mknod, tmpfs -->

<!-- TODO: 所挂载的文件系统的用处 -->

<!-- TODO: KernelLogging(/dev/kmsg) -->

<!-- TODO: InstallRebootSignalHandlers -->

调用 `int FirstStageMain(int argc, char** argv)` 进入启动的第一阶段，函数所在文件的路径为 `system/core/init/first_stage_init.cpp`：

```c++
int FirstStageMain(int argc, char** argv) {
    ...

    CHECKCALL(clearenv());
    CHECKCALL(setenv("PATH", _PATH_DEFPATH, 1));
    // Get the basic filesystem setup we need put together in the initramdisk
    // on / and then we'll let the rc file figure out the rest.
    CHECKCALL(mount("tmpfs", "/dev", "tmpfs", MS_NOSUID, "mode=0755"));
    CHECKCALL(mkdir("/dev/pts", 0755));
    CHECKCALL(mkdir("/dev/socket", 0755));
    CHECKCALL(mkdir("/dev/dm-user", 0755));
    CHECKCALL(mount("devpts", "/dev/pts", "devpts", 0, NULL));
#define MAKE_STR(x) __STRING(x)
    CHECKCALL(mount("proc", "/proc", "proc", 0, "hidepid=2,gid=" MAKE_STR(AID_READPROC)));
#undef MAKE_STR

    // Don't expose the raw commandline to unprivileged processes.
    // 设置文件权限 [2.2.1]
    CHECKCALL(chmod("/proc/cmdline", 0440));
    std::string cmdline;
    android::base::ReadFileToString("/proc/cmdline", &cmdline);
    // Don't expose the raw bootconfig to unprivileged processes.
    chmod("/proc/bootconfig", 0440);
    std::string bootconfig;
    android::base::ReadFileToString("/proc/bootconfig", &bootconfig);
    gid_t groups[] = {AID_READPROC};
    CHECKCALL(setgroups(arraysize(groups), groups));
    CHECKCALL(mount("sysfs", "/sys", "sysfs", 0, NULL));
    CHECKCALL(mount("selinuxfs", "/sys/fs/selinux", "selinuxfs", 0, NULL));

    CHECKCALL(mknod("/dev/kmsg", S_IFCHR | 0600, makedev(1, 11)));

    if constexpr (WORLD_WRITABLE_KMSG) {
        CHECKCALL(mknod("/dev/kmsg_debug", S_IFCHR | 0622, makedev(1, 11)));
    }

    CHECKCALL(mknod("/dev/random", S_IFCHR | 0666, makedev(1, 8)));
    CHECKCALL(mknod("/dev/urandom", S_IFCHR | 0666, makedev(1, 9)));

    // This is needed for log wrapper, which gets called before ueventd runs.
    CHECKCALL(mknod("/dev/ptmx", S_IFCHR | 0666, makedev(5, 2)));
    CHECKCALL(mknod("/dev/null", S_IFCHR | 0666, makedev(1, 3)));

    // These below mounts are done in first stage init so that first stage mount can mount
    // subdirectories of /mnt/{vendor,product}/.  Other mounts, not required by first stage mount,
    // should be done in rc files.
    // Mount staging areas for devices managed by vold
    // See storage config details at http://source.android.com/devices/storage/
    CHECKCALL(mount("tmpfs", "/mnt", "tmpfs", MS_NOEXEC | MS_NOSUID | MS_NODEV,
                    "mode=0755,uid=0,gid=1000"));
    // /mnt/vendor is used to mount vendor-specific partitions that can not be
    // part of the vendor partition, e.g. because they are mounted read-write.
    CHECKCALL(mkdir("/mnt/vendor", 0755));
    // /mnt/product is used to mount product-specific partitions that can not be
    // part of the product partition, e.g. because they are mounted read-write.
    CHECKCALL(mkdir("/mnt/product", 0755));

    // /debug_ramdisk is used to preserve additional files from the debug ramdisk
    CHECKCALL(mount("tmpfs", "/debug_ramdisk", "tmpfs", MS_NOEXEC | MS_NOSUID | MS_NODEV,
                    "mode=0755,uid=0,gid=0"));

    // /second_stage_resources is used to preserve files from first to second
    // stage init
    CHECKCALL(mount("tmpfs", kSecondStageRes, "tmpfs", MS_NOEXEC | MS_NOSUID | MS_NODEV,
                    "mode=0755,uid=0,gid=0"))
#undef CHECKCALL

    SetStdioToDevNull(argv);
    // Now that tmpfs is mounted on /dev and we have /dev/kmsg, we can actually
    // talk to the outside world...
    InitKernelLogging(argv);

    if (!errors.empty()) {
        for (const auto& [error_string, error_errno] : errors) {
            LOG(ERROR) << error_string << " " << strerror(error_errno);
        }
        LOG(FATAL) << "Init encountered errors starting first stage, aborting";
    }

    LOG(INFO) << "init first stage started!";

    ...

    // 执行 /system/bin/init
    // 注意到传入的参数是 selinux_setup, 因此接下来会调用 SetupSelinux 函数进行 SELinux 的初始化 [2.3]
    const char* path = "/system/bin/init";
    const char* args[] = {path, "selinux_setup", nullptr};
    auto fd = open("/dev/kmsg", O_WRONLY | O_CLOEXEC);
    dup2(fd, STDOUT_FILENO);
    dup2(fd, STDERR_FILENO);
    close(fd);
    execv(path, const_cast<char**>(args));

    // execv() only returns if an error happened, in which case we
    // panic and never fall through this conditional.
    PLOG(FATAL) << "execv(\"" << path << "\") failed";

    return 1;
}
```

此函数主要工作是挂载一些文件系统，然后执行 `/system/bin/init`，重新进入 main 函数，通过传入参数 `"selinux_setup"`，进而调用 SetupSelinux 函数，进行 SELinux 的初始化。



### 2.2.1 设置文件或目录的权限

在 Linux 系统上，`chmod` 命令用于控制用户对文件或者目录的权限，但只有文件所有者和超级用户可以修改文件或目录的权限。

权限分为三类：读，写，执行。



#### 2.2.1.1 符号表示法

符号表示法用 10 位字符表示权限，其形式为：

```
-rwxrwxrwx
```

其中第一个字符表示文件类型，常见的符号有：

- `-`，表示普通文件。
- `d`，表示目录。
- `c`，表示字符特殊文件。

剩余的 9 个字符，每 3 个字符分为一组，分别表示文件所有者，用户组，其他用户的权限。

3 个字符组成的字符串，第 1 位控制读权限，第 2 位控制写权限，第 3 位控制执行权限：

- 如果拥有读权限，第 1 位字符为 `r`，否则为 `-`。
- 如果拥有写权限，第 2 位字符为 `w`，否则为 `-`。
- 如果拥有执行权限，第 3 位字符为 `x`，否则为 `-`。

示例：`-rwxrw-r--` 表示一个普通文件，文件所有者拥有读，写和执行权限，用户组拥有读和写权限，其他用户只有读权限。



#### 2.2.1.2 数字表示法

可以用 4 位八进制数字表示权限，第 1 位同样表示文件类型，剩余的 3 位数字，分别表示文件所有者，用户组，其他用户的权限。

读，写，执行，这 3 类权限可以使用 3 位二进制数字表示，不同类型的权限单独对应二进制数字中一个位：

|          数值          | 权限 |
| :--------------------: | :--: |
| 二进制：100，八进制：4 |  r   |
| 二进制：010，八进制：2 |  w   |
| 二进制：001，八进制：1 |  x   |
| 二进制：000，八进制：0 |  -   |

在各类权限对应的数值之和，可以代表一组特定的权限。由于和的数值范围为 0~7，因此可以通过一个八进制数字表示。示例：

|          数值          | 权限 |
| :--------------------: | :--: |
| 二进制：111，八进制：7 | rwx  |
| 二进制：110，八进制：6 | rw-  |
| 二进制：101，八进制：5 | r-x  |
| 二进制：000，八进制：0 | ---  |

因此，用符号表示法表示的权限，同样可以使用数字表示法来表示，示例：

| 符号表示法 | 数字表示法 |                             说明                             |
| :--------: | :--------: | :----------------------------------------------------------: |
| -rwxrwxrwx |    0777    | 一个普通文件，文件所有者，用户组，其他用户都有读，写，执行的权限 |
| -rwxrw-r-- |    0764    | 一个普通文件，文件所有者拥有读，写和执行权限，用户组拥有读和写权限，其他用户只有读权限。 |



#### 2.2.1.3 分析源码中的权限设置

```c++
// Don't expose the raw commandline to unprivileged processes.
CHECKCALL(chmod("/proc/cmdline", 0440));

// Don't expose the raw bootconfig to unprivileged processes.
chmod("/proc/bootconfig", 0440);
```

在源码中有两处使用到 `chmod` 命令，分别对文件 `/proc/cmdline`，`/proc/bootconfig` 设置了权限 `0440`，表明 root 用户 (文件所有者) 及其用户组也只有文件的读权限。



## 2.3 初始化 SELinux

<!-- TODO: 分析 SELinux -->

调用 `int SetupSelinux(char** argv)` 进行 SELinux 的初始化，该函数所在文件的路径为 `system/core/init/selinux.cpp`：

```c++
// This function initializes SELinux then execs init to run in the init SELinux context.
int SetupSelinux(char** argv) {
    ...

    // Set up SELinux, loading the SELinux policy.
    // 初始化 SELinux
    SelinuxSetupKernelLogging();
    SelinuxInitialize();

    ...

    // 执行 /system/bin/init
    // 注意到传入的参数是 second_stage, 因此接下来会调用 SecondStageMain 函数进入启动的第二阶段 [2.4]
    const char* path = "/system/bin/init";
    const char* args[] = {path, "second_stage", nullptr};
    execv(path, const_cast<char**>(args));

    ...

    return 1;
}
```

函数首先初始化了 SELinux，然后执行 `/system/bin/init`，重新进入 main 函数，通过传入参数 `"second_stage"`，进而调用 SecondStageMain 函数，进入启动的第二阶段。



## 2.4 启动的第二阶段

<!-- TODO: property_load_boot_defaults -->

<!-- TODO: 在编译的过程中, android 的各种系统参数会被汇总到 build.prop 文件中？ -->

<!-- TODO: SELinux 相关代码的分析 -->

<!-- TODO: keyctl_get_keyring_ID(KEY_SPEC_SESSION_KEYRING, 1) -->

<!-- TODO: InitializeSubcontext() -->

<!-- TODO: ActionManager 和 ServiceList 的作用 -->

调用 `int SecondStageMain(int argc, char** argv)` 进入启动的第二阶段，函数所在文件的路径为 `system/core/init/init.cpp`：

```c++
int SecondStageMain(int argc, char** argv) {
    ...

    // Set init and its forked children's oom_adj.
    // 设置 init 进程以及由其 fork 得到的子进程 oom_score_adj 的值
    // 数值越小, 进程优先级越高
    if (auto result = WriteFile("/proc/1/oom_score_adj", "-1000"); !result) {
        LOG(ERROR) << "Unable to write -1000 to /proc/1/oom_score_adj: " << result.error();
    }

    ...

    // 初始化属性服务 [2.4.2.1]
    property_init();

    ...

    // 初始化 epoll [2.4.1.1]
    Epoll epoll;
    if (auto result = epoll.Open(); !result) {
        PLOG(FATAL) << result.error();
    }

    // 使用 epoll 对 init 子进程的信号进行监听 [2.4.3.1.3]
    // 初始化子进程退出的信号处理函数
    InstallSignalFdHandler(&epoll);

    ...

    // 开启属性服务 [2.4.2.2]
    StartPropertyService(&epoll);

    ...

    ActionManager& am = ActionManager::GetInstance();
    ServiceList& sm = ServiceList::GetInstance();

    // 加载启动脚本 [2.4.4]
    LoadBootScripts(am, sm);

    ...

    // 添加 Action [2.4.5.1]
    am.QueueBuiltinAction(SetupCgroupsAction, "SetupCgroups");
    am.QueueEventTrigger("early-init");
    am.QueueBuiltinAction(wait_for_coldboot_done_action, "wait_for_coldboot_done");
    ...
    am.QueueBuiltinAction(queue_property_triggers_action, "queue_property_triggers");

    // 进入无限循环状态 [2.4.5]
    while (true) {
        auto epoll_timeout = std::optional<std::chrono::milliseconds>{};

        if (do_shutdown && !shutting_down) {
            do_shutdown = false;
            if (HandlePowerctlMessage(shutdown_command)) {
                shutting_down = true;
            }
        }

        if (!(waiting_for_prop || Service::is_exec_service_running())) {
            am.ExecuteOneCommand();
        }
        if (!(waiting_for_prop || Service::is_exec_service_running())) {
            if (!shutting_down) {
                auto next_process_action_time = HandleProcessActions();

                if (next_process_action_time) {
                    epoll_timeout = std::chrono::ceil<std::chrono::milliseconds>(
                            *next_process_action_time - boot_clock::now());
                    if (*epoll_timeout < 0ms) epoll_timeout = 0ms;
                }
            }

            if (am.HasMoreCommands()) epoll_timeout = 0ms;
        }

        if (auto result = epoll.Wait(epoll_timeout); !result) {
            LOG(ERROR) << result.error();
        }
    }

    return 0;
}
```

第二阶段的工作可以大致分为 5 个部分：

- epoll

- 属性服务

- 信号

- 加载启动脚本

- 进入无限循环状态

接下来将分别对这 5 个部分的工作进行分析。



### 2.4.1 epoll

epoll 是 Linux 提供的 I/O 事件通知机制。epoll 可以监听大量的文件描述符，检查其中某一个文件描述符是否进入就绪状态。



#### 2.4.1.1 初始化

在 init 进程中，epoll 通过以下代码初始化：

```c++
Epoll epoll;
if (auto result = epoll.Open(); !result) {
    PLOG(FATAL) << result.error();
}
```

可以知道，epoll 实例是通过调用 Open 函数创建的，函数所在文件的路径为 `system/core/init/epoll.cpp`，来分析这个函数：

```c++
Result<Success> Epoll::Open() {
    if (epoll_fd_ >= 0) return Success();

    // 关键点
    epoll_fd_.reset(epoll_create1(EPOLL_CLOEXEC));

    if (epoll_fd_ == -1) {
        return ErrnoError() << "epoll_create1 failed";
    }
    return Success();
}
```

此函数的关键点在于调用 `epoll_create1`。`epoll_create1` 是一个系统调用，其新建了一个 epoll 实例并返回该实例文件描述符。



#### 2.4.1.2 使用 epoll 监听文件描述符

要使用 epoll 监听文件描述符，需要调用 RegisterHandler 函数，将文件描述符注册到 epoll。

函数所在文件的路径为 `system/core/init/epoll.cpp`，此函数的关键点在于系统调用 epoll_ctl：

```c++
Result<Success> Epoll::RegisterHandler(int fd, std::function<void()> handler, uint32_t events) {
    ...

    // 关键点
    if (epoll_ctl(epoll_fd_, EPOLL_CTL_ADD, fd, &ev) == -1) {

        ...

        return result;
    }
    return Success();
}
```

首先来看下 epoll_ctl 这个系统调用。



##### 2.4.1.2.1 系统调用 epoll_ctl

**epoll_ctl** 是一个系统调用，函数原型为 `int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)`，用于对指定的文件描述符 fd 进行添加、修改或者删除操作。



###### 2.4.1.2.1.1 参数

首先来理解 epoll_ctl 的参数：

- **epfd** 是 epoll 实例的文件描述符。

- **op** 表示要执行的操作，有三种取值：

  - **EPOLL_CTL_ADD**

    添加指定的文件描述符到 epoll 的监控列表。

  - **EPOLL_CTL_MOD**

    修改 epoll 的监控列表中指定的文件描述符。

  - **EPOLL_CTL_DEL**

    从 epoll 的监控列表中删除指定的文件描述符。

- **fd** 表示指定的文件描述符。

- **event** 是一个 epoll_event 类型的指针，而 epoll_event 是一个结构体，其定义如下：

  ```c++
  struct epoll_event {
     uint32_t     events;      /* Epoll events */
     epoll_data_t data;        /* User data variable */
  };
  ```

  结构体成员 `events` 用于表示要监听的事件，可以由零个或者多个事件类型组成，下面列出部分常见的事件类型：

  - **EPOLLIN**
    表示关联的文件描述符可用于读操作。

  - **EPOLLOUT**
    表示关联的文件描述符可用于写操作。

  - **EPOLLPRI**
    表示关联的文件描述符出现异常情况。

  - **EPOLLERR**
    表示关联的文件描述符出现错误。

  - **EPOLLHUP**
    表示关联的文件描述符被挂断。

  - **EPOLLONESHOT**
    表示只监听文件描述符的一次事件。当事件触发之后，epoll 就会禁用此文件描述符。
    如果想要继续监听该文件描述符的事件，需要再次调用 epoll_ctl，并执行 EPOLL_CTL_MOD 操作。

  结构体成员 `data` 的作用是将其数据交给内核保存，当文件描述符就绪后，内核会返回保存的数据 [2.4.1.3]。`epoll_data_t` 是一个共用体，其定义如下：

  ```c++
  typedef union epoll_data {
     void        *ptr;
     int          fd;
     uint32_t     u32;
     uint64_t     u64;
  } epoll_data_t;
  ```



###### 2.4.1.2.1.2 返回值

最后来了解 epoll_ctl 的返回值：

- 当操作成功时，返回值为 0。

- 当发生错误时，返回值为 -1。



##### 2.4.1.2.2 分析 RegisterHandler 函数

在了解过系统调用 epoll_ctl 以后，现在开始分析 RegisterHandler 函数。首先来看函数在头文件中的声明，头文件的路径为 `system/core/init/epoll.h`：

```c++
class Epoll {
  public:
    Result<Success> RegisterHandler(int fd, std::function<void()> handler,
                                    uint32_t events = EPOLLIN);
};
```

由函数原型可知，函数的第三个参数 `events` 有默认值 `EPOLLIN`，表示监听文件描述符的读操作是否可用。

接下来分析 RegisterHandler 函数：

```c++
Result<Success> Epoll::RegisterHandler(int fd, std::function<void()> handler, uint32_t events) {
    ...

    // handler 是事件触发时的回调函数
    auto [it, inserted] = epoll_handlers_.emplace(fd, std::move(handler));

    ...

    epoll_event ev;

    // 设置要监听的事件类型
    // 由于 events 有默认值 EPOLLIN, 因此默认监听的事件类型是读操作是否可用
    ev.events = events;
  
    // 设置回调函数, 回调函数会在事件触发后被调用 [2.4.1.3.2]
    ev.data.ptr = reinterpret_cast<void*>(&it->second);

    // 发起 epoll_ctl 系统调用
    // 注意到第二个参数传入的是 EPOLL_CTL_ADD, 因此对指定文件描述符执行的是添加操作
    if (epoll_ctl(epoll_fd_, EPOLL_CTL_ADD, fd, &ev) == -1) {
        Result<Success> result = ErrnoError() << "epoll_ctl failed to add fd";
        epoll_handlers_.erase(fd);
        return result;
    }
    return Success();
}
```

经过分析，可以知道函数的主要工作是：设置文件描述符需要监听的事件类型 `events` 以及事件触发时的回调函数 `handler`，最后发起 `epoll_ctl` 系统调用，将文件描述符添加到 epoll 监控列表。



#### 2.4.1.3 等待事件触发

经过 epoll 的初始化以及将文件描述符注册到 epoll 之后，接下来就需要等待事件触发，然后处理事件。

在 init 进程启动的第二阶段，最后一个步骤就是进入死循环，不断地等待事件触发。接下来看下这段逻辑：

```c++
int SecondStageMain(int argc, char** argv) {
    ...

    while (true) {
        ...

        // 关键点
        if (auto result = epoll.Wait(epoll_timeout); !result) {
            LOG(ERROR) << result.error();
        }
    }

    return 0;
}
```

这段逻辑的关键点在于 Wait 函数，接下来分析这个函数。



##### 2.4.1.3.1 系统调用 epoll_wait

在分析 Wait 函数之前，首先要了解系统调用 epoll_wait，这个系统调用是 Wait 函数的关键点。

**epoll_wait** 是一个系统调用，函数原型为 `int epoll_wait(int epfd, struct epoll_event *events, int maxevents, int timeout)`，用于等待 epoll 上的事件。

调用 epoll_wait 便会进入阻塞状态，直至满足以下任一条件，调用才会返回：

- 监听的文件描述符传递一个事件；
- 调用被信号处理程序中断；
- 等待超出超时时长。



###### 2.4.1.3.1.1 参数

首先来理解 epoll_wait 的参数：

- **epfd** 是 epoll 实例的文件描述符。

- **events** 是一个 epoll_event 类型的指针，而 epoll_event 是一个结构体，在 [2.4.1.2.1] 一节已经详细介绍过。

  当事件触发后，调用就会返回，此时可以从 events 中得到已触发事件的集合。集合中每一个事件结构体 epoll_event 的 data 字段和调用 epoll_ctl 为相应的文件描述符设置的 data 字段是相同的。意味着，当事件触发后，之前调用 epoll_ctl 给指定文件描述符设置的 event.data 会通过这个参数返回给 epoll_wait 的调用者。

- **maxevents** 表示在一次调用中返回事件的最大数量。因此，从参数 events 中得到的事件数量不会大于 maxevents。

- **timeout** 是此次调用的超时时长。当 timeout = -1 时，调用将会一直阻塞，直到有事件发生；当 timeout = 0 时，调用会立即返回，即使当前没有事件发生。



###### 2.4.1.3.1.2 返回值

最后来了解 epoll_wait 的返回值：

- 当调用成功时，返回值为已就绪的文件描述符的数量，此时可以对这些文件描述符进行 I/O 操作。

  需要注意的是，如果该调用是因为超出等待时长而返回的，则返回值为 0，表明没有已就绪的文件描述符。

- 当调用失败时，返回值为 -1，并返回错误信息。



##### 2.4.1.3.2 分析 Wait 函数

在了解过系统调用 epoll_wait 以后，就可以开始分析 Wait 函数，函数所在文件的路径为 `system/core/init/epoll.cpp`：

```c++
Result<Success> Epoll::Wait(std::optional<std::chrono::milliseconds> timeout) {
    // 超时时长默认值为 -1
    int timeout_ms = -1;

    if (timeout && timeout->count() < INT_MAX) {
        // 当参数 timeout 满足不等于 0 和数值没有溢出时, 更新超时时长
        timeout_ms = timeout->count();
    }

    epoll_event ev;

    // 发起 epoll_wait 系统调用
    // 注意到第三个参数传入的是 1, 表明每次调用最多返回 1 个事件
    auto nr = TEMP_FAILURE_RETRY(epoll_wait(epoll_fd_, &ev, 1, timeout_ms));

    if (nr == -1) {
        // 调用返回 -1, 表明此次调用失败
        return ErrnoError() << "epoll_wait failed";
    } else if (nr == 1) {
        // 调用返回 1, 表明此次调用成功, 有 1 个事件触发
        // ev.data.ptr 是之前调用 epoll_ctl 给指定文件描述符设置的回调函数, 因此事件最后交给回调函数进行处理
        std::invoke(*reinterpret_cast<std::function<void()>*>(ev.data.ptr));
    }
    return Success();
}
```

此函数设置了超时时长，然后发起 `epoll_wait` 系统调用，等待事件触发。当 `epoll_wait` 成功返回时，会从返回的数据里面取出之前通过 epoll_ctl 设置的回调函数，将事件交由回调函数处理。



### 2.4.2 属性服务

属性服务是 Android 提供的一个用于存取系统属性的服务，属性以 key-value 的形式存储在一块共享内存，所有进程都可以通过这个服务获取和设置属性。

使用 adb，输入命令 `getprop`，可以查看一台设备上的属性，下面列举其中的一些输出：

```
[ro.product.brand]: [Redmi]
[ro.product.manufacturer]: [Xiaomi]
[ro.product.marketname]: [Redmi K30S Ultra]
[ro.product.model]: [M2007J3SC]
[ro.product.name]: [apollo]

[ro.product.cpu.abi]: [arm64-v8a]
[ro.product.cpu.abilist]: [arm64-v8a,armeabi-v7a,armeabi]
[ro.product.cpu.abilist32]: [armeabi-v7a,armeabi]
[ro.product.cpu.abilist64]: [arm64-v8a]

[dalvik.vm.heapsize]: [512m]
[dalvik.vm.heapstartsize]: [8m]
```

通过列举出的数据，可以看出属性里面包含了很多重要的信息：机型信息，CPU 架构，虚拟机分配的内存大小等等。

在 Java 代码中，可以调用 `SystemProperties.get(String, String)` 方法获取系统属性。例如，在 ActivityManager 中就有这样的代码：

```java
static public int staticGetLargeMemoryClass() {
    // 通过获取系统属性 dalvik.vm.heapsize, 得出应用可用堆内存的最大值
    String vmHeapSize = SystemProperties.get("dalvik.vm.heapsize", "16m");

    return Integer.parseInt(vmHeapSize.substring(0, vmHeapSize.length() - 1));
}
```

类似地，调用 `SystemProperties.set(String, String)` 方法可以设置系统属性。例如，在 ActivityManagerService 中就有这样的代码：

```java
final void finishBooting() {
    ...

    synchronized (this) {

        ...

        // Tell anyone interested that we are done booting!
        // 通过设置属性 sys.boot_completed = 1, 标记系统已经启动完成
        SystemProperties.set("sys.boot_completed", "1");

        ...
    }

    ...
}
```



#### 2.4.2.1 初始化

<!-- TODO: 如何创建内存区域？ -->

<!-- TODO: 进程是怎样获取共享内存中的属性？ -->

<!-- TODO: 进程为什么不能直接设置共享内存中的属性？ -->

<!-- TODO: CreateSerializedPropertyInfo() -->

<!-- TODO: property_info_area.LoadDefaultPath() -->

属性服务初始化时，调用的是 property_init 函数，文件路径为 `system/core/init/property_service.cpp`。

```c++
void property_init() {
    mkdir("/dev/__properties__", S_IRWXU | S_IXGRP | S_IXOTH);
    CreateSerializedPropertyInfo();

    // 创建内存区域
    if (__system_property_area_init()) {
        LOG(FATAL) << "Failed to initialize property area";
    }

    if (!property_info_area.LoadDefaultPath()) {
        LOG(FATAL) << "Failed to load serialized property info file";
    }
}
```

函数最主要的工作是通过调用 `__system_property_area_init` 函数，创建用于存储属性的内存区域。



#### 2.4.2.2 启动

<!-- TODO: socket 的创建过程 -->

<!-- TODO: listen(property_set_fd, 8) -->

<!-- TODO: 一个进程是怎样向 init 进程发起属性设置的请求？ -->

属性服务启动时，调用的是 StartPropertyService 函数，文件路径为 `system/core/init/property_service.cpp`：

```c++
void StartPropertyService(Epoll* epoll) {

    ...

    // 创建 socket 并返回其文件描述符, 其中 PROP_SERVICE_NAME = "property_service"
    property_set_fd = CreateSocket(PROP_SERVICE_NAME, SOCK_STREAM | SOCK_CLOEXEC | SOCK_NONBLOCK,
                                   false, 0666, 0, 0, nullptr);
    if (property_set_fd == -1) {
        PLOG(FATAL) << "start_property_service socket creation failed";
    }

    listen(property_set_fd, 8);

    // 将 socket 文件描述符注册到 epoll, 监听其可读事件 [2.4.1.2]
    // handle_property_set_fd 是可读事件触发时的回调函数 [2.4.2.3]
    if (auto result = epoll->RegisterHandler(property_set_fd, handle_property_set_fd); !result) {
        PLOG(FATAL) << result.error();
    }
}
```

此函数首先创建名称为 property_service 的 socket，然后将 socket 文件描述符注册到 epoll，监听其可读事件。

当可读事件触发时，会进入回调函数 `handle_property_set_fd`，接着来分析这个回调函数。



#### 2.4.2.3 回调函数 handle_property_set_fd

<!-- TODO: ucred cr; socklen_t cr_size = sizeof(cr); -->

<!-- TODO: 设置属性的具体实现 -->

<!-- TODO: 除了通过向 init 进程发出请求设置属性以外, 在其他地方是否也有属性的设置？ -->

<!-- TODO: 属性的同步问题 -->

函数所在文件的路径为 `system/core/init/property_service.cpp`：

```c++
static void handle_property_set_fd() {
    // 2000ms
    static constexpr uint32_t kDefaultSocketTimeout = 2000; /* ms */

    ...

    SocketConnection socket(s, cr);

    // 设置 2000ms 的超时时长
    uint32_t timeout_ms = kDefaultSocketTimeout;

    uint32_t cmd = 0;
    if (!socket.RecvUint32(&cmd, &timeout_ms)) {
        PLOG(ERROR) << "sys_prop: error while reading command from the socket";
        socket.SendUint32(PROP_ERROR_READ_CMD);
        return;
    }

    switch (cmd) {
    // 处理 PROP_MSG_SETPROP 命令
    case PROP_MSG_SETPROP: {
        // PROP_NAME_MAX = 32
        char prop_name[PROP_NAME_MAX];
        char prop_value[PROP_VALUE_MAX];

        // 接收属性名和属性值, 分别存储于字符数组 prop_name 和 prop_value 中
        if (!socket.RecvChars(prop_name, PROP_NAME_MAX, &timeout_ms) ||
            !socket.RecvChars(prop_value, PROP_VALUE_MAX, &timeout_ms)) {
          PLOG(ERROR) << "sys_prop(PROP_MSG_SETPROP): error while reading name/value from the socket";
          return;
        }

        // 将字符数组中最后的一个元素设置为 0
        prop_name[PROP_NAME_MAX-1] = 0;
        prop_value[PROP_VALUE_MAX-1] = 0;

        const auto& cr = socket.cred();
        std::string error;

        // 设置属性
        uint32_t result =
            HandlePropertySet(prop_name, prop_value, socket.source_context(), cr, &error);

        if (result != PROP_SUCCESS) {
            LOG(ERROR) << "Unable to set property '" << prop_name << "' to '" << prop_value
                       << "' from uid:" << cr.uid << " gid:" << cr.gid << " pid:" << cr.pid << ": "
                       << error;
        }

        break;
      }

    // 处理 PROP_MSG_SETPROP2 命令
    case PROP_MSG_SETPROP2: {
        std::string name;
        std::string value;

        // 接收字符串类型的属性名和属性值
        if (!socket.RecvString(&name, &timeout_ms) ||
            !socket.RecvString(&value, &timeout_ms)) {
          PLOG(ERROR) << "sys_prop(PROP_MSG_SETPROP2): error while reading name/value from the socket";
          socket.SendUint32(PROP_ERROR_READ_DATA);
          return;
        }

        const auto& cr = socket.cred();
        std::string error;

        // 设置属性
        uint32_t result = HandlePropertySet(name, value, socket.source_context(), cr, &error);

        if (result != PROP_SUCCESS) {
            LOG(ERROR) << "Unable to set property '" << name << "' to '" << value
                       << "' from uid:" << cr.uid << " gid:" << cr.gid << " pid:" << cr.pid << ": "
                       << error;
        }
        socket.SendUint32(result);
        break;
      }

    default:
        LOG(ERROR) << "sys_prop: invalid command " << cmd;
        socket.SendUint32(PROP_ERROR_INVALID_CMD);
        break;
    }
}
```

函数初始化了 socket 接收事件的超时时长，然后对接收到的 `cmd` 做相应的操作：

- 对于 `PROP_MSG_SETPROP` 命令，使用两个长度为 `PROP_NAME_MAX` 的字符数组保存接收到的属性名和属性值。值得注意的是，最后会将字符数组中最后的一个元素设为 0，因此接收的属性名和属性值的最大长度不应超过 `PROP_NAME_MAX - 1` 。
- 对于 `PROP_MSG_SETPROP2` 命令，使用两个 `std::string` 保存接收到的属性名和属性值。

最后，不论是 `PROP_MSG_SETPROP` 命令还是 `PROP_MSG_SETPROP2` 命令，都会调用 `HandlePropertySet` 函数来设置属性。



### 2.4.3 信号

**信号 (Signals)** 属于 IPC 的一种方式，用于向一个进程或者进程中的特定线程发送事件通知。

信号是一个**异步的**通信机制，一个进程可以不需要等待信号的到达。在信号未产生时，进程会正常执行；当信号产生后，系统可以中断目标进程的任何非原子操作，此时进程的正常执行流程会被中断，进而对信号进行处理。



#### 2.4.3.1 处理信号

在 Linux，一个子进程终结后，内核依然会为其维护关于此进程的最小信息集 (PID、终结状态、资源使用信息)，以便父进程获取有关子进程的信息。如果父进程没有为该子进程发起 wait、waitpid 或者 waitid 系统调用，那么子进程的信息就会一直占据着内核进程表的空间。在这种情况下，这个子进程就成为了一个**僵尸进程 (Zombie process)**。

一般情况下，对于一个僵尸进程，如果其父进程终结了，那么 init 进程就会自动成为僵尸进程的父进程。此时，为了避免僵尸进程一直占据着系统资源，init 进程需要为僵尸进程释放占据的空间。

而且，在加载启动脚本 [2.4.4] 这一步骤中，init 进程会 fork 得到一些子进程，例如：servicemanager 进程、zygote 进程。这些子进程对 Android 而言非常重要，在这些进程退出后，init 进程需要有相应的动作将其重启。

于是，为了管理这些子进程，init 进程需要接收子进程终结的信号并进行相应的处理。

在 Android，信号处理基于 Linux 的信号机制。



##### 2.4.3.1.1 信号的处理方式

<!-- 更正: 注册信号处理函数 -->

信号的处理方式有以下三种：

- **忽略该信号**
- **按信号的默认行为处理该信号**
- **使用自定义的信号处理函数来处理该信号**

init 进程使用的是第三种处理方式：使用自定义的信号处理函数来处理该信号。进程注册了一个信号处理函数，当目标信号产生时，信号会被捕获，并调用注册的函数进行处理。

接下来分析这个过程。



##### 2.4.3.1.2 系统调用 sigaction

在分析 init 进程注册函数的过程之前，首先来了解系统调用 sigaction，这个系统调用是此过程的一个关键点。

**sigaction** 是一个系统调用，函数原型为 `int sigaction(int signum, const struct sigaction *restrict act, struct sigaction *restrict oldact)`，用于更改进程在收到指定信号后的行为。



###### 2.4.3.1.2.1 参数

首先来理解 sigaction 的参数：

- **signum** 是指定信号的编号。

  同一个信号在不同的架构上可能有不同的编号。在 Android，ARM 架构的信号编号定义在 `bionic/libc/kernel/uapi/asm-arm/asm/signal.h` 文件上。

- **act** 是一个 sigaction 类型的指针，而 sigaction 是一个结构体，其定义如下：

  ```c++
  struct sigaction {
     void     (*sa_handler)(int);
     void     (*sa_sigaction)(int, siginfo_t *, void *);
     sigset_t   sa_mask;
     int        sa_flags;
     void     (*sa_restorer)(void);
  };
  ```

  关注结构体中两个较为重要的成员 `sa_handler` 和 `sa_flags`。

  成员 `sa_handler` 用于指定信号产生时的行为，可以是以下这些值之一：

  - **SIG_DFL**

    表示执行该信号的默认行为。

  - **SIG_IGN**

    表示忽略该信号。

  - **一个指向信号处理函数的指针**

    信号编号 signum 将作为函数的唯一参数传入。当目标信号产生时，会自动调用此函数进行处理。

  成员 `sa_flags` 是标志位，可以由零个或者多个标志组成。下面列出其中的一些标志：

  - **SA_NOCLDSTOP**

    只有当 signum = SIGCHLD 时才有意义。表示进程不接收子进程停止或者恢复时产生的信号，只接收子进程终结的信号。

  - **SA_RESETHAND**

    当信号已经由信号处理函数处理过后，将信号恢复为使用默认行为进行处理。

  - **SA_SIGINFO**

    不再通过结构体成员 sa_handler 设置信号处理函数，而是使用成员 sa_sigaction。此时，函数的入参有三个。

- **oldact** 同样是一个 sigaction 类型的指针，用于保存上一个 sigaction 调用所传入的参数 act，可以为空。



###### 2.4.3.1.2.2 返回值

最后来了解 sigaction 的返回值：

- 当操作成功时，返回值为 0。

- 当发生错误时，返回值为 -1。



##### 2.4.3.1.3 init 进程注册信号处理函数

在了解过系统调用 sigaction 以后，现在可以开始分析 init 进程注册信号处理函数的过程。



###### 2.4.3.1.3.1 发起系统调用 sigaction

init 进程通过调用 InstallSignalFdHandler 函数实现注册信号处理函数，函数所在文件的路径为 `system/core/init/init.cpp`，首先来关注函数前半部分所做的工作：

```c++
static void InstallSignalFdHandler(Epoll* epoll) {
    // 构建结构体 sigaction
    // 成员 sa_handler 用于指定信号产生时的行为, 令 sa_handler = SIG_DFL 表明当信号产生时按默认行为对信号进行处理
    // 成员 sa_flags 是标志位, 令 sa_flags = SA_NOCLDSTOP 表明 init 进程不接收子进程被中断或者被中断后恢复时产生的信号, 只接收子进程终结的信号
    const struct sigaction act { .sa_handler = SIG_DFL, .sa_flags = SA_NOCLDSTOP };

    // sigaction 是一个系统调用, 用于更改进程在收到指定的信号时所执行的操作 [2.4.3.1.2]
    // 注意到第一个参数传入的是 SIGCHLD, 表明目标信号是 SIGCHLD, 当子进程终结, 被中断, 或者被中断后恢复时, 就会产生此信号
    sigaction(SIGCHLD, &act, nullptr);

    ...

}
```

函数前半部分所做的工作是：发起系统调用 sigaction，针对子进程产生的 `SIGCHLD` 信号，当进程在收到信号时，按默认行为对信号进行处理，而 `SIGCHLD` 信号的默认行为就是忽略该信号。

值得注意的是，当子进程终结、被中断或者被中断后恢复时，都会产生 `SIGCHLD` 信号，而添加标志位 `SA_NOCLDSTOP` 可以使得 init 进程只接收子进程终结的信号。

显然，在函数前半部分所做的工作中，并没有实现按自定义行为对信号进行处理。事实上，init 进程是通过监听 signal 文件描述符来实现按自定义行为来处理信号的。接下来分析 InstallSignalFdHandler 函数后半部分所做的工作。



###### 2.4.3.1.3.2 监听 signal 文件描述符

之前提到，信号是一个异步的通信机制。进程可以注册一个信号处理函数，接着就能继续进行其他的工作，只有当目标信号发送到进程时，才回调处理函数进行处理，这里就体现出异步的性质。

除了异步的方式以外，进程还可以通过同步的方式接收信号。Linux 提供了系统调用 signalfd，用于创建一个接收指定信号的文件描述符。对文件描述符执行读操作会进入阻塞状态，直到目标信号产生并传递给进程。

init 进程就是通过这种方式来同步接收信号的，接下来分析这个过程：

```c++
static void InstallSignalFdHandler(Epoll* epoll) {

    ...

    // mask 是一个信号集, 用于指定想要接收的信号
    // 在这里, 目标信号就是 SIGCHLD
    sigset_t mask;
    sigemptyset(&mask);
    sigaddset(&mask, SIGCHLD);

    ...

    // 获取 signal 文件描述符
    // 注意到, 在发起系统调用 signalfd 时, 传入的第一个参数是 -1, 则调用会创建一个新的文件描述符
    signal_fd = signalfd(-1, &mask, SFD_CLOEXEC);

    if (signal_fd == -1) {
        PLOG(FATAL) << "failed to create signalfd";
    }

    // 将 signal 文件描述符注册到 epoll, 监听其可读事件 [2.4.1.2]
    // HandleSignalFd 是可读事件触发时的回调函数 [2.4.3.1.4]
    if (auto result = epoll->RegisterHandler(signal_fd, HandleSignalFd); !result) {
        LOG(FATAL) << result.error();
    }
}
```

函数后半部分所做的工作是：创建信号集，信号集里面包含了信号 `SIGCHLD`，然后新建一个与信号集关联的 signal 文件描述符，最后将文件描述符注册到 epoll。

当 signal 文件描述符的可读事件触发时，就会调用 HandleSignalFd 函数，接下来分析这个函数。



##### 2.4.3.1.4 回调函数 HandleSignalFd

函数所在文件的路径为 `system/core/init/init.cpp`：

```c++
static void HandleSignalFd() {
    signalfd_siginfo siginfo;

    // 从 signal 文件描述符中读取结构体 signalfd_siginfo, 每次读取一个
    ssize_t bytes_read = TEMP_FAILURE_RETRY(read(signal_fd, &siginfo, sizeof(siginfo)));

    if (bytes_read != sizeof(siginfo)) {
        PLOG(ERROR) << "Failed to read siginfo from signal_fd";
        return;
    }

    // 根据信号编号执行相应的操作
    switch (siginfo.ssi_signo) {
        case SIGCHLD:
            ReapAnyOutstandingChildren();
            break;
        case SIGTERM:
            HandleSigtermSignal(siginfo);
            break;
        default:
            PLOG(ERROR) << "signal_fd: received unexpected signal " << siginfo.ssi_signo;
            break;
    }
}
```

当目标信号产生并传递给进程时，signal 文件描述符的可读事件就会触发，返回一个或者多个结构体 signalfd_siginfo。而函数 HandleSignalFd 负责从 signal 文件描述符中读出一个结构体 signalfd_siginfo，并根据信号编号执行相应的操作。

在这里，我们关心的信号是 `SIGCHLD`。当此信号产生后，会调用 ReapAnyOutstandingChildren 函数，接着来分析这个函数。



###### 2.4.3.1.4.1 分析 ReapAnyOutstandingChildren 函数

函数所在文件的路径为 `system/core/init/sigchld_handler.cpp`：

```c++
void ReapAnyOutstandingChildren() {
    while (ReapOneProcess()) {
    }
}
```

此函数循环调用 ReapOneProcess 函数，只有当函数 ReapOneProcess 返回 false 时，循环才会停止。接着来看 ReapOneProcess 函数做了什么工作。



###### 2.4.3.1.4.2 分析 ReapOneProcess 函数

<!-- TODO: 分析 service->flags() -->

<!-- TODO: 分析 ServiceList::GetInstance().RemoveService(*service) -->

函数所在文件的路径为 `system/core/init/sigchld_handler.cpp`：

```c++
static bool ReapOneProcess() {
    siginfo_t siginfo = {};

    // 发起系统调用 waitid, 此系统调用用于获取子进程状态的变更和相关的信息
    // 由于 init 进程只接收子进程终结的信号, 因此这里获取到的都是已进入僵尸状态的子进程的相关信息, 信息保存在 siginfo 当中
    if (TEMP_FAILURE_RETRY(waitid(P_ALL, 0, &siginfo, WEXITED | WNOHANG | WNOWAIT)) != 0) {
        // 失败, 函数返回 false
        PLOG(ERROR) << "waitid failed";
        return false;
    }

    // 获取子进程的 pid
    auto pid = siginfo.si_pid;

    // 子进程的 pid 不存在, 函数返回 false
    if (pid == 0) return false;

    ...

    std::string name;
    std::string wait_string;
    Service* service = nullptr;

    if (PropertyChildReap(pid)) {
        name = "Async property child";
    } else if (SubcontextChildReap(pid)) {
        name = "Subcontext";
    } else {
        // 根据 pid 查询相应的 service
        service = ServiceList::GetInstance().FindService(pid, &Service::pid);

        if (service) {
            name = StringPrintf("Service '%s' (pid %d)", service->name().c_str(), pid);

            ...

        } else {
            name = StringPrintf("Untracked pid %d", pid);
        }
    }

    ...

    // 没有找到对应的 service, 函数返回 true
    if (!service) return true;

    // 调用 service 的 Reap 函数
    service->Reap(siginfo);

    ...

    return true;
}
```

经分析可知，ReapOneProcess 函数的主要工作是：为每个进入终结状态的子进程发起系统调用 waitid，从而避免已进入僵尸状态的子进程一直占据着系统资源。最后，函数会根据子进程 pid 查找是否存在相应的 service，如果 service 存在，还会调用 service 的 Reap 函数。接着来分析 Reap 函数。



###### 2.4.3.1.4.3 分析 Reap 函数

函数所在文件的路径为 `system/core/init/service.cpp`：

```c++
void Service::Reap(const siginfo_t& siginfo) {
    if (!(flags_ & SVC_ONESHOT) || (flags_ & SVC_RESTART)) {
        // 如果服务的标志位同时不带有 SVC_ONESHOT 和 SVC_RESTART, 则直接杀死服务所在的进程组
        KillProcessGroup(SIGKILL);
    }

    ...

    // 如果服务的标志位带有 SVC_TEMPORARY, 则直接返回
    if (flags_ & SVC_TEMPORARY) return;

    // 重置状态
    pid_ = 0;
    flags_ &= (~SVC_RUNNING);
    start_order_ = 0;

    if ((flags_ & SVC_ONESHOT) && !(flags_ & SVC_RESTART) && !(flags_ & SVC_RESET)) {
        // 将带有 SVC_ONESHOT 的服务设为不可用状态
        flags_ |= SVC_DISABLED;
    }

    if (flags_ & (SVC_DISABLED | SVC_RESET))  {
        // 如果服务的标志位带有 SVC_DISABLED 或者 SVC_RESET, 则使其进入 stopped 状态, 不会自动重启
        NotifyStateChange("stopped");
        return;
    }

    // 带有标志位 SVC_CRITICAL 的服务会被标记为系统关键服务
    // 如果服务在 4 分钟之内崩溃 4 次以上, 又或者服务崩溃时系统处于未完全启动状态
    // 则设备会重启并进入 bootloader 或者设置崩溃相关的属性
    boot_clock::time_point now = boot_clock::now();
    if (((flags_ & SVC_CRITICAL) || !pre_apexd_) && !(flags_ & SVC_RESTART)) {
        bool boot_completed = android::base::GetBoolProperty("sys.boot_completed", false);
        if (now < time_crashed_ + 4min || !boot_completed) {
            if (++crash_count_ > 4) {
                if (flags_ & SVC_CRITICAL) {
                    // Aborts into bootloader
                    LOG(FATAL) << "critical process '" << name_ << "' exited 4 times "
                               << (boot_completed ? "in 4 minutes" : "before boot completed");
                } else {
                    LOG(ERROR) << "updatable process '" << name_ << "' exited 4 times "
                               << (boot_completed ? "in 4 minutes" : "before boot completed");
                    // Notifies update_verifier and apexd
                    property_set("ro.init.updatable_crashing", "1");
                }
            }
        } else {
            time_crashed_ = now;
            crash_count_ = 1;
        }
    }

    // 添加标志位 SVC_RESTARTING, init 进程会重启带有此标志位的服务 [2.4.5.2]
    flags_ &= (~SVC_RESTART);
    flags_ |= SVC_RESTARTING;

    // 执行当前 service 中所有 onrestart 命令
    onrestart_.ExecuteAllCommands();

    // 使服务进入 restarting 状态
    NotifyStateChange("restarting");
    return;
}
```

Reap 函数最主要的工作就是重启有需要的服务。注意到，改变服务状态是通过调用 NotifyStateChange 函数实现的，来看看这个函数。



###### 2.4.3.1.4.4 分析 NotifyStateChange 函数

函数所在文件的路径为 `system/core/init/service.cpp`：

```c++
void Service::NotifyStateChange(const std::string& new_state) const {
    if ((flags_ & SVC_TEMPORARY) != 0) {
        // 如果服务的标志位中带有 SVC_TEMPORARY, 则不会使用属性服务来记录它的状态
        return;
    }

    // 使用属性服务来记录服务当前的状态
    std::string prop_name = "init.svc." + name_;
    property_set(prop_name, new_state);

    ...
}
```

通过分析这个函数，可以知道，系统会使用属性服务以 key-value 的形式存储服务当前的状态，其中 key 的取值为 init.svc.[service_name]，value 是服务当前的状态。因此，可以使用 adb，输入命令 `getprop | grep init.svc.`，查看设备上 service 的运行状态，以下是其中的一些输出：

```
[init.svc.adbd]: [running]
[init.svc.alarm-hal-1-0]: [running]
[init.svc.android.thermal-hal]: [running]
[init.svc.apexd]: [stopped]
[init.svc.apexd-bootstrap]: [stopped]
[init.svc.apexd-snapshotde]: [stopped]
[init.svc.audioserver]: [running]
[init.svc.wifidisplayhalservice]: [running]
[init.svc.wpa_supplicant]: [running]
[init.svc.zygote]: [running]
[init.svc.zygote_secondary]: [running]
```



### 2.4.4 加载启动脚本

<!-- TODO: 分析函数 CreateParser -->

<!-- TODO: .rc 文件的解析过程 -->

<!-- TODO: 属性的变更如何触发脚本中的属性触发器？ -->

<!-- TODO: /system/etc/init -->

<!-- TODO: /odm/etc/init -->

<!-- TODO: /vendor/etc/init -->

调用 LoadBootScripts 函数加载启动脚本，函数所在文件的路径为 `system/core/init/init.cpp`。

```c++
static void LoadBootScripts(ActionManager& action_manager, ServiceList& service_list) {
    Parser parser = CreateParser(action_manager, service_list);

    std::string bootscript = GetProperty("ro.boot.init_rc", "");
    if (bootscript.empty()) {
        // 加载 init.rc 脚本文件 [2.4.4.1]
        parser.ParseConfig("/init.rc");

        // 加载 /system/etc/init 目录下的所有脚本文件
        if (!parser.ParseConfig("/system/etc/init")) {
            late_import_paths.emplace_back("/system/etc/init");
        }

        // 加载 /product/etc/init 目录下的所有脚本文件
        if (!parser.ParseConfig("/product/etc/init")) {
            late_import_paths.emplace_back("/product/etc/init");
        }

        // 加载 /product_services/etc/init 目录下的所有脚本文件
        if (!parser.ParseConfig("/product_services/etc/init")) {
            late_import_paths.emplace_back("/product_services/etc/init");
        }

        // 加载 /odm/etc/init 目录下的所有脚本文件
        if (!parser.ParseConfig("/odm/etc/init")) {
            late_import_paths.emplace_back("/odm/etc/init");
        }

        // 加载 /vendor/etc/init 目录下的所有脚本文件
        if (!parser.ParseConfig("/vendor/etc/init")) {
            late_import_paths.emplace_back("/vendor/etc/init");
        }
    } else {
        parser.ParseConfig(bootscript);
    }
}
```

一般情况下，此函数会去加载一些指定的脚本，其中：

- `init.rc` 是主要的 .rc 文件。
- `/system/etc/init/` 包含了核心的系统项目，例如：SurfaceFlinger, MediaService, logcatd。
- `/vendor/etc/init/` 包含了 SoC 供应商项目，例如：核心 SoC 功能所需的操作或守护进程。
- `/odm/etc/init/` 包含了设备制造商项目，例如：运动传感器或其他外围设备所需的操作或守护进程。

init.rc 是最主要脚本文件，接下来将对这个脚本进行分析。



#### 2.4.4.1 init.rc 脚本

在 Android，后缀为 .rc 的文件由 Android Init Language 编写，[关于 Android Init Language 的详细说明](https://cs.android.com/android/platform/superproject/+/android-security-10.0.0_r56:system/core/init/README.md) 可以在 AOSP 上找到，路径为 `system/core/init/README.md`。

在分析脚本之前，首先来了解这种语言的语法。



##### 2.4.4.1.1 Android Init Language

Android Init Language 由五大类表达式组成：`Actions`，`Commands`，`Services`，`Options`，`Imports`。

其语法规则有：

- 每一行都是一条语句，一条语句由多个 token 组成，token 之间以空格分开。
- 如果一个 token 包含了空格，可以跟 c 语言类似，使用反斜杠 `\` 作为转义字符，又或者使用双引号包裹整个 token。
- 在行的末尾使用反斜杠 `\`，可以将语句换行。
- 以符号 `#` 开头的行是注释行。
- 系统属性的值可以通过语法 `${property.name}` 获取，例如：`import /init.recovery.${ro.hardware}.rc`。
- 一个文件可以分为多个 section，必须使用 `Actions` 或者 `Services` 来声明一个新的 section。所有的 `Commands` 和 `Options` 都是属于最近声明的那一个 section。如果在一个 section 之前声明了 `Commands` 或者 `Options` ，则声明会被忽略。
- `Services` 的名称必须是唯一的，如果存在多个重名的 `Services` ，除了第一个以外，其他的都会被忽略，并且会输出错误日志。

接下来列出一些较为重要的表达式。



###### 2.4.4.1.1.1 Actions

`Actions` 由 一系列 `Commands` 组成，同时 `Triggers` 决定了 `Actions` 的触发时机，其形式为：

```
on <trigger> [&& <trigger>]*
   <command>
   <command>
   <command>
```

当一个事件触发后，如果此事件能够匹配上 `Actions` 的  `Triggers`，那么 `Actions` 会被添加到待执行队列的尾部。

之后，待执行队列中的 `Actions` 会按照加入顺序出队，并且执行该 `Actions` 中的 `Commands`。

以启动 Zygote 作为示例：

```
on zygote-start && property:ro.crypto.state=unencrypted
    exec_start update_verifier_nonencrypted
    start netd
    start zygote
    start zygote_secondary
```

简单分析这个脚本：

这个 `Actions` 拥有两个 `Triggers`，分别是 `zygote-start` 和 `property:ro.crypto.state=unencrypted`，从第二行开始，每一行都是一个 `Command`。

当事件 `zygote-start` 和 `property:ro.crypto.state=unencrypted` 触发后，`Actions` 会被加入到待执行队列。在执行到该 `Actions` 时，会按照 `Commands` 定义的先后顺序，依次执行 `Actions` 中的 `Commands`。



###### 2.4.4.1.1.2 Triggers

`Triggers` 是字符串，用于匹配某些类型的事件并触发 `Actions` 中的 `Commands`。

Triggers 可以分为两类：

- **Event triggers**

  事件触发器，这类触发器所匹配的事件由命令 `trigger`  触发，又或者通过调用 `QueueEventTrigger()` 函数触发。

- **Property triggers**

  属性触发器，这类触发器所匹配的事件是：属性是否满足某个值，形式为 `property:<key>=<value>`。这里的属性就是之前提到的属性服务管理的属性。

注意，每一个 `Actions` 可以有多个属性触发器，但只能有一个事件触发器。

例如：

`on init && property:a=b`，`on property:a=b && property:c=d` 是合法的。

`on boot && on init` 是不合法的。



###### 2.4.4.1.1.3 Commands

下面列举一些常见的 `Commands`：

- **trigger <event>**

  触发一个事件。

- **write <path> <content>**

  按 path 打开文件，往文件中写入内容。

- **chown <owner> <group> <path>**

  更改文件所有者和组。

- **mkdir <path> [mode] [owner] [group]**

  在路径上创建一个目录，可以选择使用给定的权限，所有者，用户组。 

  如果未指定权限，所有者，用户组，则新建的目录默认具有权限 755，并由 root 用户和 root 组拥有。

  当指定权限，所有者，用户组时，如果目录已经存在，则权限，所有者，用户组将被更新。

- **start <service>**

  当指定的服务未在运行时，启动该服务。

- **exec_start <service>**

  启动指定的服务，在该命令返回之前暂停处理其他命令。

- **setprop <name> <value>**

  给系统属性赋值，这里的属性是之前提到的属性服务中的属性。

- **symlink <target> <path>**

  在 path 上创建一个连接到 target 的符号链接。



###### 2.4.4.1.1.4 Services

`Services` 是由 init 进程启动的程序，一般运行在 init 进程的一个子进程，因此 init 进程在启动一个 `Services` 时会通过 fork 的方式生成子进程。

默认情况下，`Services` 退出后会重启。

`Services` 的形式如下：

```
service <name> <pathname> [ <argument> ]*
   <option>
   <option>
   ...
```

以启动 Zygote 64 位进程的脚本作为示例：

```
service zygote /system/bin/app_process64 -Xzygote /system/bin --zygote --start-system-server
    class main
    priority -20
    user root
    group root readproc reserved_disk
    socket zygote stream 660 root system
    socket usap_pool_primary stream 660 root system
    onrestart write /sys/android_power/request_state wake
    onrestart write /sys/power/state on
    onrestart restart audioserver
    onrestart restart cameraserver
    onrestart restart media
    onrestart restart netd
    onrestart restart wificond
    writepid /dev/cpuset/foreground/tasks
```

简单分析这个脚本：`zygote` 是 `Services` 的名称，`/system/bin/app_process64` 是可执行文件的路径，`-Xzygote /system/bin --zygote --start-system-server` 是启动参数，从第二行开始，每一行都是一个 `Options`。



###### 2.4.4.1.1.5 Options

`Options` 是 `Services` 的配置项，用于控制 init 进程运行 `Services` 的方式和时间。

 下面列举一些常见的 `Options`：

- **class <name> [ <name>\* ]**

  指定 `Services` 的类名。当 `Services` 所属类开启 (退出) 时，`Services` 也会开启 (退出) 。默认值为 default。

- **class_start <serviceclass>**

  启动所有未在运行的，类名被指定为 `serviceclass` 的 `Services`。

- **priority <priority>**

  设置 `Services` 进程的优先级。取值范围是 -20 ~ 19。默认值为 0。

- **user <username>**

  设置执行 `Services` 的用户。一般情况下，默认值为 root。

- **group <groupname> [ <groupname>\* ]**

  设置执行 `Services` 的用户组。一般情况下，默认值为 root。

- **socket <name> <type> <perm> [ <user> [ <group> [ <seclabel> ] ] ]**

  创建一个 unix 域的 socket，命名为 `/dev/socket/<name>`，并将 socket 的文件描述符传递给创建的进程。

- **onrestart**

  当 `Services` 重启时执行一个 `Commands`。

- **oneshot**

  当 `Services` 退出后不再重启。

- **writepid <file> [ <file>\* ]**

  在进程 fork 之后，将子进程的 pid 写入指定的文件。



##### 2.4.4.1.2 加载 init.rc 脚本

脚本文件 init.rc 负责系统的初始设置，文件的路径为 `system/core/rootdir/init.rc`。

在了解到脚本的编写语法之后，可以知道脚本的内容都是由  `Actions` 和 `Services` 构成的，而在 init.rc 脚本中绝大部分都是 `Actions`。在 [2.4.4.1.1.2] 中曾经提到，调用 `QueueEventTrigger()` 函数可以触发一个事件，当事件匹配上  `Actions` 的  `Triggers`，就会开始执行该 `Actions`。 接下来回到进程启动的第二阶段，寻找事件的触发点。



###### 2.4.4.1.2.1 源码中事件的触发点

回到 init 进程启动的第二阶段，对应源代码 `system/core/init/init.cpp` 的 SecondStageMain 函数：

```c++
int SecondStageMain(int argc, char** argv) {

    ...

    ActionManager& am = ActionManager::GetInstance();

    ...

    // 将触发事件 early-init 的操作加入到 ActionManager
    am.QueueEventTrigger("early-init");

    ...

    // Trigger all the boot actions to get us started.
    // 将触发事件 init 的操作加入到 ActionManager
    am.QueueEventTrigger("init");

    ...

    // Don't mount filesystems or start core system services in charger mode.
    std::string bootmode = GetProperty("ro.bootmode", "");
    if (bootmode == "charger") {
        // 如果当前处于充电模式, 则将触发事件 charger 的操作加入到 ActionManager
        // 在充电模式下, 不会挂载文件系统和启动核心系统服务
        am.QueueEventTrigger("charger");
    } else {
        // 如果当前处于非充电模式, 则将触发事件 late-init 的操作加入到 ActionManager
        am.QueueEventTrigger("late-init");
    }

    ...

    return 0;
}
```

ActionManager 会按操作加入的顺序逐一执行 [2.4.5.x]，由此可知事件的触发顺序：

- 非充电模式：early-init -> init -> late-init
- 充电模式：early-init -> init -> charger

由于在充电模式下，系统可能未完全启动，接下来将选取非充电模式下的流程进行分析。



###### 2.4.4.1.2.2 init.rc 脚本的执行过程

<!-- TODO: trigger boot -->

现在结合事件的触发顺序，分析脚本执行过程中的一些关键点：

```
on early-init
    ...

    # 以 start 方式启动 ueventd
    start ueventd

    # 以 exec_start 方式运行 apexd-bootstrap, 在该命令返回之前暂停处理其他命令, 从而保证后续流程中 APEX 的可用性
    exec_start apexd-bootstrap

on init
    ...

    # 以 start 方式启动基本服务
    # 启动 servicemanager [2.4.4.1.2.3]
    start servicemanager
    start hwservicemanager
    start vndservicemanager

# 挂载文件和启动核心系统服务
# 其中 trigger 用于触发一个事件 [2.4.4.1.1.2]
on late-init
    trigger early-fs
    trigger fs
    trigger post-fs
    trigger late-fs
    trigger post-fs-data
    trigger load_persist_props_action

    # 触发事件 zygote-start, 启动 zygote [2.4.4.1.2.4]
    trigger zygote-start

    trigger firmware_mounts_complete
    trigger early-boot
    trigger boot
```

经过分析，现在已经大致了解脚本的执行过程，接下来分析 servicemanager 和 zygote 的启动。



###### 2.4.4.1.2.3 启动 servicemanager

servicemanager 启动脚本的路径为 `frameworks/native/cmds/servicemanager/servicemanager.rc`，分析这个脚本：

```
# servicemanager 是 Services 的名称
# /system/bin/servicemanager 是可执行文件的路径
service servicemanager /system/bin/servicemanager
    # 指定类名为 core 和 animation
    # 当 core 或者 animation 开启 (关闭) 时, servicemanager 也将开启 (关闭)
    class core animation

    # 设置用户为 system
    user system

    # 设置用户组为 system 和 readproc
    group system readproc

    # 将其标记为设备的关键服务
    # 如果此服务在规定时间内不断重启, 则设备会重启并进入 bootloader
    critical

    # 设置服务重启后执行的操作
    # 每一个操作都是一个 Commands
    onrestart restart healthd
    onrestart restart zygote
    onrestart restart audioserver
    onrestart restart media
    onrestart restart surfaceflinger
    onrestart restart inputflinger
    onrestart restart drm
    onrestart restart cameraserver
    onrestart restart keystore
    onrestart restart gatekeeperd
    onrestart restart thermalservice

    # 在进程执行 fork 操作之后，将子进程的 pid 写入文件 /dev/cpuset/system-background/tasks
    writepid /dev/cpuset/system-background/tasks

    # 设置服务的关闭行为
    # shutdown critical 表示此服务在关闭期间不会被杀死, 当关闭超时后才会被杀死
    shutdown critical
```

启动 servicemanager 之后，便会进入 `frameworks/native/cmds/servicemanager/service_manager.c` 的 main 函数。



###### 2.4.4.1.2.4 启动 zygote

在 init.rc 脚本中，事件 `zygote-start` 有 3 个对应的 `Actions`：

```
on zygote-start && property:ro.crypto.state=unencrypted
    exec_start update_verifier_nonencrypted
    start netd
    start zygote
    start zygote_secondary

on zygote-start && property:ro.crypto.state=unsupported
    exec_start update_verifier_nonencrypted
    start netd
    start zygote
    start zygote_secondary

on zygote-start && property:ro.crypto.state=encrypted && property:ro.crypto.type=file
    exec_start update_verifier_nonencrypted
    start netd
    start zygote
    start zygote_secondary
```

其中，zygote 通过以下语句导入：

```
import /init.${ro.zygote}.rc
```

此语句会根据属性 `ro.zygote`，导入相应的文件，其中包括：

```
system/core/rootdir/init.zygote32.rc
system/core/rootdir/init.zygote32_64.rc
system/core/rootdir/init.zygote64.rc
system/core/rootdir/init.zygote64_32.rc
```

zygote 的启动除了依赖事件 `zygote-start` 以外，还需要某些属性满足特定的值。当上面列出的三个 `Actions` 中的其中一个满足条件后，便会启动 zygote。

这里分析启动 Zygote 64 位进程的脚本，脚本文件的路径为 `system/core/rootdir/init.zygote64.rc`：

```
# zygote 是 Services 的名称
# /system/bin/app_process64 是可执行文件的路径
# -Xzygote /system/bin --zygote --start-system-server 是启动参数
service zygote /system/bin/app_process64 -Xzygote /system/bin --zygote --start-system-server
    # 指定类名为 main
    # 当 main 开启 (关闭) 时, zygote 也将开启 (关闭)
    class main

    # 进程优先级为 -20
    # 由于优先级的取值范围是 -20 ~ 19, 数值越小, 优先级越高, 因此 zygote 进程拥有最高的优先级
    priority -20

    # 用户和用户组都是 root
    user root
    group root readproc reserved_disk

    # 创建 socket
    socket zygote stream 660 root system
    socket usap_pool_primary stream 660 root system

    # 设置服务重启后执行的操作
    # 每一个操作都是一个 Commands
    onrestart write /sys/android_power/request_state wake
    onrestart write /sys/power/state on
    onrestart restart audioserver
    onrestart restart cameraserver
    onrestart restart media
    onrestart restart netd
    onrestart restart wificond

    # 在 zygote 进程执行 fork 操作之后，将子进程的 pid 写入文件 /dev/cpuset/foreground/tasks
    writepid /dev/cpuset/foreground/tasks
```

启动 zygote 之后，便会进入 `frameworks/base/cmds/app_process/app_main.cpp` 的 main 函数。



### 2.4.5 进入无限循环状态

init 进程启动完成之后，进程就会一直存活，而保持存活的方法就是进入无限循环状态。接下来分析这个过程，源代码的路径为 `system/core/init/init.cpp`：

```c++
int SecondStageMain(int argc, char** argv) {

    ...

    while (true) {
        // 设置等待 epoll 事件的超时时长
        // 默认情况下会一直处于阻塞状态, 直到有事件发生, 此时 epoll_timeout 取值为 -1
        auto epoll_timeout = std::optional<std::chrono::milliseconds>{};

        ...

        if (!(waiting_for_prop || Service::is_exec_service_running())) {
            // am 是一个引用, 指向 ActionManager 实例
            // 按顺序执行 ActionManager 中的一个命令 [2.4.5.1]
            am.ExecuteOneCommand();
        }
        if (!(waiting_for_prop || Service::is_exec_service_running())) {
            if (!shutting_down) {
                // 获取下一次 HandleProcessActions 的时间点 [2.4.5.2]
                auto next_process_action_time = HandleProcessActions();

                if (next_process_action_time) {
                    // 计算出 next_process_action_time 与当前时间的差值
                    epoll_timeout = std::chrono::ceil<std::chrono::milliseconds>(
                            *next_process_action_time - boot_clock::now());

                    // 如果差值小于 0, 说明需要立即进行下一次的循环
                    // 设置等待 epoll 事件的超时时长为 0, 会使得接下来的 Wait 调用会立即返回, 不阻塞进程
                    if (*epoll_timeout < 0ms) epoll_timeout = 0ms;
                }
            }

            // 如果 ActionManager 中仍有待执行的命令, 那么 init 进程不会进入阻塞状态, 并立即再次唤起
            // 设置等待 epoll 事件的超时时长为 0, 会使得接下来的 Wait 调用会立即返回, 不阻塞进程
            if (am.HasMoreCommands()) epoll_timeout = 0ms;
        }

        // 调用 Wait 函数, 等待事件触发 [2.4.1.3]
        if (auto result = epoll.Wait(epoll_timeout); !result) {
            LOG(ERROR) << result.error();
        }
    }

    return 0;
}
```

经分析可知，init 进程进入循环状态后主要的工作有 3 个：

- 执行 ActionManager 中的命令。
- 重启服务。
- 等待 epoll 事件，当事件触发后，调用相应的函数进行处理。结合前面的分析，可知等待的事件有：一个进程通过请求 init 进程向属性服务写入属性，以及 init 进程接收到 `SIGCHLD` 信号。



#### 2.4.5.1 ActionManager

<!-- TODO: 什么是 Action? -->

<!-- TODO: Action 和脚本中的 Actions 有没有关系 -->

<!-- TODO: 添加和执行过程 -->

ActionManager 是 Action 的管理器，它提供了对 Action 的添加、执行、清空等操作。



##### 2.4.5.1.1 添加 Action

<!-- TODO: 详细分析 Action 的作用 -->

在 init 进程启动的第二阶段中添加了一些 Action，简单看下其中部分 Action 的作用：

```c++
int SecondStageMain(int argc, char** argv) {
    ...

    // am 是一个引用, 指向 ActionManager 实例
    ActionManager& am = ActionManager::GetInstance();

    ...

    am.QueueBuiltinAction(SetupCgroupsAction, "SetupCgroups");

    // 添加 Action: 触发事件 early-init [2.4.4.1.2.1]
    am.QueueEventTrigger("early-init");

    // 添加 Action: 等待 coldboot 完成
    am.QueueBuiltinAction(wait_for_coldboot_done_action, "wait_for_coldboot_done");

    am.QueueBuiltinAction(MixHwrngIntoLinuxRngAction, "MixHwrngIntoLinuxRng");
    am.QueueBuiltinAction(SetMmapRndBitsAction, "SetMmapRndBits");
    am.QueueBuiltinAction(SetKptrRestrictAction, "SetKptrRestrict");
    Keychords keychords;
    am.QueueBuiltinAction(
        [&epoll, &keychords](const BuiltinArguments& args) -> Result<Success> {
            for (const auto& svc : ServiceList::GetInstance()) {
                keychords.Register(svc->keycodes());
            }
            keychords.Start(&epoll, HandleKeychord);
            return Success();
        },
        "KeychordInit");
    am.QueueBuiltinAction(console_init_action, "console_init");

    // 添加 Action: 触发事件 init [2.4.4.1.2.1]
    am.QueueEventTrigger("init");

    am.QueueBuiltinAction(StartBoringSslSelfTest, "StartBoringSslSelfTest");
    am.QueueBuiltinAction(MixHwrngIntoLinuxRngAction, "MixHwrngIntoLinuxRng");

    // 添加 Action: 为 init 进程初始化 binder
    am.QueueBuiltinAction(InitBinder, "InitBinder");

    std::string bootmode = GetProperty("ro.bootmode", "");
    if (bootmode == "charger") {
        // 在充电模式下, 添加 Action: 触发事件 charger [2.4.4.1.2.1]
        am.QueueEventTrigger("charger");
    } else {
        // 否则, 添加 Action: 触发事件 late-init [2.4.4.1.2.1]
        am.QueueEventTrigger("late-init");
    }

    // 添加 Action: 触发当前所有的属性触发器
    am.QueueBuiltinAction(queue_property_triggers_action, "queue_property_triggers");

    ...

}
```

此外，在解析脚本或者属性值改变的时候，也会使用 ActionManager 添加 Action。



#### 2.4.5.2 HandleProcessActions

<!-- ((s->flags() & SVC_RUNNING) && s->timeout_period()) -->

<!-- 详细分析 service 的重启过程 -->

```c++
static std::optional<boot_clock::time_point> HandleProcessActions() {
    std::optional<boot_clock::time_point> next_process_action_time;

    // 遍历 ServiceList
    for (const auto& s : ServiceList::GetInstance()) {
        // 判断服务是否处于运行中状态, 以及是否有超时时长
        if ((s->flags() & SVC_RUNNING) && s->timeout_period()) {
            // 计算出服务启动的超时时间
            auto timeout_time = s->time_started() + *s->timeout_period();

            if (boot_clock::now() > timeout_time) {
                // 如果当前时间已大于服务启动的超时时间, 说明服务启动已超时
                s->Timeout();
            } else {
                // 更新 next_process_action_time
                // 此时, next_process_action_time 表示最近的, 需要检查服务是否启动成功的时间点
                if (!next_process_action_time || timeout_time < *next_process_action_time) {
                    next_process_action_time = timeout_time;
                }
            }
        }

        // 如果服务不带有标志位 SVC_RESTARTING, 则不需要重启, 继续 for 循环
        if (!(s->flags() & SVC_RESTARTING)) continue;

        // 计算出服务启动的超时时间
        auto restart_time = s->time_started() + s->restart_period();

        // 现在服务需要重启, 接下来重启已满足重启条件的服务
        if (boot_clock::now() > restart_time) {
            // 如果当前时间已大于服务的重启时间, 那么立即启动服务
            if (auto result = s->Start(); !result) {
                LOG(ERROR) << "Could not restart process '" << s->name() << "': " << result.error();
            }
        } else {
            // 更新 next_process_action_time
            // 此时, next_process_action_time 表示最近的, 需要对服务进行重启操作的时间点
            if (!next_process_action_time || restart_time < *next_process_action_time) {
                next_process_action_time = restart_time;
            }
        }
    }
    return next_process_action_time;
}
```

此函数的工作有：

1. 检查带有标志位 `SVC_RUNNING` 的服务是否超时，如果服务启动超时，则调用 `Timeout` 函数，否则更新下次检查的时间点。
2. 当服务带有标志位 `SVC_RESTARTING` 时，说明服务需要重启。如果当前时间已大于服务的重启时间，则立即重启服务；否则，更新下次重启的时间点。 

经分析可知，`next_process_action_time` 可能表示下一次的检查服务是否启动超时的时间点，也可能表示下一次为某个服务重启的时间点。

注意到，在之前的分析信号 [2.4.3.1.4.3] 提到过，当 init 收到子进程终结的信号之后，会为有重启需要的服务添加标志位 `SVC_RESTARTING`。那么现在可以知道，在 init 进程进入无限循环状态后，就会处理这些带有标志位 `SVC_RESTARTING` 的服务，重启满足条件的服务。



# 参考

[Android系统启动-综述](http://gityuan.com/2016/02/01/android-booting/)

[Android系统启动-Init篇](http://gityuan.com/2016/02/05/android-init/)

[epoll(7)](https://man7.org/linux/man-pages/man7/epoll.7.html)

[Signal](https://en.wikipedia.org/wiki/Signal_(IPC))

[signal(7)](https://man7.org/linux/man-pages/man7/signal.7.html)

[wait(2)](https://man7.org/linux/man-pages/man2/wait.2.html)

[signalfd(2)](https://man7.org/linux/man-pages/man2/signalfd.2.html)

