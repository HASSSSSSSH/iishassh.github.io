---
title: Android 同步屏障
date: 2021-03-25
updated: 2021-03-25
permalink: /android/sync-barrier/
tags:
categories:
- [Android, Framework, Handler]
---

在 Android 系统源码中，多处使用了同步屏障。例如：

```java
// ViewRootImpl.java

void scheduleTraversals() {
    if (!mTraversalScheduled) {
        mTraversalScheduled = true;

        // 发起同步屏障
        mTraversalBarrier = mHandler.getLooper().getQueue().postSyncBarrier();

        // 监听下一帧的信号
        mChoreographer.postCallback(
                Choreographer.CALLBACK_TRAVERSAL, mTraversalRunnable, null);

        // 省略...
    }
}
```

这是 Android 屏幕刷新过程中的一个操作，在调用 postCallback(int, Runnable, Object) 方法监听下一帧信号之前，首先向当前线程的消息队列发起了同步屏障。

接下来我们通过对源码的解读，分析出同步屏障的作用到底是什么。



<!-- more -->

# 1. Message 的种类

Message 中有一个 flag 变量，用于标记 Message 的类型。

## 1.1 Message::setAsynchronous(boolean)

```java
public void setAsynchronous(boolean async) {
    if (async) {
        // 异步消息, 通过或运算添加 FLAG_ASYNCHRONOUS 标记
        flags |= FLAG_ASYNCHRONOUS;
    } else {
        // 同步消息, 通过与运算移除 FLAG_ASYNCHRONOUS 标记
        flags &= ~FLAG_ASYNCHRONOUS;
    }
}
```

调用 setAsynchronous(boolean) 方法，可以将 Message 设置为**同步消息**或者**异步消息**。

## 1.2 Message::isAsynchronous()

```java
public boolean isAsynchronous() {
    // 通过与运算判断 flags 是否带有 FLAG_ASYNCHRONOUS 标记
    return (flags & FLAG_ASYNCHRONOUS) != 0;
}
```

通过 isAsynchronous() 方法，可以判断 Message 是**同步消息**还是**异步消息**。

由此可知，Message 可以分为两类：

- 同步消息
- 异步消息



# 2. 发起同步屏障

在屏幕刷新的例子中，同步屏障是通过调用 MessageQueue::postSyncBarrier() 方法发起的，实际上是间接调用了 MessageQueue::postSyncBarrier(long) 方法。

```java
public int postSyncBarrier() {
    return postSyncBarrier(SystemClock.uptimeMillis());
}
```

这里传入的 SystemClock.uptimeMillis() 参数，表示了从系统启动到当前时刻经过的时间。

```java
private int postSyncBarrier(long when) {
    // 注意到这里需要加锁进行操作, 因为 postSyncBarrier() 方法是有可能在多线程环境下调用
    synchronized (this) {

        // mNextBarrierToken 是 int 类型, token 实际上是通过发起同步屏障次数来表示的
        final int token = mNextBarrierToken++;

        // 注意到 Message 实例是通过 Message.obtain() 方法获取的, 分析见小节2.1
        final Message msg = Message.obtain();
        msg.markInUse();
        msg.when = when;
        msg.arg1 = token;

        // 这里所做的就是按照消息执行时刻 when 排序, 将新建的 Message 实例插入到消息队列中对应的位置
        // 注意到, 对于所以已在消息队列中的消息 p, 如果满足 p.when <= when, 则消息 p 排在新建的 Message 之前, 优先处理消息 p
        Message prev = null;
        Message p = mMessages;
        if (when != 0) {
            while (p != null && p.when <= when) {
                prev = p;
                p = p.next;
            }
        }
        if (prev != null) { // invariant: p == prev.next
            msg.next = p;
            prev.next = msg;
        } else {
            msg.next = p;
            mMessages = msg;
        }

        // 返回特定的 token, 用于取消同步屏障, 分析见[小节4]
        return token;
    }
}
```

## 2.1 获取 Message 实例

```java
final Message msg = Message.obtain();
msg.markInUse();
msg.when = when;
msg.arg1 = token;
```

Message 实例是通过 Message.obtain() 方法获取的，来看看这个方法：

```java
public static Message obtain() {
    synchronized (sPoolSync) {
        if (sPool != null) {
            Message m = sPool;
            sPool = m.next;
            m.next = null;
            m.flags = 0; // clear in-use flag
            sPoolSize--;
            return m;
        }
    }
    return new Message();
}
```

从这个方法可以看出来，Message 实例是从缓存池中获取的，当缓存池没有可用的 Message 时，则新建一个 Message 对象。

在获取到 Message 实例以后，将其标记为使用中，并设置 Message 的 when 和 arg1 属性。

我们知道，Message 中有一个重要的属性 target，其类型为 Handler。当消息被消费时，用于处理该消息的 Handler 就是 message.target 指向的 Handler。

但是，这里创建出的 Message 实例没有设置 target，也就是 message.target = null。



## 2.2 小结

通过上面的分析，我们知道发起同步屏障其实就是将一个**不带有 target 属性的 Message (接下来我将称之为同步屏障消息)** 按照执行时间的先后顺序插入到消息队列中。

现在消息队列中已经存在这样一个独特的消息，接着就需要分析 Android 是如何处理这种消息的。



# 3. 处理同步屏障消息

在 Android Handler 机制中，Looper 负责将 Message 分发给目标 Handler，依靠的是 Looper::loop() 方法中不断执行的 for 循环 ，首先来分析这个方法是如何获取 Message。

## 3.1 Looper::loop()

```java
public static void loop() {
    final Looper me = myLooper();

    // 省略...

    final MessageQueue queue = me.mQueue;

    // 省略...

    for (;;) {
        Message msg = queue.next();

        // 省略消息的处理过程
    }
}
```

从代码可以看出，Looper 其实是从 MessageQueue 不断取出 Message，然后再对 Message 进行处理的。

接着来分析 MessageQueue::next() 方法，了解 Message 是怎么获取的。



## 3.2 MessageQueue::next()

为了分析处理同步屏障消息这种场景，现在假设所有的按时间顺序排在同步屏障消息之前的 Message 都已经被消费，换句话说，现在**位于消息队列头部**的 Message 就是同步屏障消息。

```java
Message next() {
    // 省略...

    for (;;) {
        // 省略...

        synchronized (this) {
            final long now = SystemClock.uptimeMillis();

            Message prevMsg = null;

            // mMessages 其实就是队列头部的 Message
            Message msg = mMessages;

            // 根据假设, 现在我们知道 msg 就是同步屏障消息, msg.target = null 成立, 会进入以下代码分支
            if (msg != null && msg.target == null) {
                // isAsynchronous() 方法可以判断一个 Message 是同步消息还是异步消息
                // 这个循环目的是找出一个异步消息, 将其赋值给 msg
                do {
                    prevMsg = msg;
                    msg = msg.next;
                } while (msg != null && !msg.isAsynchronous());
            }

            if (msg != null) {
                // 如果找到目标 Message, 则进入以下代码分支
                if (now < msg.when) {
                    // 当目标 Message 触发时间大于当前时间, 则设置下一次轮询的超时时长
                    nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                } else {
                    // 目标 Message 可以立即消费
                    mBlocked = false;

                    // 这里对消息队列的操作, 目的是将目标 Message 从消息队列中移除
                    if (prevMsg != null) {
                        // 注意到, 在处理同步屏障消息的场景下, prevMsg 不可能是 null, 同步屏障消息不会被消费
                        // 在这里场景下, 被消费的总是异步消息
                        prevMsg.next = msg.next;
                    } else {
                        mMessages = msg.next;
                    }
                    msg.next = null;

                    if (DEBUG) Log.v(TAG, "Returning message: " + msg);
                    msg.markInUse();

                    // 返回异步消息
                    return msg;
                }
            } else {
                // 队列中没有可以消费的异步消息
                nextPollTimeoutMillis = -1;
            }

            // 省略...
        }

        // 省略...
    }
}
```



## 3.3 小结

通过上面的分析，可以得出一些结论：

- 同步屏障的目的是：阻塞消息队列中同步消息的消费，**只处理异步消息**。
- 在发起同步屏障之后，由于在消息队列中的**同步屏障消息不会被自动消费**，必须手动移除同步屏障。
- 在没有同步屏障的场景下，同步消息和异步消息在**从消息队列获取消息的过程**中没有区别。



# 4. 移除同步屏障

经过上面的分析，现在我们知道，在发起同步屏障之后，由于在消息队列中的**同步屏障消息不会被自动消费**，Android 提供了 MessageQueue::removeSyncBarrier(int) 方法让我们手动移除同步屏障，来看看这个方法做了什么。

## 4.1 MessageQueue::removeSyncBarrier(int)

```java
public void removeSyncBarrier(int token) {
    // 注意到这里需要加锁进行操作, 因为 removeSyncBarrier(int) 方法是有可能在多线程环境下调用
    synchronized (this) {
        Message prev = null;

      	// mMessages 是位于队列头部的 Message
        Message p = mMessages;

      	// 在发起同步屏障时, 新建 Message 实例的 arg1 属性存储的就是 token
      	// 所以这个循环的目的是: 找到 token 相等的同步屏障消息, 将其赋值给 p
        while (p != null && (p.target != null || p.arg1 != token)) {
            prev = p;
            p = p.next;
        }

        if (p == null) {
            // 在找不到目标同步屏障消息时, 会抛出异常
            throw new IllegalStateException("The specified message queue synchronization "
                    + " barrier token has not been posted or has already been removed.");
        }

        final boolean needWake;

      	// 将目标同步屏障消息从消息队列中移除
        if (prev != null) {
            prev.next = p.next;
            needWake = false;
        } else {
            mMessages = p.next;
            needWake = mMessages == null || mMessages.target != null;
        }
        p.recycleUnchecked();

        // 省略...
    }
}
```



## 4.2 小结

从代码中可以看出，想要移除同步屏障，需要保存发起同步屏障 postSyncBarrier() 方法的返回值 token，根据 token 对应地移除同步屏障。


