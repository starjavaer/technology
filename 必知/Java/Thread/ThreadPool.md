# ThreadLocal

## 原理

java 解决线程安全问题的思路有两种，一种是对共享变量的修改加锁，另一种是为每个线程分配一个共享变量副本，ThreadLocal 就是这种思想的具体实现。

核心源码如下：

```java
public class ThreadLocal<T> {
  
    public ThreadLocal() {
    }

    public T get() {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
        return setInitialValue();
    }

    static class ThreadLocalMap {

        static class Entry extends WeakReference<ThreadLocal<?>> {

            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
	
        private Entry[] table;
    }
}
```

Thread 类里有个 ThreadLocalMap 变量，ThreadLocalMap 是 ThreadLocalMap 的内部类，
ThreadLocalMap 的 key 是 TheadLocal，value 是业务对象，

当调用一个 TheadLocal 变量的 get() 时

先获取当前线程，然后从线程对象里取到 ThreadLocalMap，然后再从 map 里以当前ThreadLocal 对象为 key 获取到业务数据。

每个线程都有自己的 ThreadLocalMap，所以不会存在并发修改同一个变量问题。

而 ThreadLocalMap 的 key 定义为 ThreadMap，是因为在代码里 ThreadLocal 变量可能定义多个，就是同一个线程里存在多个，那么以 ThreadLocal 作为 key 就可以应对这种场景。

## 问题

1. 脏数据
