---
title: 并发编程
---
# 并发编程

## 解释下乐观锁和悲观锁的区别

### **1. 定义**
- **乐观锁**:
  - 假设并发冲突很少发生，因此不加锁，而是在提交数据时检查是否有冲突。
  - 如果检测到冲突，则回滚操作并重试。
- **悲观锁**:
  - 假设并发冲突经常发生，因此在操作数据之前加锁，防止其他线程修改数据。

---

### **2. 实现方式**
- **乐观锁**:
  - 通过 **版本号机制** 或 **CAS（Compare-And-Swap）** 实现。
  - **版本号机制**:
    - 在数据库中添加一个版本号字段，每次更新数据时检查版本号是否与当前一致。
    - 示例：
      ```sql
      UPDATE table_name
      SET value = new_value, version = version + 1
      WHERE id = ? AND version = current_version;
      ```
  - **CAS（Compare-And-Swap）**:
    - 一种无锁机制，通过比较和交换操作实现。
    - 示例：Java 中的 `AtomicInteger`。
      ```java
      AtomicInteger atomicInteger = new AtomicInteger(0);
      atomicInteger.compareAndSet(0, 1); // 如果当前值是 0，则更新为 1
      ```

- **悲观锁**:
  - 通过 **数据库的锁机制** 或 **同步代码块** 实现。
  - **数据库锁机制**:
    - 使用 `SELECT ... FOR UPDATE` 对查询的数据加行锁，防止其他事务修改。
    - 示例：
      ```sql
      SELECT * FROM table_name WHERE id = ? FOR UPDATE;
      ```
  - **同步代码块**:
    - 在代码中使用 `synchronized` 或 `ReentrantLock` 加锁。
    - 示例：
      ```java
      synchronized (this) {
          // 临界区代码
      }
      ```

---

### **3. 优缺点对比**

| 特性               | 乐观锁                           | 悲观锁                           |
|--------------------|----------------------------------|----------------------------------|
| **加锁机制**        | 无锁，通过版本号或 CAS 实现       | 加锁，通过数据库锁或同步机制实现 |
| **适用场景**        | 并发冲突较少的场景               | 并发冲突较多的场景               |
| **性能**           | 性能较高，避免了锁的开销          | 性能较低，存在锁竞争             |
| **冲突处理**        | 通过重试机制解决                 | 通过阻塞机制解决                 |
| **死锁风险**        | 无                               | 存在死锁风险                     |

---

### **4. 适用场景**
- **乐观锁**:
  - 适用于读多写少的场景，例如配置管理系统。
  - 适合高并发场景，避免锁竞争。
- **悲观锁**:
  - 适用于写操作频繁、冲突较多的场景，例如银行转账系统。
  - 需要确保数据一致性时使用。

---

### **总结**
- **乐观锁**: 通过无锁机制提高性能，但需要处理冲突重试，适合冲突较少的场景。
- **悲观锁**: 通过加锁保证数据一致性，但性能较低，适合冲突较多的场景。
## 详细说说 CAS 是什么？

### **1. 定义**
- **CAS（Compare-And-Swap）** 是一种无锁并发编程技术，中文称为“比较并交换”。
- 它是一种原子操作，用于在多线程环境下实现变量的安全更新。

### **2. 工作原理**
- CAS 操作包含三个参数：
  1. **内存值（V）**: 当前变量的值。
  2. **预期值（E）**: 线程期望变量的值。
  3. **新值（N）**: 需要更新的值。
- 当且仅当内存值（V）与预期值（E）相等时，将内存值更新为新值（N）；否则，更新失败，线程可以选择重试。

### **3. 示例**
- **Java 中的 CAS 示例**:
  ```java
  import java.util.concurrent.atomic.AtomicInteger;

  public class CASExample {
      public static void main(String[] args) {
          AtomicInteger atomicInteger = new AtomicInteger(0);

          // 如果当前值是 0，则更新为 1
          boolean success = atomicInteger.compareAndSet(0, 1);
          System.out.println("Update success: " + success); // 输出: true
          System.out.println("Current value: " + atomicInteger.get()); // 输出: 1
      }
  }
  ```

### **4. 优缺点**
| 特性               | 优点                             | 缺点                             |
|--------------------|----------------------------------|----------------------------------|
| **优点**           | 无锁机制，性能高，避免线程阻塞    | 存在 ABA 问题                   |
| **缺点**           | 实现复杂，可能导致自旋消耗 CPU    | 需要配合版本号解决 ABA 问题      |

### **5. ABA 问题**
- **定义**: 如果变量从值 A 变为 B，又变回 A，CAS 无法检测到这种变化。
- **解决方法**:
  - 使用 **版本号机制**，每次更新时增加版本号。
  - 示例：`AtomicStampedReference`。

---

## 详细说说雪花算法和美团的 Leaf，两个都进行详细比对

### **1. 雪花算法（Snowflake）**
- **定义**: Twitter 提出的分布式唯一 ID 生成算法。
- **结构**:
  - 64 位二进制组成：
    - **1 位**: 符号位，始终为 0。
    - **41 位**: 时间戳，表示毫秒级时间，支持约 69 年。
    - **10 位**: 工作机器 ID，支持最多 1024 个节点。
    - **12 位**: 序列号，支持每毫秒生成 4096 个 ID。
- **优点**:
  - 高性能：本地生成，无需网络请求。
  - 有序性：基于时间戳生成，ID 大致有序。
- **缺点**:
  - 时钟回拨问题：如果服务器时间回拨，可能导致 ID 冲突。

### **2. 美团的 Leaf**
- **定义**: 美团开源的分布式唯一 ID 生成服务。
- **实现方式**:
  - **号段模式（Segment Mode）**:
    - 从数据库中批量获取一段 ID，缓存在内存中。
    - 优点：性能高，减少数据库压力。
    - 缺点：依赖数据库，存在单点故障风险。
  - **Snowflake 模式**:
    - 基于雪花算法生成 ID。
- **优点**:
  - 提供两种模式，灵活选择。
  - 号段模式适合高并发场景。
- **缺点**:
  - 部署复杂，需要维护数据库。

### **3. 对比分析**

| 特性               | 雪花算法（Snowflake）            | 美团 Leaf                       |
|--------------------|----------------------------------|----------------------------------|
| **生成方式**        | 本地生成                        | 号段模式 + Snowflake 模式       |
| **性能**           | 高                              | 号段模式性能更高               |
| **依赖性**         | 无需依赖外部服务                | 号段模式依赖数据库             |
| **时钟回拨问题**    | 存在                            | Snowflake 模式存在，号段模式无  |
| **适用场景**        | 分布式系统，ID 有序性要求高      | 高并发场景，灵活性要求高        |

---

### **总结**
- **CAS**: 是一种无锁机制，适合高并发场景，但需要注意 ABA 问题。
- **雪花算法**: 高性能、低依赖，适合分布式系统，但需解决时钟回拨问题。
- **美团 Leaf**: 提供号段模式和 Snowflake 模式，适合高并发和灵活性要求高的场景。
## 线程声明使用有多少种方法？

1. **继承 `Thread` 类**:
   - 通过继承 `Thread` 类并重写 `run()` 方法。
   - 示例：
     ```java
     class MyThread extends Thread {
         @Override
         public void run() {
             System.out.println("Thread is running...");
         }
     }

     public class Main {
         public static void main(String[] args) {
             MyThread thread = new MyThread();
             thread.start();
         }
     }
     ```

2. **实现 `Runnable` 接口**:
   - 通过实现 `Runnable` 接口并重写 `run()` 方法。
   - 示例：
     ```java
     class MyRunnable implements Runnable {
         @Override
         public void run() {
             System.out.println("Thread is running...");
         }
     }

     public class Main {
         public static void main(String[] args) {
             Thread thread = new Thread(new MyRunnable());
             thread.start();
         }
     }
     ```

3. **使用 `Callable` 和 `Future`**:
   - 通过实现 `Callable` 接口，支持返回值和异常处理。
   - 示例：
     ```java
     import java.util.concurrent.*;

     class MyCallable implements Callable<String> {
         @Override
         public String call() throws Exception {
             return "Thread is running...";
         }
     }

     public class Main {
         public static void main(String[] args) throws Exception {
             ExecutorService executor = Executors.newSingleThreadExecutor();
             Future<String> future = executor.submit(new MyCallable());
             System.out.println(future.get());
             executor.shutdown();
         }
     }
     ```

4. **使用线程池**:
   - 通过线程池创建和管理线程。
   - 示例：
     ```java
     ExecutorService executor = Executors.newFixedThreadPool(2);
     executor.execute(() -> System.out.println("Thread is running..."));
     executor.shutdown();
     ```



## 线程池怎么创建，线程池数怎么安排？

1. **线程池的创建方式**:
   - 使用 `Executors` 工具类创建线程池：
     - **`newFixedThreadPool`**: 固定大小线程池。
     - **`newCachedThreadPool`**: 可缓存线程池，线程数动态调整。
     - **`newSingleThreadExecutor`**: 单线程池。
     - **`newScheduledThreadPool`**: 定时任务线程池。
   - 示例：
     ```java
     ExecutorService executor = Executors.newFixedThreadPool(5);
     executor.execute(() -> System.out.println("Task executed"));
     executor.shutdown();
     ```

2. **线程池数的安排**:
   - **CPU 密集型任务**:
     - 线程数 = CPU 核心数 + 1。
     - 示例：计算密集型任务，如科学计算。
   - **IO 密集型任务**:
     - 线程数 = CPU 核心数 × 2。
     - 示例：文件读写、网络请求。
   - **混合型任务**:
     - 根据任务的 CPU 和 IO 比例动态调整线程数。

3. **自定义线程池**:
   - 使用 `ThreadPoolExecutor` 自定义线程池。
   - 示例：
     ```java
     ThreadPoolExecutor executor = new ThreadPoolExecutor(
         2, 4, 60, TimeUnit.SECONDS,
         new LinkedBlockingQueue<>(10)
     );
     executor.execute(() -> System.out.println("Task executed"));
     executor.shutdown();
     ```
## 线程池的参数怎么设置？

线程池的参数可以通过 `ThreadPoolExecutor` 构造函数进行设置，以下是各参数的详细说明：

### **1. 线程池参数说明**
- **`corePoolSize`（核心线程数）**:
  - 核心线程数是线程池中始终保持存活的线程数量，即使线程处于空闲状态也不会被销毁。
  - **设置建议**:
    - CPU 密集型任务：设置为 CPU 核心数。
    - IO 密集型任务：设置为 CPU 核心数的 2 倍或更多。

- **`maximumPoolSize`（最大线程数）**:
  - 线程池中允许的最大线程数量，当任务队列满时，会创建非核心线程来处理任务。
  - **设置建议**:
    - 根据系统资源和任务类型合理设置，避免过多线程导致资源竞争。

- **`keepAliveTime`（线程存活时间）**:
  - 非核心线程在空闲状态下的存活时间，超过该时间后会被销毁。
  - **设置建议**:
    - 对于短期任务，可以设置较短时间。
    - 对于长期任务，可以设置较长时间。

- **`unit`（时间单位）**:
  - `keepAliveTime` 的时间单位，如 `TimeUnit.SECONDS`。

- **`workQueue`（任务队列）**:
  - 用于存放等待执行的任务。
  - 常见队列类型：
    - **`ArrayBlockingQueue`**: 有界队列，防止任务过多导致内存溢出。
    - **`LinkedBlockingQueue`**: 无界队列，适合任务量较大的场景。
    - **`SynchronousQueue`**: 不存储任务，直接将任务交给线程处理。

- **`threadFactory`（线程工厂）**:
  - 用于创建线程的工厂，可以自定义线程名称、优先级等。

- **`handler`（拒绝策略）**:
  - 当任务队列满且线程数达到最大值时，线程池会执行拒绝策略。
  - 常见拒绝策略：
    - **`AbortPolicy`**: 抛出异常（默认策略）。
    - **`CallerRunsPolicy`**: 由调用线程执行任务。
    - **`DiscardPolicy`**: 丢弃任务，不抛异常。
    - **`DiscardOldestPolicy`**: 丢弃队列中最旧的任务。

---

### **2. 示例代码**
```java
import java.util.concurrent.*;

public class ThreadPoolExample {
    public static void main(String[] args) {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, // 核心线程数
            4, // 最大线程数
            60, // 空闲线程存活时间
            TimeUnit.SECONDS, // 时间单位
            new LinkedBlockingQueue<>(10), // 任务队列
            Executors.defaultThreadFactory(), // 线程工厂
            new ThreadPoolExecutor.AbortPolicy() // 拒绝策略
        );

        for (int i = 0; i < 20; i++) {
            executor.execute(() -> {
                System.out.println(Thread.currentThread().getName() + " is executing a task");
            });
        }

        executor.shutdown();
    }
}
```

---

### **3. 参数设置建议**
1. **根据任务类型设置线程数**:
   - **CPU 密集型任务**:
     - 线程数 = CPU 核心数 + 1。
     - 示例：科学计算、图像处理。
   - **IO 密集型任务**:
     - 线程数 = CPU 核心数 × 2。
     - 示例：文件读写、网络请求。
   - **混合型任务**:
     - 根据任务的 CPU 和 IO 比例动态调整线程数。

2. **选择合适的任务队列**:
   - **`ArrayBlockingQueue`**: 适合任务量有限的场景。
   - **`LinkedBlockingQueue`**: 适合任务量较大的场景。
   - **`SynchronousQueue`**: 适合高并发、低延迟的场景。

3. **合理设置拒绝策略**:
   - **`AbortPolicy`**: 默认策略，适合对任务丢失敏感的场景。
   - **`CallerRunsPolicy`**: 适合任务量较小的场景。
   - **`DiscardPolicy`**: 适合对任务丢失不敏感的场景。
   - **`DiscardOldestPolicy`**: 适合优先处理新任务的场景。

---

### **总结**
- **线程池参数设置**: 根据任务类型和系统资源合理配置核心线程数、最大线程数、任务队列和拒绝策略。
- **线程池的优点**: 提高资源利用率，避免频繁创建和销毁线程。
- **注意事项**: 避免线程数设置过多导致资源竞争，合理选择任务队列和拒绝策略。


## 锁都有什么类型？每个锁的特点是什么？

1. **`synchronized` 锁**:
   - **特点**: 内置锁，简单易用，锁住代码块或方法。
   - **优点**: 自动释放锁，避免死锁。
   - **缺点**: 粒度较大，性能较低。

2. **`ReentrantLock`（可重入锁）**:
   - **特点**: 显式锁，支持公平锁和非公平锁。
   - **优点**: 提供更灵活的锁机制，支持中断和超时。
   - **缺点**: 需要手动释放锁，使用复杂。
   - 示例：
     ```java
     ReentrantLock lock = new ReentrantLock();
     lock.lock();
     try {
         // 临界区代码
     } finally {
         lock.unlock();
     }
     ```

3. **读写锁（`ReentrantReadWriteLock`）**:
   - **特点**: 读写分离，读操作可并发，写操作互斥。
   - **优点**: 提高读多写少场景的性能。
   - **缺点**: 写操作会阻塞读操作。

4. **`StampedLock`**:
   - **特点**: 提供乐观读锁，进一步提高读性能。
   - **优点**: 适合读多写少的场景。
   - **缺点**: 使用复杂，不支持重入。

5. **`CountDownLatch`**:
   - **特点**: 用于线程等待，倒计数为 0 时释放所有等待线程。
   - **优点**: 适合一次性事件的同步。
   - **缺点**: 不可重用。

6. **`CyclicBarrier`**:
   - **特点**: 用于线程集合点，所有线程到达屏障后继续执行。
   - **优点**: 可重用，适合多线程分阶段任务。
   - **缺点**: 使用复杂。

7. **`Semaphore`（信号量）**:
   - **特点**: 控制线程并发数量。
   - **优点**: 适合限流场景。
   - **缺点**: 需要手动释放许可。

8. **`LockSupport`**:
   - **特点**: 提供线程阻塞和唤醒功能。
   - **优点**: 灵活控制线程状态。
   - **缺点**: 使用复杂，需手动管理。

---

### **总结**
- **线程声明**: 提供了多种方式，推荐使用线程池管理线程。
- **线程池**: 根据任务类型选择合适的线程池，CPU 密集型任务使用固定线程池，IO 密集型任务使用缓存线程池。
- **锁类型**: 根据场景选择合适的锁，`synchronized` 简单易用，`ReentrantLock` 灵活强大，`StampedLock` 适合读多写少场景。