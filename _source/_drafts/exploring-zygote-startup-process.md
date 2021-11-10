---
title: 分析 Zygote 启动流程
tags:
- Zygote
categories:
- [Android]
---



> 本文基于源代码: android-security-10.0.0_r56



# 1. 前言

Android 基于 Linux 内核，在内核启动过程中会创建出 init 进程。之后，init 进程会解析 `init.rc` 脚本文件，进而启动 Zygote 进程。Zygote 启动完成后，监听来自客户端的请求，接收到后立即唤醒并执行相应工作。

<!-- TODO -->

<!-- more -->



# 2. 简析 init 进程的启动流程

init 进程由内核启动，是系统用户空间的第一个进程，其进程的 pid 为 1。



## 2.1 init 进程的入口

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

其中，`argc` 表示命令行参数的个数，`argv` 则是传入的参数，`argv[0]` 是程序的全路径名，`arg[1]` 才是实际意义上的第一个参数。

> `strcmp(str1, str2)` 是字符串比较函数，返回值为整数。当两个字符串相等时，函数返回 0。
>
> `basename(path)` 函数可以根据给定的一个路径，返回文件名。例如：传入参数 "/system/bin/ueventd"，函数返回 "ueventd"。

`main` 函数会根据传入的参数，选择执行方式：

1. 如果从路径参数 `argv[0]` 解析出 "ueventd"，执行 `ueventd_main(argc, argv)`
2. 如果带参启动 `init`，执行 `SubcontextMain(argc, argv, &function_map)`，`SetupSelinux(argv)`，`SecondStageMain(argc, argv)` 中的一个
3. 否则，默认执行 `FirstStageMain(argc, argv)`



## 2.2 启动的第一阶段

启动的第一阶段会执行 `int FirstStageMain(int argc, char** argv)`，源代码路径为 `system/core/init/first_stage_init.cpp`。

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
    // 设置文件权限, 分析见 2.2.1
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

    // 执行 /system/bin/init/selinux_setup, 初始化 SELinux
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

函数的主要工作有：挂载文件系统，初始化 SELinux。



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

剩余的 9 个字符，每 3 个字符分为一组，分别表示用户，用户组，其他用户的权限。

3 个字符组成的字符串，第 1 位控制读权限，第 2 位控制写权限，第 3 位控制执行权限：

- 如果拥有读权限，第 1 位字符为 `r`；否则为 `-`。
- 如果拥有写权限，第 2 位字符为 `w`；否则为 `-`。
- 如果拥有读权限，第 3 位字符为 `r`；否则为 `-`。



示例：

`-rwxrw-r--`，表示一个普通文件，用户拥有读，写和执行权限，用户组拥有读和写权限，其他用户只有读权限。



#### 2.2.1.2 数字表示法

可以用 4 位八进制数字表示权限，第 1 位同样表示文件类型，剩余的 3 位数字，分别表示用户，用户组，其他用户的权限。

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

因此，用符号表示法表示的权限，同样可以使用数字表示法去表示，示例：

| 符号表示法 | 数字表示法 |                             说明                             |
| :--------: | :--------: | :----------------------------------------------------------: |
| -rwxrwxrwx |    0777    |  一个普通文件，用户，用户组，其他用户都有读，写，执行的权限  |
| -rwxrw-r-- |    0764    | 一个普通文件，用户拥有读，写和执行权限，用户组拥有读和写权限，其他用户只有读权限。 |



#### 2.2.1.3 分析源码中的权限设置

```c++
// Don't expose the raw commandline to unprivileged processes.
CHECKCALL(chmod("/proc/cmdline", 0440));

// Don't expose the raw bootconfig to unprivileged processes.
chmod("/proc/bootconfig", 0440);
```

在源码中有两处使用到 `chmod` 命令，分别对文件 `/proc/cmdline, /proc/bootconfig` 设置了权限 `0440`，表明root 用户及其用户组也只有文件的读权限。



## 2.3 初始化 SELinux

初始化 SELinux 会执行 `int SetupSelinux(char** argv)`，源代码路径为 `system/core/init/selinux.cpp`。

```c++
// This function initializes SELinux then execs init to run in the init SELinux context.
int SetupSelinux(char** argv) {
    InitKernelLogging(argv);

    if (REBOOT_BOOTLOADER_ON_PANIC) {
        InstallRebootSignalHandlers();
    }

    // Set up SELinux, loading the SELinux policy.
    // 初始化 SELinux
    SelinuxSetupKernelLogging();
    SelinuxInitialize();

    ...

    // 执行 /system/bin/init/second_stage, 进入第二阶段
    const char* path = "/system/bin/init";
    const char* args[] = {path, "second_stage", nullptr};
    execv(path, const_cast<char**>(args));

    ...

    return 1;
}
```

该函数首先是初始化 SELinux，然后执行 `/system/bin/init/second_stage`，进入启动的第二阶段。



## 2.4 启动的第二阶段

启动的第二阶段会执行 `int SecondStageMain(int argc, char** argv)`，源代码路径为 `system/core/init/init.cpp`。

```c++
int SecondStageMain(int argc, char** argv) {
    if (REBOOT_BOOTLOADER_ON_PANIC) {
        InstallRebootSignalHandlers();
    }

    SetStdioToDevNull(argv);
    InitKernelLogging(argv);
    LOG(INFO) << "init second stage started!";

    ...

    // Now set up SELinux for second stage.
    // 为第二阶段设置 SeLinux
    SelinuxSetupKernelLogging();
    SelabelInitialize();
    SelinuxRestoreContext();

    ...

    subcontexts = InitializeSubcontexts();

    ActionManager& am = ActionManager::GetInstance();
    ServiceList& sm = ServiceList::GetInstance();

    // 加载 init.rc 脚本文件, 分析见 2.4.1
    LoadBootScripts(am, sm);

    ...

    am.QueueBuiltinAction(SetupCgroupsAction, "SetupCgroups");

    am.QueueEventTrigger("early-init");

    // Queue an action that waits for coldboot done so we know ueventd has set up all of /dev...
    am.QueueBuiltinAction(wait_for_coldboot_done_action, "wait_for_coldboot_done");
    // ... so that we can start queuing up actions that require stuff from /dev.
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

    // Trigger all the boot actions to get us started.
    am.QueueEventTrigger("init");

    // Starting the BoringSSL self test, for NIAP certification compliance.
    am.QueueBuiltinAction(StartBoringSslSelfTest, "StartBoringSslSelfTest");

    // Repeat mix_hwrng_into_linux_rng in case /dev/hw_random or /dev/random
    // wasn't ready immediately after wait_for_coldboot_done
    am.QueueBuiltinAction(MixHwrngIntoLinuxRngAction, "MixHwrngIntoLinuxRng");

    // Initialize binder before bringing up other system services
    am.QueueBuiltinAction(InitBinder, "InitBinder");

    // Don't mount filesystems or start core system services in charger mode.
    std::string bootmode = GetProperty("ro.bootmode", "");
    if (bootmode == "charger") {
        am.QueueEventTrigger("charger");
    } else {
        am.QueueEventTrigger("late-init");
    }

    // Run all property triggers based on current state of the properties.
    am.QueueBuiltinAction(queue_property_triggers_action, "queue_property_triggers");

    while (true) {
        // By default, sleep until something happens.
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

                // If there's a process that needs restarting, wake up in time for that.
                if (next_process_action_time) {
                    epoll_timeout = std::chrono::ceil<std::chrono::milliseconds>(
                            *next_process_action_time - boot_clock::now());
                    if (*epoll_timeout < 0ms) epoll_timeout = 0ms;
                }
            }

            // If there's more work to do, wake up again immediately.
            if (am.HasMoreCommands()) epoll_timeout = 0ms;
        }

        if (auto result = epoll.Wait(epoll_timeout); !result) {
            LOG(ERROR) << result.error();
        }
    }

    return 0;
}
```



### 2.4.1 加载 init.rc 脚本文件

加载 init.rc 脚本文件会执行 `void LoadBootScripts(ActionManager& action_manager, ServiceList& service_list)`，源代码路径为 `system/core/init/init.cpp`。

```c++
static void LoadBootScripts(ActionManager& action_manager, ServiceList& service_list) {
    Parser parser = CreateParser(action_manager, service_list);

    std::string bootscript = GetProperty("ro.boot.init_rc", "");
    if (bootscript.empty()) {
        parser.ParseConfig("/init.rc");
        if (!parser.ParseConfig("/system/etc/init")) {
            late_import_paths.emplace_back("/system/etc/init");
        }
        if (!parser.ParseConfig("/product/etc/init")) {
            late_import_paths.emplace_back("/product/etc/init");
        }
        if (!parser.ParseConfig("/product_services/etc/init")) {
            late_import_paths.emplace_back("/product_services/etc/init");
        }
        if (!parser.ParseConfig("/odm/etc/init")) {
            late_import_paths.emplace_back("/odm/etc/init");
        }
        if (!parser.ParseConfig("/vendor/etc/init")) {
            late_import_paths.emplace_back("/vendor/etc/init");
        }
    } else {
        parser.ParseConfig(bootscript);
    }
}
```





# 参考

[Android系统启动-综述](http://gityuan.com/2016/02/01/android-booting/)

[Android系统启动-zygote篇](http://gityuan.com/2016/02/13/android-zygote/)

