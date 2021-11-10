---
title: Android 屏幕刷新机制
tags:
- Graphics
categories:
- [Android, Framework, Graphics]
---



<!-- more -->

## ViewRootImpl::scheduleTraversals()

```java
void scheduleTraversals() {
    // 变量 mTraversalScheduled 的分析见[小节3.1]
    if (!mTraversalScheduled) {
        // 将变量 mTraversalScheduled 设为 true, 防止逻辑重入
        mTraversalScheduled = true;          

        // 发起同步屏障, 分析见[小节3.2]
        mTraversalBarrier = mHandler.getLooper().getQueue().postSyncBarrier();

        // 方法的第2个入参 mTraversalRunnable 的类型是 Runnable, 分析见[小节3.3]
        // 关于 Choreographer 的分析见[小节4]
        mChoreographer.postCallback(
                Choreographer.CALLBACK_TRAVERSAL, mTraversalRunnable, null);

        if (!mUnbufferedInputDispatch) {
            scheduleConsumeBatchedInput();
        }
        notifyRendererOfFramePending();
        pokeDrawLockIfNeeded();
    }
}
```



### ViewRootImpl::mTraversalScheduled

变量 mTraversalScheduled 的声明如下：

```java
public boolean mTraversalScheduled;
```

从变量声明可以看出，mTraversalScheduled 就是一个普通的 boolean 类型的变量。

在 scheduleTraversals() 方法中，变量 mTraversalScheduled 的作用是防止重复执行同一段逻辑。

对变量 mTraversalScheduled 的操作之所以没有加上线程同步，是因为 Android UI 操作是单线程的，在对变量 mTraversalScheduled 的操作之前都会检查一次线程，因此没必要对其加锁进行操作。

mTraversalScheduled  变量只有在执行 scheduleTraversals() 方法的过程中才有机会将 mTraversalScheduled 设为 true。



### MessageQueue::postSyncBarrier()

```java
public int postSyncBarrier() {
    return postSyncBarrier(SystemClock.uptimeMillis());
}

private int postSyncBarrier(long when) {
    synchronized (this) {
        final int token = mNextBarrierToken++;
        final Message msg = Message.obtain();
        msg.markInUse();
        msg.when = when;
        msg.arg1 = token;

        Message prev = null;
        Message p = mMessages;
        if (when != 0) {
            while (p != null && p.when <= when) {
                prev = p;
                p = p.next;
            }
        }
        if (prev != null) {
            msg.next = p;
            prev.next = msg;
        } else {
            msg.next = p;
            mMessages = msg;
        }
        return token;
    }
}
```

关于消息队列的同步屏障，我在 [Android 同步屏障](https://hasssssssh.github.io/2021/03/25/android-sync-barrier/) 一文中分析过，在这里目的是为了更快的响应UI刷新事件，让ViewRootImpl::mTraversalRunnable 尽快被执行。



### ViewRootImpl::mTraversalRunnable

入参 mTraversalRunnable 是 TraversalRunnable 的实例，TraversalRunnable 的实现如下：

```java
final class TraversalRunnable implements Runnable {
    @Override
    public void run() {
        doTraversal();
    }
}
```

这个 Runnable 执行的是 doTraversal() 方法，接着来看这个方法。

#### ViewRootImpl::doTraversal()

```java
void doTraversal() {
    if (mTraversalScheduled) {
        // TODO
        // 在进入 doTraversal 的核心逻辑后, 首先将变量 mTraversalScheduled 置为 false
        // 说明即使多次调用 scheduleTraversals() 方法, 
        mTraversalScheduled = false;

        // 移除消息屏障
        mHandler.getLooper().getQueue().removeSyncBarrier(mTraversalBarrier);

        // 省略...

        // 触发 View 的绘制流程
        performTraversals();

        // 省略...
    }
}
```

performTraversals() 这个方法我曾经在之前的一篇文章 (// TODO) 中做过分析，View 的测量、布局、绘制三大流程都是由此方法发起的。

现在可以知道，View 的绘制流程是在 doTraversal() 方法中发起的，而 doTraversal() 方法又被封装成一个 Runnable，接下来就要搞清楚这个 Runnable 是在什么时机被执行的。



## Choreographer



### Choreographer::postCallback(int, Runnable, Object)

```java
public void postCallback(int callbackType, Runnable action, Object token) {
    postCallbackDelayed(callbackType, action, token, 0);
}
```





