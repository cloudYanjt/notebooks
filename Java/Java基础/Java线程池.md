# java线程池

线程池（Thread Pool）是一种基于池化思想管理线程的工具.
使用线程池一方面可以减少线程频繁创建和销毁带来的资源消耗，另一方面可以控制线程创建过度切换上下文的开销。

在 Java 中使用最多的是 JDK 提供的 ThreadPoolExecutor 线程池工具，下面就来看一看它是怎么工作的。

## 1、ThreadPoolExecutor 的核心参数

先看一下它的构造方法
```
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler)
```

ThreadPoolExecutor 构造方法有7个参数

|      参数       |                                     说明                                     |
| :-------------: | :--------------------------------------------------------------------------: |
|  corePoolSize   |        核心线程数，即使它们处于空闲状态，除非设置了allowCoreThreadTimeOut        |
| maximumPoolSize |                            线程池中允许的最大线程数                            |
|  keepAliveTime  |         当线程数大于核心线程数时，空闲线程在终止前等待新任务的最长等待时间         |
|      unit       |                          keepAliveTime参数的时间单位                          |
|    workQueue    | 用于在执行任务之前保存任务的队列。 这个队列将只保存execute方法提交的Runnable任务。 |
|  threadFactory  |                         执行程序创建新线程时使用的工厂                          |
|     handler     |           任务执行被阻塞时使用的处理程序，因为达到了线程边界和队列容量            |

## 2、往线程池中提交任务

往线程池提交任务有两种方式，一个是调用 submit 方法提交，另一个是直接调用 execute 方法。

通过 submit 提交的任务会封装成 FutureTask 然后再调用 execute 方法。

execute 主要有三大逻辑：
* 工作线程数小于核心线程数，则创建线程执行任务
* 核心线程数已满，将任务放到队列等待执行
* 队列已满，基于最大线程数开启线程执行任务，如果工作线程数超过最大线程数则执行拒绝策略。

下面是 execute 的源码，以及个人的理解。

```
    public void execute(Runnable command) {
        if (command == null)
            throw new NullPointerException();
        /*
         * Proceed in 3 steps:
         *
         * 1. If fewer than corePoolSize threads are running, try to
         * start a new thread with the given command as its first
         * task.  The call to addWorker atomically checks runState and
         * workerCount, and so prevents false alarms that would add
         * threads when it shouldn't, by returning false.
         *
         * 2. If a task can be successfully queued, then we still need
         * to double-check whether we should have added a thread
         * (because existing ones died since last checking) or that
         * the pool shut down since entry into this method. So we
         * recheck state and if necessary roll back the enqueuing if
         * stopped, or start a new thread if there are none.
         *
         * 3. If we cannot queue task, then we try to add a new
         * thread.  If it fails, we know we are shut down or saturated
         * and so reject the task.
         */
        int c = ctl.get();
        // 判断工作线程数是否小于核心线程数。小于核心线程数时，新创建一个线程执行任务
        if (workerCountOf(c) < corePoolSize) {
            //如果其他提交任务线程抢先占满核心线程数，会导致当前提交的任务并未提交(addWorker返回false)，所以这里再做了一次判断
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        //如果核心线程数满了，且线程池还处于运行状态，则将任务添加到队列
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            //添加到队列之后，再做判断，如果线程池不在运行状态且添加到队列中的任务还未被执行
            //则先从队列中删除该任务，然后执行拒绝策略
            if (! isRunning(recheck) && remove(command))
                reject(command);
            //如果线程池是运行中状态，但是工作线程已经没有了，那么新开一个工作线程从队列中获取任务执行。
            //（这里是为了避免核心线程数为0时，导致添加到队列中的任务无工作线程执行的情况）
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        //如果队列也满了，则尝试开启新的线程执行任务
        else if (!addWorker(command, false))
            //如果最大线程数也满了，那么执行拒绝策略
            reject(command);
    }
```

## 3、addWorker

在 addWorker 方法中可以看到，实际用来开启线程的对象不是我们调用 execute 时传的 Runnable，而是 ThreadPoolExecutor的内部类Worker。

Worker 继承了 AQS 实现了 Runnable 接口：

```
private final class Worker
        extends AbstractQueuedSynchronizer
        implements Runnable
```

addWorker 代码片段：

```
 ... ...
try {
            w = new Worker(firstTask);
            final Thread t = w.thread;
            if (t != null) {
                ... ...
                if (workerAdded) {
                    t.start();
                    workerStarted = true;
                }
            }
        }
 ... ...
```

## 4、RunWorker

```
final void runWorker(Worker w) {
        //这里获取当前线程其实也就是当前Worker创建的线程
        Thread wt = Thread.currentThread();
        //firstTask 是我们调用 execute 提交的任务
        Runnable task = w.firstTask;
        w.firstTask = null;
        w.unlock(); // allow interrupts
        boolean completedAbruptly = true;
        try {
        //提交的任务执行完后，该线程会尝试从队列中获取任务（getTask()）
            while (task != null || (task = getTask()) != null) {
                //这里加锁的主要原因是，当调用shutdown等操作时
                //如果获取不到锁则表示该Worker还有任务正在执行，不会立即结束该线程。
                //是线程任务安全执行完的保障
                w.lock();
                // If pool is stopping, ensure thread is interrupted;
                // if not, ensure thread is not interrupted.  This
                // requires a recheck in second case to deal with
                // shutdownNow race while clearing interrupt
                if ((runStateAtLeast(ctl.get(), STOP) ||
                     (Thread.interrupted() &&
                      runStateAtLeast(ctl.get(), STOP))) &&
                    !wt.isInterrupted())
                    wt.interrupt();
                try {
                    beforeExecute(wt, task);
                    try {
                        task.run();
                        afterExecute(task, null);
                    } catch (Throwable ex) {
                        afterExecute(task, ex);
                        throw ex;
                    }
                } finally {
                    task = null;
                    w.completedTasks++;
                    w.unlock();
                }
            }
            completedAbruptly = false;
        } finally {
            processWorkerExit(w, completedAbruptly);
        }
    }
```

## 5、再来看一下 getTask() 做了什么。

可以看到设置 keepAliveTime 线程空闲超时是通过BlockingQueue 的 poll(keepAliveTime, TimeUnit.NANOSECONDS)
做到的，workQueue.poll获取任务超时后返回null，runWorker方法也随着结束，线程生命周期到头结束。

*BlockingQueue 的 poll 方法，从队列中获取不到元素会返回null*
*BlockingQueue 的 take 方法，获取不到元素时线程会阻塞，直到从队列中获取到元素为止*


```
    ... ...
            // Are workers subject to culling?
            boolean timed = allowCoreThreadTimeOut || wc > corePoolSize;

            if ((wc > maximumPoolSize || (timed && timedOut))
                && (wc > 1 || workQueue.isEmpty())) {
                if (compareAndDecrementWorkerCount(c))
                    return null;
                continue;
            }

            try {
                Runnable r = timed ?
                    workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS) :
                    workQueue.take();
                if (r != null)
                    return r;
                timedOut = true;
            } catch (InterruptedException retry) {
                timedOut = false;
            }
```
