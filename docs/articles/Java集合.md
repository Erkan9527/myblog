---
title: Java集合
date: 2025-09-21
category: 后端技术
tags: [Java, 集合, 面试]
---
# Java集合
## 集合框架概述
- **定义**: Java 集合框架是一个用于存储和操作数据的容器类库，提供了数据结构和算法的实现。
- **核心接口**:
  - **Collection**: 单一元素的集合，包含 `List`、`Set` 和 `Queue`。
  - **Map**: 键值对的集合。


## Java 集合类型

| **接口**       | **实现类**                          | **特点**                                                                 |
|----------------|-------------------------------------|--------------------------------------------------------------------------|
| **List**       | `ArrayList`, `LinkedList`, `Vector` | 有序、可重复，按插入顺序存储元素。                                       |
| **Set**        | `HashSet`, `LinkedHashSet`, `TreeSet` | 无序（或有序）、不可重复，`TreeSet` 按自然顺序排序。                     |
| **Queue**      | `PriorityQueue`, `LinkedList`       | 先进先出（FIFO），`PriorityQueue` 支持优先级排序。                       |
| **Deque**      | `ArrayDeque`, `LinkedList`          | 双端队列，支持从两端插入和删除元素。                                     |
| **Map**        | `HashMap`, `LinkedHashMap`, `TreeMap`, `Hashtable` | 键值对存储，`TreeMap` 按键排序，`HashMap` 无序，`LinkedHashMap` 有序。 |


## 常用集合类的特点

### **1. ArrayList**
- **特点**: 基于动态数组实现，查询快，增删慢。
- **线程安全**: 否。
- **适用场景**: 频繁查询的场景。
- **示例**:
  ```java
  List<String> list = new ArrayList<>();
  list.add("A");
  list.add("B");
  System.out.println(list); // 输出: [A, B]
  ```
---
#### ArrayList 为什么不安全，如何优化？

- **原因**:
  1. **无同步机制**: `ArrayList` 的方法没有使用 `synchronized` 关键字，多线程环境下可能会导致数据不一致。
  2. **并发修改异常**: 在一个线程遍历 `ArrayList` 的同时，另一个线程修改了它的结构（如添加或删除元素），会抛出 `ConcurrentModificationException`。
  3. **动态扩容问题**: 多线程同时触发扩容时，可能导致数据覆盖或丢失。

- **示例问题**:
  ```java
  List<Integer> list = new ArrayList<>();
  Runnable task = () -> {
      for (int i = 0; i < 1000; i++) {
          list.add(i);
      }
  };
  Thread t1 = new Thread(task);
  Thread t2 = new Thread(task);
  t1.start();
  t2.start();
  t1.join();
  t2.join();
  System.out.println(list.size()); // 输出结果可能小于 2000
  ```
---
#### 如何优化 ArrayList 的线程安全性？
---
#### **方法 1: 使用 `Collections.synchronizedList`**
- **原理**: 将 `ArrayList` 包装成线程安全的集合，所有方法都加上同步锁。
- **示例**:
  ```java
  List<Integer> list = Collections.synchronizedList(new ArrayList<>());
  synchronized (list) {
      list.add(1);
      list.add(2);
  }
  ```

#### **方法 2: 使用 `CopyOnWriteArrayList`**
- **原理**: 写时复制，修改时会创建一个新的数组，避免并发修改问题。
- **优点**:
  - 适合读多写少的场景。
  - 不会抛出 `ConcurrentModificationException`。
- **示例**:
  ```java
  List<Integer> list = new CopyOnWriteArrayList<>();
  list.add(1);
  list.add(2);
  System.out.println(list);
  ```

#### **方法 3: 使用线程安全的集合替代**
- **替代方案**: 如果需要线程安全的集合，可以直接使用 `Vector` 或其他线程安全的集合类。
- **示例**:
  ```java
  List<Integer> list = new Vector<>();
  list.add(1);
  list.add(2);
  System.out.println(list);
  ```
---
###  ArrayList 与 CopyOnWriteArrayList 的对比

| 特性                  | ArrayList                  | CopyOnWriteArrayList          |
|-----------------------|---------------------------|-------------------------------|
| **线程安全**          | 否                        | 是                            |
| **性能**              | 高（无锁机制）            | 较低（写时复制开销较大）      |
| **适用场景**          | 单线程或读写分离的场景     | 多线程且读多写少的场景        |
| **并发修改异常**       | 会抛出 `ConcurrentModificationException` | 不会抛出异常                 |

---

> **总结**: 
- `ArrayList` 是非线程安全的，适合单线程环境。
- 在多线程环境下，可以使用 `Collections.synchronizedList` 或 `CopyOnWriteArrayList` 来保证线程安全。
- 根据场景选择合适的集合类，避免性能问题。

### **2. LinkedList**
- **特点**: 基于双向链表实现，增删快，查询慢。
- **线程安全**: 否。
- **适用场景**: 频繁插入和删除的场景。
- **示例**:
  ```java
  List<String> list = new LinkedList<>();
  list.add("A");
  list.add("B");
  System.out.println(list); // 输出: [A, B]
  ```

### **3. HashSet**
- **特点**: 基于 `HashMap` 实现，元素无序且不可重复。
- **线程安全**: 否。
- **适用场景**: 需要快速去重的场景。
- **示例**:
  ```java
  Set<String> set = new HashSet<>();
  set.add("A");
  set.add("B");
  set.add("A");
  System.out.println(set); // 输出: [A, B]
  ```

### **4. HashMap**
- **特点**: 基于哈希表实现，键值对存储，键不可重复。
- **线程安全**: 否。
- **适用场景**: 快速查找键值对。
- **示例**:
  ```java
  Map<String, Integer> map = new HashMap<>();
  map.put("A", 1);
  map.put("B", 2);
  System.out.println(map); // 输出: {A=1, B=2}
  ```


## 集合框架的线程安全实现

| **集合类型** | **非线程安全类** | **线程安全类**        | **说明**                                   |
|--------------|------------------|-----------------------|------------------------------------------|
| **List**     | `ArrayList`      | `Vector`, `CopyOnWriteArrayList` | `Vector` 使用同步方法，`CopyOnWriteArrayList` 使用写时复制。 |
| **Set**      | `HashSet`        | `CopyOnWriteArraySet` | `CopyOnWriteArraySet` 是线程安全的。       |
| **Map**      | `HashMap`        | `Hashtable`, `ConcurrentHashMap` | `ConcurrentHashMap` 支持高并发。          |
## HashMap 的实现

### **1. 数据结构**
- **底层实现**: 基于 **数组 + 链表 + 红黑树** 的数据结构。
  - **数组**: 存储键值对的主要结构。
  - **链表**: 解决哈希冲突，当多个键的哈希值相同时，存储在链表中。
  - **红黑树**: 当链表长度超过 8 时，链表会转换为红黑树，提高查询效率。

### **2. 工作原理**
1. **存储过程**:
   - 通过键的 `hashCode()` 方法计算哈希值。
   - 使用哈希值对数组长度取模，确定存储位置（索引）。
   - 如果该位置已有元素，使用链表或红黑树解决冲突。
2. **扩容机制**:
   - 当数组的使用率超过 **负载因子（默认 0.75）** 时，触发扩容。
   - 扩容时，数组长度翻倍，并重新计算每个元素的存储位置。
3. **线程安全性**:
   - **非线程安全**: 多线程环境下可能出现数据覆盖或丢失问题。
   - **解决方案**: 使用 `ConcurrentHashMap` 替代。

### **3. 示例代码**
```java
Map<String, Integer> map = new HashMap<>();
map.put("A", 1);
map.put("B", 2);
System.out.println(map.get("A")); // 输出: 1
```

## TreeMap 的实现

### **1. 数据结构**
- **底层实现**: 基于 **红黑树**。
  - 红黑树是一种自平衡二叉搜索树，保证插入、删除、查找的时间复杂度为 **O(log n)**。
  - 节点按照键的自然顺序（或自定义比较器）排序。

### **2. 工作原理**
1. **存储过程**:
   - 插入元素时，根据键的顺序插入到红黑树的正确位置。
   - 如果键重复，则覆盖旧值。
2. **排序特性**:
   - 默认按键的自然顺序排序（键必须实现 `Comparable` 接口）。
   - 可以通过自定义比较器（`Comparator`）实现自定义排序。
3. **线程安全性**:
   - **非线程安全**: 多线程环境下需要手动同步。
   - **解决方案**: 使用 `Collections.synchronizedSortedMap()` 包装。

### **3. 示例代码**
```java
Map<String, Integer> map = new TreeMap<>();
map.put("B", 2);
map.put("A", 1);
map.put("C", 3);
System.out.println(map); // 输出: {A=1, B=2, C=3}（按键排序）
```


## HashMap 与 TreeMap 的对比

| 特性               | HashMap                          | TreeMap                          |
|--------------------|-----------------------------------|-----------------------------------|
| **底层数据结构**    | 数组 + 链表 + 红黑树             | 红黑树                           |
| **键值对存储**      | 无序                             | 按键排序（自然顺序或自定义顺序） |
| **时间复杂度**      | O(1)（哈希冲突时退化为 O(n)）     | O(log n)                         |
| **线程安全**        | 否                               | 否                               |
| **适用场景**        | 快速查找，无需排序               | 需要有序存储的场景               |


> **总结**: 
- **HashMap** 适合快速查找和存储键值对，无需排序的场景。
- **TreeMap** 适合需要按键排序的场景，但性能略低于 HashMap。

## ConcurrentHashMap 的实现及内部结构

### **1. 数据结构**
- **底层实现**: 基于 **数组 + 链表 + 红黑树** 的数据结构。
  - **数组**: 存储 `Node` 对象，作为哈希桶。
  - **链表**: 解决哈希冲突，当多个键的哈希值相同时，存储在链表中。
  - **红黑树**: 当链表长度超过 8 时，链表会转换为红黑树，提高查询效率。

---

### **2. 工作原理**
1. **分段锁机制（JDK 1.7）**:
   - 将整个哈希表分成多个段（`Segment`），每个段维护一部分数据。
   - 线程操作不同段的数据时不会互相影响，从而提高并发性能。

2. **CAS + Synchronized（JDK 1.8）**:
   - JDK 1.8 后取消了分段锁，改为使用 **CAS（Compare-And-Swap）** 和 **synchronized** 实现线程安全。
   - 插入数据时，先通过 CAS 尝试更新节点，失败时使用 `synchronized` 锁定桶。

3. **扩容机制**:
   - 当负载因子超过阈值时，触发扩容。
   - 扩容时，数组长度翻倍，并重新计算每个元素的存储位置。
   - 使用分段迁移，避免一次性扩容导致性能下降。

---

### **3. 特性**
- **线程安全**: 通过 CAS 和 `synchronized` 保证线程安全。
- **高并发**: 支持多线程同时读写，性能优于 `Hashtable`。
- **无锁读操作**: 读操作不需要加锁，性能更高。
- **分段扩容**: 扩容时只迁移部分数据，避免阻塞。

---

### **4. 示例代码**
```java
import java.util.concurrent.ConcurrentHashMap;

public class ConcurrentHashMapExample {
    public static void main(String[] args) {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        map.put("A", 1);
        map.put("B", 2);
        map.put("C", 3);

        System.out.println(map.get("A")); // 输出: 1

        map.forEach((key, value) -> System.out.println(key + ": " + value));
    }
}
```

---

### **5. ConcurrentHashMap 与 HashMap 的对比**

| 特性               | HashMap                          | ConcurrentHashMap               |
|--------------------|-----------------------------------|----------------------------------|
| **线程安全**        | 否                               | 是                              |
| **性能**           | 高（单线程）                     | 高（多线程）                    |
| **锁机制**         | 无                               | CAS + Synchronized              |
| **适用场景**        | 单线程环境                       | 多线程环境                      |

---

### **6. ConcurrentHashMap 与 Hashtable 的对比**

| 特性               | ConcurrentHashMap               | Hashtable                       |
|--------------------|----------------------------------|----------------------------------|
| **线程安全**        | 是                              | 是                              |
| **锁粒度**         | 锁住部分桶                      | 锁住整个表                      |
| **性能**           | 高                              | 低                              |
| **适用场景**        | 高并发读写                      | 低并发场景                      |

---

> **总结**: 
- `ConcurrentHashMap` 是多线程环境下的首选集合，使用 CAS 和分段锁机制保证线程安全和高性能。
- 相较于 `HashMap` 和 `Hashtable`，`ConcurrentHashMap` 在并发场景下性能更优。