# ThreadPool

## 参数

```java
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue
                              RejectedExecutionHandler handler) 
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue
             Executors.defaultThreadFactory(), handler);
    }
```

1. corePoolSize

   线程池维护核心线程数量，即使线程池处于空闲状态，核心线程也不会被回收。
2. maximumPoolSize

   线程池允许承载的最大线程数量，当核心线程全部处于工作状态，线程池接收到新的任务，线程池会先把新任务放入等待队列，直到等待队列满载，才会开启新的线程（<= maximumPoolSize）消费等待队列中的任务。
3. keepAliveTime & unit

   超出核心线程数之外的线程最大存活时间，空闲超出这个时间的就会被回收。
4. workQueue

   任务等待队列，当线程池核心线程被用完后，就会把新提交的任务放入队列等待。

   - ArrayBlockingQueue：基于数组的有界阻塞队列，按FIFO排序。
   - LinkedBlockingQuene：基于链表的无界阻塞队列，默认容量为Interger.MAX，按照FIFO排
     序，使用时需要指定容量。
   - PriorityBlockingQueue：具有优先级的无界阻塞队列，优先级通过任务的 Comparator 决定。
5. threadFactory

   创建线程的工厂，一般在定义线程池时也会同时自定义工厂，为线程设置可读性更好的名字，方便线上问题排查
6. handler

   当等待队列已满，新提交的任务就会触发拒绝策略

   1. CallerRunsPolicy

      直接执行任务的run方法。
   2. AbortPolicy（默认）

      直接丢弃任务，并抛出RejectedExecutionException异常。
   3. DiscardPolicy

      直接丢弃任务。
   4. DiscardOldestPolicy

      丢弃最早进入队列的任务，然后把新任务放入队列。

## 执行流程

提交一个交新任务：

1. 如果线程数小于核心线程数时，创建线程。
2. 如果线程数大于等于核心线程数，且任务队列未满时，将任务放入任务队列。
3. 如果线程数大于等于核心线程数，且任务队列已满
   1. 如果线程数小于最大线程数，创建线程
   2. 如果线程数等于最大线程数，抛出异常，拒绝任务

## 如何设置参数
