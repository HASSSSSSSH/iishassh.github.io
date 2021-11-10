---
title: 分析 Android app 进程创建流程
tags:
- Zygote
categories:
- [Android]
---



> 本文基于源代码: android-security-10.0.0_r56



## 1. 前言

<!-- more -->



### 1.1 前置知识

<!-- TODO -->



#### 1.1.1 Android 系统启动概述

<!-- TODO -->



#### 1.1.2 Linux fork

<!-- TODO -->



## 2. system_server 侧的逻辑

<!-- TODO -->

system_server 作为进程创建的发起方，入口在 ProcessList 当中，进程的启动流程最终会走到 `ProcessList.startProcess` 方法。



### 2.1 ProcessList.startProcess(HostingRecord, String, ProcessRecord, int, int[], int, int, String, String, String, String, long)

```java
private Process.ProcessStartResult startProcess(HostingRecord hostingRecord, String entryPoint,
        ProcessRecord app, int uid, int[] gids, int runtimeFlags, int mountExternal,
        String seInfo, String requiredAbi, String instructionSet, String invokeWith,
        long startTime) {
    try {
        Trace.traceBegin(Trace.TRACE_TAG_ACTIVITY_MANAGER, "Start proc: " +
                app.processName);
        checkSlow(startTime, "startProcess: asking zygote to start proc");
        final Process.ProcessStartResult startResult;
        if (hostingRecord.usesWebviewZygote()) {
            startResult = startWebView(entryPoint,
                    app.processName, uid, uid, gids, runtimeFlags, mountExternal,
                    app.info.targetSdkVersion, seInfo, requiredAbi, instructionSet,
                    app.info.dataDir, null, app.info.packageName,
                    new String[] {PROC_START_SEQ_IDENT + app.startSeq});
        } else if (hostingRecord.usesAppZygote()) {
            final AppZygote appZygote = createAppZygoteForProcessIfNeeded(app);

            startResult = appZygote.getProcess().start(entryPoint,
                    app.processName, uid, uid, gids, runtimeFlags, mountExternal,
                    app.info.targetSdkVersion, seInfo, requiredAbi, instructionSet,
                    app.info.dataDir, null, app.info.packageName,
                    /*useUsapPool=*/ false,
                    new String[] {PROC_START_SEQ_IDENT + app.startSeq});
        } else {
            // 一般的进程启动流程会进入此分支
            startResult = Process.start(entryPoint,
                    app.processName, uid, uid, gids, runtimeFlags, mountExternal,
                    app.info.targetSdkVersion, seInfo, requiredAbi, instructionSet,
                    app.info.dataDir, invokeWith, app.info.packageName,
                    new String[] {PROC_START_SEQ_IDENT + app.startSeq});
        }
        checkSlow(startTime, "startProcess: returned from zygote!");
        return startResult;
    } finally {
        Trace.traceEnd(Trace.TRACE_TAG_ACTIVITY_MANAGER);
    }
}
```

一般的进程启动流程，会调用 `Process.start` 方法，创建新进程。



### 2.2 Process.start(String, String, int, int, int[], int, int, int, String, String, String, String, String, String, String[])

```java
public static ProcessStartResult start(@NonNull final String processClass,
                                       @Nullable final String niceName,
                                       int uid, int gid, @Nullable int[] gids,
                                       int runtimeFlags,
                                       int mountExternal,
                                       int targetSdkVersion,
                                       @Nullable String seInfo,
                                       @NonNull String abi,
                                       @Nullable String instructionSet,
                                       @Nullable String appDataDir,
                                       @Nullable String invokeWith,
                                       @Nullable String packageName,
                                       @Nullable String[] zygoteArgs) {
    return ZYGOTE_PROCESS.start(processClass, niceName, uid, gid, gids,
                runtimeFlags, mountExternal, targetSdkVersion, seInfo,
                abi, instructionSet, appDataDir, invokeWith, packageName,
                /*useUsapPool=*/ true, zygoteArgs);
}
```

其中，`ZYGOTE_PROCESS` 是 ZygoteProcess 的实例：

```java
/**
 * State associated with the zygote process.
 * @hide
 */
public static final ZygoteProcess ZYGOTE_PROCESS = new ZygoteProcess();
```

再来看看 ZygoteProcess 的构造方法：

```java
public ZygoteProcess() {
    mZygoteSocketAddress =
            new LocalSocketAddress(Zygote.PRIMARY_SOCKET_NAME,
                                   LocalSocketAddress.Namespace.RESERVED);
    mZygoteSecondarySocketAddress =
            new LocalSocketAddress(Zygote.SECONDARY_SOCKET_NAME,
                                   LocalSocketAddress.Namespace.RESERVED);

    mUsapPoolSocketAddress =
            new LocalSocketAddress(Zygote.USAP_POOL_PRIMARY_SOCKET_NAME,
                                   LocalSocketAddress.Namespace.RESERVED);
    mUsapPoolSecondarySocketAddress =
            new LocalSocketAddress(Zygote.USAP_POOL_SECONDARY_SOCKET_NAME,
                                   LocalSocketAddress.Namespace.RESERVED);
}
```

ZygoteProcess 的构造方法初始化了数个 Socket 地址，这些地址将会在与 Zygote 进程建立连接时用到。

接下来分析 `ZygoteProcess.start` 方法。



### 2.3 ZygoteProcess.start(String, String, int, int, int[], int, int, int, String, String, String, String, String, String, boolean, String[])

```java
public final Process.ProcessStartResult start(@NonNull final String processClass,
                                              final String niceName,
                                              int uid, int gid, @Nullable int[] gids,
                                              int runtimeFlags, int mountExternal,
                                              int targetSdkVersion,
                                              @Nullable String seInfo,
                                              @NonNull String abi,
                                              @Nullable String instructionSet,
                                              @Nullable String appDataDir,
                                              @Nullable String invokeWith,
                                              @Nullable String packageName,
                                              boolean useUsapPool,
                                              @Nullable String[] zygoteArgs) {
    // UASP 相关, 分析见 2.3.1
    if (fetchUsapPoolEnabledPropWithMinInterval()) {
        informZygotesOfUsapPoolStatus();
    }

    try {
        return startViaZygote(processClass, niceName, uid, gid, gids,
                runtimeFlags, mountExternal, targetSdkVersion, seInfo,
                abi, instructionSet, appDataDir, invokeWith, /*startChildZygote=*/ false,
                packageName, useUsapPool, zygoteArgs);
    } catch (ZygoteStartFailedEx ex) {
        Log.e(LOG_TAG,
                "Starting VM process through Zygote failed");
        throw new RuntimeException(
                "Starting VM process through Zygote failed", ex);
    }
}
```

方法中有一段与 UASP 相关的逻辑，分析见 2.3.1，紧接着就会调用 `ZygoteProcess.startViaZygote` 方法。

注意到，此方法还会把 `ZygoteProcess.startViaZygote` 方法可能抛出的 `ZygoteStartFailedEx` 转换成 `RuntimeException`。



#### 2.3.1 ZygoteProcess.fetchUsapPoolEnabledPropWithMinInterval()

```java
private boolean fetchUsapPoolEnabledPropWithMinInterval() {
    final long currentTimestamp = SystemClock.elapsedRealtime();

    if (SystemProperties.get("dalvik.vm.boot-image", "").endsWith("apex.art")) {
        if (currentTimestamp <= 15000) {
            return false;
        }
    }

    if (mIsFirstPropCheck
            || (currentTimestamp - mLastPropCheckTimestamp >= Zygote.PROPERTY_CHECK_INTERVAL)) {
        mIsFirstPropCheck = false;
        mLastPropCheckTimestamp = currentTimestamp;
      
      	// 返回是否开启 UsapPool 的判断结果, 分析见 2.3.1.1
        return fetchUsapPoolEnabledProp();
    }

    return false;
}
```



##### 2.3.1.1 ZygoteProcess.fetchUsapPoolEnabledProp()

```java
private boolean fetchUsapPoolEnabledProp() {
    boolean origVal = mUsapPoolEnabled;

    final String propertyString = Zygote.getConfigurationProperty(
            ZygoteConfig.USAP_POOL_ENABLED, USAP_POOL_ENABLED_DEFAULT);

    if (!propertyString.isEmpty()) {
        mUsapPoolEnabled = Zygote.getConfigurationPropertyBoolean(
              ZygoteConfig.USAP_POOL_ENABLED,
              Boolean.parseBoolean(USAP_POOL_ENABLED_DEFAULT));
    }

    boolean valueChanged = origVal != mUsapPoolEnabled;

    if (valueChanged) {
        Log.i(LOG_TAG, "usapPoolEnabled = " + mUsapPoolEnabled);
    }

    return valueChanged;
}
```

这个方法的主要目的是从系统配置文件中加载 UsapPool 的开启状态，而默认状态 `USAP_POOL_ENABLED_DEFAULT` 是 false，也就是说 UsapPool 默认是不开启的。

因此，接下来的分析都会默认跳过 UASP 相关逻辑。



### 2.4 ZygoteProcess.startViaZygote(String, String, int, int, int[], int, int, int, String, String, String, String, String, boolean, String, boolean, String[])

```java
private Process.ProcessStartResult startViaZygote(@NonNull final String processClass,
                                                  @Nullable final String niceName,
                                                  final int uid, final int gid,
                                                  @Nullable final int[] gids,
                                                  int runtimeFlags, int mountExternal,
                                                  int targetSdkVersion,
                                                  @Nullable String seInfo,
                                                  @NonNull String abi,
                                                  @Nullable String instructionSet,
                                                  @Nullable String appDataDir,
                                                  @Nullable String invokeWith,
                                                  boolean startChildZygote,
                                                  @Nullable String packageName,
                                                  boolean useUsapPool,
                                                  @Nullable String[] extraArgs)
                                                  throws ZygoteStartFailedEx {
    ArrayList<String> argsForZygote = new ArrayList<>();

    // --runtime-args, --setuid=, --setgid=,
    // and --setgroups= must go first
    argsForZygote.add("--runtime-args");
    argsForZygote.add("--setuid=" + uid);
    argsForZygote.add("--setgid=" + gid);
    argsForZygote.add("--runtime-flags=" + runtimeFlags);

    ...

    synchronized(mLock) {
      	// openZygoteSocketIfNeeded 方法分析见 2.4.1
        return zygoteSendArgsAndGetResult(openZygoteSocketIfNeeded(abi),
                                          useUsapPool,
                                          argsForZygote);
    }
}
```

这个方法的主要目的是将 uid, gid 等参数保存在数组 `argsForZygote` 当中，接下来将会用到这些参数。



#### 2.4.1 ZygoteProcess.openZygoteSocketIfNeeded(String)

```java
@GuardedBy("mLock")
private ZygoteState openZygoteSocketIfNeeded(String abi) throws ZygoteStartFailedEx {
    try {
        attemptConnectionToPrimaryZygote();

        if (primaryZygoteState.matches(abi)) {
            return primaryZygoteState;
        }

        if (mZygoteSecondarySocketAddress != null) {
            // The primary zygote didn't match. Try the secondary.
            attemptConnectionToSecondaryZygote();

            if (secondaryZygoteState.matches(abi)) {
                return secondaryZygoteState;
            }
        }
    } catch (IOException ioe) {
        throw new ZygoteStartFailedEx("Error connecting to zygote", ioe);
    }

    throw new ZygoteStartFailedEx("Unsupported zygote ABI: " + abi);
}
```

此方法主要目的是尝试与 Zygote 进程建立连接，如果失败，将会抛出异常。

注意到，方法的注解 `@GuardedBy("mLock")` 表明外部在调用此方法之前需要对 `mLock` 加锁，以确保方法调用是同步的。



### 2.5 ZygoteProcess.zygoteSendArgsAndGetResult(ZygoteState, boolean, ArrayList)

```java
@GuardedBy("mLock")
private Process.ProcessStartResult zygoteSendArgsAndGetResult(
        ZygoteState zygoteState, boolean useUsapPool, @NonNull ArrayList<String> args)
        throws ZygoteStartFailedEx {

    // 检查参数中是否存在不合法字符
    for (String arg : args) {
        if (arg.indexOf('\n') >= 0) {
            throw new ZygoteStartFailedEx("Embedded newlines not allowed");
        } else if (arg.indexOf('\r') >= 0) {
            throw new ZygoteStartFailedEx("Embedded carriage returns not allowed");
        }
    }

    String msgStr = args.size() + "\n" + String.join("\n", args) + "\n";

  	...

    return attemptZygoteSendArgsAndGetResult(zygoteState, msgStr);
}
```

此方法检查参数的合法性，紧接着就调用了 `ZygoteProcess.attemptZygoteSendArgsAndGetResult` 方法。



### 2.6 ZygoteProcess.attemptZygoteSendArgsAndGetResult(ZygoteState, String)

```java
private Process.ProcessStartResult attemptZygoteSendArgsAndGetResult(
        ZygoteState zygoteState, String msgStr) throws ZygoteStartFailedEx {
    try {
      	// 已经与 Zygote 进程建立了 Socket 连接, 此时两者间的通信就像读写文件流一样方便
        final BufferedWriter zygoteWriter = zygoteState.mZygoteOutputWriter;
        final DataInputStream zygoteInputStream = zygoteState.mZygoteInputStream;

      	// 往 Zygote 写入参数
        zygoteWriter.write(msgStr);
        zygoteWriter.flush();

      	// 读取结果
        Process.ProcessStartResult result = new Process.ProcessStartResult();
        result.pid = zygoteInputStream.readInt();
        result.usingWrapper = zygoteInputStream.readBoolean();

        if (result.pid < 0) {
            // 如果读取到的 pid 小于 0, 说明进程创建失败
            throw new ZygoteStartFailedEx("fork() failed");
        }

        return result;
    } catch (IOException ex) {
      	// 当出现 IOException, 会关闭与 Zygote 进程的连接
        zygoteState.close();

        Log.e(LOG_TAG, "IO Exception while communicating with Zygote - "
                + ex.toString());
        throw new ZygoteStartFailedEx(ex);
    }
  
    // 注意到, 方法结束后没有调用 zygoteState.close(), 说明此时仍会与 Zygote 进程保持连接
}
```

此方法主要工作是与 Zygote 进程进行通信，向 Zygote 写入参数，通知 Zygote 创建进程。

如果从 Zygote 返回的 pid < 0，则说明进程创建失败；否则说明进程创建成功，通信结果包装在 `Process.ProcessStartResult` 当中。



## 3. Zygote 侧的逻辑

<!-- TODO -->



### 3.1 Zygote 的启动过程

Zygote 进程是由 init 进程创建的，在 Zygote 进程启动之后就会进入 `ZygoteInit.main` 方法。



#### 3.1.1 ZygoteInit.main(String)

```java
public static void main(String argv[]) {
    ZygoteServer zygoteServer = null;

    // 在 Zygote 即将启动时调用, 调用此方法后可以使得任何线程的创建都将出错
    ZygoteHooks.startZygoteNoThreadCreation();

    try {
      	// 分析见 3.1.1.1
        Os.setpgid(0, 0);
    } catch (ErrnoException ex) {
        throw new RuntimeException("Failed to setpgid(0,0)", ex);
    }

    Runnable caller;
    try {
        
      	...

      	// 开启 DDMS
        RuntimeInit.enableDdms();

      	// 加载系统配置, 接下来的启动流程将会使用到这些配置
        boolean startSystemServer = false;
        String zygoteSocketName = "zygote";
        String abiList = null;
        boolean enableLazyPreload = false;
        for (int i = 1; i < argv.length; i++) {
            if ("start-system-server".equals(argv[i])) {
                startSystemServer = true;
            } else if ("--enable-lazy-preload".equals(argv[i])) {
                enableLazyPreload = true;
            } else if (argv[i].startsWith(ABI_LIST_ARG)) {
                abiList = argv[i].substring(ABI_LIST_ARG.length());
            } else if (argv[i].startsWith(SOCKET_NAME_ARG)) {
                zygoteSocketName = argv[i].substring(SOCKET_NAME_ARG.length());
            } else {
                throw new RuntimeException("Unknown command line argument: " + argv[i]);
            }
        }

      	// Zygote.PRIMARY_SOCKET_NAME = "zygote"
        final boolean isPrimaryZygote = zygoteSocketName.equals(Zygote.PRIMARY_SOCKET_NAME);

        if (abiList == null) {
            throw new RuntimeException("No ABI list supplied.");
        }

        if (!enableLazyPreload) {
            bootTimingsTraceLog.traceBegin("ZygotePreload");
            EventLog.writeEvent(LOG_BOOT_PROGRESS_PRELOAD_START,
                    SystemClock.uptimeMillis());
          
            // 预加载资源
            preload(bootTimingsTraceLog);
          
            EventLog.writeEvent(LOG_BOOT_PROGRESS_PRELOAD_END,
                    SystemClock.uptimeMillis());
            bootTimingsTraceLog.traceEnd();
        } else {
            Zygote.resetNicePriority();
        }

        bootTimingsTraceLog.traceBegin("PostZygoteInitGC");
      	// Zygote 启动过程走到这里, 会主动触发 GC
        gcAndFinalize();
        bootTimingsTraceLog.traceEnd();

        bootTimingsTraceLog.traceEnd(); // ZygoteInit
        
      	// TODO: 分析
        Trace.setTracingEnabled(false, 0);


      	// TODO: 分析
        Zygote.initNativeState(isPrimaryZygote);

      	// 在 Zygote 启动结束后调用, 与此方法开头调用的 ZygoteHooks.startZygoteNoThreadCreation() 对应
        // 此时线程的创建恢复正常
        ZygoteHooks.stopZygoteNoThreadCreation();

      	// ZygoteServer 的构造方法分析见 3.1.1.2
        zygoteServer = new ZygoteServer(isPrimaryZygote);

      	// 启动 system_server 进程
        if (startSystemServer) {
            Runnable r = forkSystemServer(abiList, zygoteSocketName, zygoteServer);

          	// 在 forkSystemServer 方法中执行了 fork 操作
          	// 在父进程, 即 zygote 进程, 返回的 Runnable 是 null
          	// 而在子进程, 即 system_server 进程, 返回的是非 null 的 Runnable
            if (r != null) {
              	// 子进程在执行完 r.run() 后, 直接 return
              	// 意味着只有父进程才能走到后续的代码流程
                r.run();
                return;
            }
        }

        Log.i(TAG, "Accepting command socket connections");

      	// 分析见 3.1.1.3
        caller = zygoteServer.runSelectLoop(abiList);
    } catch (Throwable ex) {
        Log.e(TAG, "System zygote died with exception", ex);
        throw ex;
    } finally {
        if (zygoteServer != null) {
            zygoteServer.closeServerSocket();
        }
    }

    if (caller != null) {
        caller.run();
    }
}
```

此方法主要工作是启动 Zygote 进程，包含了预加载资源、创建 Socket、启动 system_server 进程等步骤，最后调用 `ZygoteServer.runSelectLoop` 方法。



##### 3.1.1.1 Os.setpgid(int, int)

```java
/**
 * See <a href="http://man7.org/linux/man-pages/man2/setpgid.2.html">setpgid(2)</a>.
 * @hide
 */
@libcore.api.CorePlatformApi
public static void setpgid(int pid, int pgid) throws ErrnoException { Libcore.os.setpgid(pid, pgid); }
```

注意到，此时传入的参数 `pid`, `pgid` 都为 0。

通过代码注释上的文档，可以了解到，当参数 `pid` 等于 0 时，将会给进程分配一个被使用过的进程 ID；当参数 `pgid` 等于 0 时，进程的 PGID 将会与它的进程 ID 保持一致。



##### 3.1.1.2 ZygoteServer(boolean)

```java
ZygoteServer(boolean isPrimaryZygote) {
    mUsapPoolEventFD = Zygote.getUsapPoolEventFD();

    if (isPrimaryZygote) {
        mZygoteSocket = Zygote.createManagedSocketFromInitSocket(Zygote.PRIMARY_SOCKET_NAME);
        mUsapPoolSocket =
                Zygote.createManagedSocketFromInitSocket(
                        Zygote.USAP_POOL_PRIMARY_SOCKET_NAME);
    } else {
        mZygoteSocket = Zygote.createManagedSocketFromInitSocket(Zygote.SECONDARY_SOCKET_NAME);
        mUsapPoolSocket =
                Zygote.createManagedSocketFromInitSocket(
                        Zygote.USAP_POOL_SECONDARY_SOCKET_NAME);
    }

    fetchUsapPoolPolicyProps();

    mUsapPoolSupported = true;
}
```

ZygoteServer 的构造方法主要工作是初始化作为服务端 Zygote 的 Socket 地址。



##### 3.1.1.3 ZygoteServer.runSelectLoop(String)

```java
Runnable runSelectLoop(String abiList) {
    ArrayList<FileDescriptor> socketFDs = new ArrayList<FileDescriptor>();
    ArrayList<ZygoteConnection> peers = new ArrayList<ZygoteConnection>();

    socketFDs.add(mZygoteSocket.getFileDescriptor());
    peers.add(null);

    while (true) {
        fetchUsapPoolPolicyPropsWithMinInterval();

        int[] usapPipeFDs = null;
        StructPollfd[] pollFDs = null;

        if (mUsapPoolEnabled) {
            usapPipeFDs = Zygote.getUsapPipeFDs();
            pollFDs = new StructPollfd[socketFDs.size() + 1 + usapPipeFDs.length];
        } else {
            pollFDs = new StructPollfd[socketFDs.size()];
        }

        int pollIndex = 0;
        for (FileDescriptor socketFD : socketFDs) {
            pollFDs[pollIndex] = new StructPollfd();
            pollFDs[pollIndex].fd = socketFD;
            pollFDs[pollIndex].events = (short) POLLIN;
            ++pollIndex;
        }

        ...

        try {
            Os.poll(pollFDs, -1);
        } catch (ErrnoException ex) {
            throw new RuntimeException("poll failed", ex);
        }

        boolean usapPoolFDRead = false;

        while (--pollIndex >= 0) {
            if ((pollFDs[pollIndex].revents & POLLIN) == 0) {
                continue;
            }

            if (pollIndex == 0) {
                // Zygote server socket

                ZygoteConnection newPeer = acceptCommandPeer(abiList);
                peers.add(newPeer);
                socketFDs.add(newPeer.getFileDescriptor());

            } else if (pollIndex < usapPoolEventFDIndex) {
                // Session socket accepted from the Zygote server socket

                try {
                    ZygoteConnection connection = peers.get(pollIndex);
                    final Runnable command = connection.processOneCommand(this);

                    // TODO (chriswailes): Is this extra check necessary?
                    if (mIsForkChild) {
                        // We're in the child. We should always have a command to run at this
                        // stage if processOneCommand hasn't called "exec".
                        if (command == null) {
                            throw new IllegalStateException("command == null");
                        }

                        return command;
                    } else {
                        // We're in the server - we should never have any commands to run.
                        if (command != null) {
                            throw new IllegalStateException("command != null");
                        }

                        if (connection.isClosedByPeer()) {
                            connection.closeSocket();
                            peers.remove(pollIndex);
                            socketFDs.remove(pollIndex);
                        }
                    }
                } catch (Exception e) {
                    if (!mIsForkChild) {

                        Slog.e(TAG, "Exception executing zygote command: ", e);

                        ZygoteConnection conn = peers.remove(pollIndex);
                        conn.closeSocket();

                        socketFDs.remove(pollIndex);
                    } else {
                        Log.e(TAG, "Caught post-fork exception in child process.", e);
                        throw e;
                    }
                } finally {
                    mIsForkChild = false;
                }
            } else {
                long messagePayload = -1;

                try {
                    byte[] buffer = new byte[Zygote.USAP_MANAGEMENT_MESSAGE_BYTES];
                    int readBytes = Os.read(pollFDs[pollIndex].fd, buffer, 0, buffer.length);

                    if (readBytes == Zygote.USAP_MANAGEMENT_MESSAGE_BYTES) {
                        DataInputStream inputStream =
                                new DataInputStream(new ByteArrayInputStream(buffer));

                        messagePayload = inputStream.readLong();
                    } else {
                        Log.e(TAG, "Incomplete read from USAP management FD of size "
                                + readBytes);
                        continue;
                    }
                } catch (Exception ex) {
                    if (pollIndex == usapPoolEventFDIndex) {
                        Log.e(TAG, "Failed to read from USAP pool event FD: "
                                + ex.getMessage());
                    } else {
                        Log.e(TAG, "Failed to read from USAP reporting pipe: "
                                + ex.getMessage());
                    }

                    continue;
                }

                if (pollIndex > usapPoolEventFDIndex) {
                    Zygote.removeUsapTableEntry((int) messagePayload);
                }

                usapPoolFDRead = true;
            }
        }

        ...

    }
}
```



## 参考

[Android系统启动-综述](http://gityuan.com/2016/02/01/android-booting/)

[理解Android进程创建流程](http://gityuan.com/2016/03/26/app-process-create/)

[Android系统启动Zygote进程(api 29)](https://www.jianshu.com/p/47d0121484fc)

