---
title: Java基础
date: 2025-09-21
category: 后端技术
tags: [Java, 基础, 面试]
---

# Java 核心基础

## 🚀 Java 语言特性

### ✅ 核心优势

- **跨平台性**: "一次编译，到处运行" (WORA - Write Once, Run Anywhere)
- **内存管理**: 自动垃圾回收机制，避免内存泄漏
- **面向对象**: 封装、继承、多态三大特性支持
- **生态丰富**: 庞大的开源生态和企业级框架支持
- **稳定可靠**: 成熟的虚拟机技术，适合大型企业应用

### ⚠️ 主要劣势

- **性能开销**: 虚拟机执行，运行速度不如 C++、Rust 等原生语言
- **启动时间**: JVM 预热过程导致冷启动较慢
- **语法冗余**: 模板代码较多，开发效率不如 Python、Go 等语言
- **内存占用**: JVM 本身占用一定内存资源

## 🎯 适用场景

### 最佳实践领域
- **企业级应用**: Spring 生态支撑的大型系统
- **Web 后端服务**: 微服务架构、RESTful API
- **大数据处理**: Hadoop、Spark、Kafka 等框架
- **Android 开发**: 移动应用主流开发语言

### 不太适合的场景
- **系统编程**: 不如 C/C++、Rust 高效
- **机器学习**: 不如 Python 生态丰富
- **高频交易**: 延迟敏感场景不够理想

---

> **小结**: Java 虽然不是最快的语言，但其稳定性、可维护性和庞大的生态系统使其成为企业级开发的首选语言。
## 🛠 JVM、JDK 和 JRE 的关系

- **JVM（Java Virtual Machine）**: Java 虚拟机，负责运行 Java 字节码，提供跨平台能力。
- **JRE（Java Runtime Environment）**: Java 运行环境，包含 JVM 和运行 Java 程序所需的类库。
- **JDK（Java Development Kit）**: Java 开发工具包，包含 JRE 和开发工具（如编译器 `javac`）。

**关系图**:
```
JDK = JRE + 开发工具
JRE = JVM + 类库
```


## 📊 数据类型

### 基本数据类型
- **整数类型**: `byte` (1字节), `short` (2字节), `int` (4字节), `long` (8字节)
- **浮点类型**: `float` (4字节), `double` (8字节)
- **字符类型**: `char` (2字节，支持 Unicode)
- **布尔类型**: `boolean` (1字节，值为 `true` 或 `false`)

### 小数计算（0.1 的表示）
- 浮点数在计算机中以二进制存储，可能导致精度问题。
- 示例：
```java
System.out.println(0.1 + 0.2); // 输出 0.30000000000000004
```
- **解决方法**: 使用 `BigDecimal` 进行精确计算。

### 引用数据类型
- **类（Class）**: 自定义对象类型。
- **接口（Interface）**: 定义行为规范。
- **数组（Array）**: 存储同类型数据的集合。


## 🧩 面向对象

### 三大特性
1. **封装**: 隐藏对象的内部实现，仅暴露必要的接口。
   ```java
   public class Person {
       private String name;
       public String getName() {
           return name;
       }
       public void setName(String name) {
           this.name = name;
       }
   }
   ```
2. **继承**: 子类继承父类的属性和方法，支持代码复用。
   ```java
   public class Animal {
       public void eat() {
           System.out.println("This animal eats food.");
       }
   }
   public class Dog extends Animal {
       public void bark() {
           System.out.println("This dog barks.");
       }
   }
   ```
3. **多态**: 同一方法在不同对象上的表现不同。
   ```java
   public class Animal {
       public void sound() {
           System.out.println("Animal makes a sound");
       }
   }
   public class Dog extends Animal {
       @Override
       public void sound() {
           System.out.println("Dog barks");
       }
   }
   ```

## 🧩 Java 抽象类与接口的区别

1. **定义**:
   - 抽象类：使用 `abstract` 关键字定义，可以包含抽象方法和具体方法。
   - 接口：使用 `interface` 关键字定义，默认所有方法是抽象的（Java 8 后可以有默认方法和静态方法）。

2. **继承与实现**:
   - 抽象类：一个类只能继承一个抽象类。
   - 接口：一个类可以实现多个接口。

3. **使用场景**:
   - 抽象类：适合描述一组有共性但不完全相同的对象。
   - 接口：适合定义行为规范，强调实现的多样性。

**示例**:
```java
abstract class Animal {
    abstract void sound();
    void eat() {
        System.out.println("This animal eats food.");
    }
}

interface Flyable {
    void fly();
}

class Bird extends Animal implements Flyable {
    @Override
    void sound() {
        System.out.println("Bird chirps");
    }
    @Override
    public void fly() {
        System.out.println("Bird flies");
    }
}
```

## 🔄 深拷贝与浅拷贝的区别

1. **定义**:
   - 浅拷贝：只复制对象的引用，原对象和拷贝对象共享同一块内存。
   - 深拷贝：复制对象的所有内容，包括引用对象的内容。

2. **实现方式**:
   - 浅拷贝：使用 `clone()` 方法。
   - 深拷贝：需要手动实现，或者使用序列化。

**示例**:
```java
class Person implements Cloneable {
    String name;
    Address address;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        Person cloned = (Person) super.clone();
        cloned.address = (Address) address.clone(); // 深拷贝
        return cloned;
    }
}
```


## 🔑 final 关键字的使用

1. **修饰类**: 表示类不能被继承。
   ```java
   final class Constants {
       // 常量类
   }
   ```

2. **修饰方法**: 表示方法不能被子类重写。
   ```java
   class Parent {
       final void display() {
           System.out.println("This method cannot be overridden.");
       }
   }
   ```

3. **修饰变量**: 表示变量是常量，值不能被修改。
   ```java
   final int MAX_VALUE = 100;
   ```


## 🔍 反射的核心概念

1. **定义**: 反射是 Java 提供的一种动态机制，允许在运行时获取类的信息并操作类的成员。

2. **常用类**:
   - `Class`: 获取类的字节码信息。
   - `Field`: 操作类的成员变量。
   - `Method`: 调用类的方法。
   - `Constructor`: 调用类的构造方法。

3. **使用场景**:
   - 动态加载类（如 JDBC 驱动）。
   - 框架开发（如 Spring、Hibernate）。
   - 动态代理。

**示例**:
```java
Class<?> clazz = Class.forName("com.example.MyClass");
Object obj = clazz.getDeclaredConstructor().newInstance();
Method method = clazz.getMethod("myMethod");
method.invoke(obj);
```


## 🏷 注解知识

1. **定义**: 注解是 Java 提供的一种元数据，用于为代码添加额外的信息。

2. **常见注解**:
   - `@Override`: 表示方法重写。
   - `@Deprecated`: 表示方法已过时。
   - `@SuppressWarnings`: 忽略编译器警告。

3. **自定义注解**:
   - 使用 `@interface` 定义。
   - 可以通过反射获取注解信息。

**示例**:
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyAnnotation {
    String value();
}

class Test {
    @MyAnnotation(value = "Hello")
    public void myMethod() {
        System.out.println("This is a custom annotation.");
    }
}
```
## 说一下new()的自动回收
- new(): 用于创建对象，分配内存。
- 自动回收: 由 JVM 的垃圾回收机制（GC）负责，回收不再被引用的对象。
注意: 无需手动释放内存，GC 会在内存不足或空闲时自动触发
## ==和equal()的区别,为什么不同?
- ==: 比较的是引用地址是否相同。
- equals(): 比较的是内容是否相同（默认实现仍是地址比较，可被重写）。
## 重写equal()时需要注意什么?
重写 equals() 时，通常也需要重写 hashCode()，确保对象在集合中的行为一致。
```
@Override
public boolean equals(Object obj) {
    if (this == obj) return true;
    if (obj == null || getClass() != obj.getClass()) return false;
    MyClass myClass = (MyClass) obj;
    return id == myClass.id && name.equals(myClass.name);
}
```
## Bean 的生命周期和作用域

### **生命周期**
1. **实例化**: 通过容器创建对象。
2. **属性赋值**: 注入依赖。
3. **初始化**: 调用 `@PostConstruct` 或 `init-method`。
4. **使用**: Bean 被容器管理并提供服务。
5. **销毁**: 调用 `@PreDestroy` 或 `destroy-method`。

### **作用域**
1. **`singleton`**: 全局单例（默认），容器中只存在一个实例。
2. **`prototype`**: 每次请求都会创建一个新的实例。
3. **`request`**: 每个 HTTP 请求创建一个实例（Web 环境）。
4. **`session`**: 每个会话创建一个实例（Web 环境）。


> **总结**: Bean 的生命周期由容器管理，作用域决定了 Bean 的实例化策略。合理选择作用域可以优化资源

## 一个对象从一个Jvm中转移到另一个jvm中,可以有几种方法?
- 序列化与反序列化: 使用 ObjectOutputStream 和 ObjectInputStream。
- 远程调用（RMI）: Java 的远程方法调用机制。
- 网络传输: 通过 Socket 或 HTTP 传输对象数据。
- 消息队列: 使用 Kafka、RabbitMQ 等传递对象。
## AOP 的常用术语及其含义

### **什么是 AOP？**
- **AOP（Aspect-Oriented Programming）**: 面向切面编程，用于分离横切关注点（如日志、事务管理），提高代码的可维护性和复用性。

### **常用术语**
1. **切面（Aspect）**: 横切关注点的模块化实现，例如日志记录、权限校验。
2. **连接点（Join Point）**: 程序执行的某个点，例如方法调用、异常抛出。
3. **切入点（Pointcut）**: 定义在哪些连接点应用切面逻辑。
4. **通知（Advice）**: 切面逻辑的具体实现，例如前置通知、后置通知、异常通知。
5. **目标对象（Target Object）**: 被切面增强的对象。
6. **代理（Proxy）**: AOP 创建的动态代理对象，用于增强目标对象。
7. **织入（Weaving）**: 将切面逻辑应用到目标对象的过程。

### **AOP 的通知类型**
- **前置通知（Before Advice）**: 在方法执行前执行。
- **后置通知（After Advice）**: 在方法执行后执行。
- **返回通知（After Returning Advice）**: 在方法成功返回后执行。
- **异常通知（After Throwing Advice）**: 在方法抛出异常后执行。
- **环绕通知（Around Advice）**: 包围目标方法的执行，控制方法的执行时机。

### **示例代码**
```java
@Aspect
@Component
public class LoggingAspect {

    // 定义切入点
    @Pointcut("execution(* com.example.service.*.*(..))")
    public void serviceMethods() {}

    // 前置通知
    @Before("serviceMethods()")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Before method: " + joinPoint.getSignature().getName());
    }

    // 后置通知
    @After("serviceMethods()")
    public void logAfter(JoinPoint joinPoint) {
        System.out.println("After method: " + joinPoint.getSignature().getName());
    }

    // 环绕通知
    @Around("serviceMethods()")
    public Object logAround(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("Before proceeding: " + joinPoint.getSignature().getName());
        Object result = joinPoint.proceed();
        System.out.println("After proceeding: " + joinPoint.getSignature().getName());
        return result;
    }
}
```
### 如何区分 AOP 动态代理

在 AOP 中，动态代理是实现切面逻辑的核心机制。Java 提供了两种主要的动态代理方式：**JDK 动态代理** 和 **CGLIB 动态代理**。以下是两者的区别和适用场景：

---

#### **1. JDK 动态代理**
- **原理**: 基于 Java 的反射机制，代理类必须实现一个或多个接口。
- **实现方式**: 使用 `java.lang.reflect.Proxy` 和 `InvocationHandler`。
- **特点**:
  - 只能代理实现了接口的类。
  - 代理类在运行时动态生成。
  - 性能较高，但由于依赖接口，灵活性较低。
- **示例代码**:
  ```java
  import java.lang.reflect.InvocationHandler;
  import java.lang.reflect.Method;
  import java.lang.reflect.Proxy;

  interface Service {
      void perform();
  }

  class RealService implements Service {
      @Override
      public void perform() {
          System.out.println("Executing real service...");
      }
  }

  class ServiceProxy implements InvocationHandler {
      private Object target;

      public ServiceProxy(Object target) {
          this.target = target;
      }

      @Override
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
          System.out.println("Before method execution...");
          Object result = method.invoke(target, args);
          System.out.println("After method execution...");
          return result;
      }
  }

  public class JDKProxyExample {
      public static void main(String[] args) {
          Service realService = new RealService();
          Service proxy = (Service) Proxy.newProxyInstance(
              realService.getClass().getClassLoader(),
              realService.getClass().getInterfaces(),
              new ServiceProxy(realService)
          );
          proxy.perform();
      }
  }
  ```

---

#### **2. CGLIB 动态代理**
- **原理**: 基于字节码生成技术，代理类是目标类的子类。
- **实现方式**: 使用 `net.sf.cglib.proxy.Enhancer`。
- **特点**:
  - 可以代理没有实现接口的类。
  - 通过继承目标类生成代理类。
  - 性能较低于 JDK 动态代理，但灵活性更高。
- **示例代码**:
  ```java
  import net.sf.cglib.proxy.Enhancer;
  import net.sf.cglib.proxy.MethodInterceptor;
  import net.sf.cglib.proxy.MethodProxy;

  class RealService {
      public void perform() {
          System.out.println("Executing real service...");
      }
  }

  class ServiceInterceptor implements MethodInterceptor {
      @Override
      public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
          System.out.println("Before method execution...");
          Object result = proxy.invokeSuper(obj, args);
          System.out.println("After method execution...");
          return result;
      }
  }

  public class CGLIBProxyExample {
      public static void main(String[] args) {
          Enhancer enhancer = new Enhancer();
          enhancer.setSuperclass(RealService.class);
          enhancer.setCallback(new ServiceInterceptor());

          RealService proxy = (RealService) enhancer.create();
          proxy.perform();
      }
  }
  ```

---

### **3. JDK 动态代理与 CGLIB 动态代理的对比**

| 特性               | JDK 动态代理                     | CGLIB 动态代理                  |
|--------------------|-----------------------------------|----------------------------------|
| **代理对象**        | 必须实现接口                     | 不需要实现接口                  |
| **实现方式**        | 基于反射机制                     | 基于字节码生成                  |
| **性能**           | 较高（接口调用开销小）            | 较低（字节码生成开销大）         |
| **适用场景**        | 目标类实现了接口                 | 目标类没有实现接口              |
| **依赖库**          | 无需额外依赖                     | 需要引入 CGLIB 库               |

---

### **4. Spring AOP 的代理选择**
- **默认策略**:
  - 如果目标类实现了接口，Spring 默认使用 **JDK 动态代理**。
  - 如果目标类没有实现接口，Spring 使用 **CGLIB 动态代理**。
- **强制使用 CGLIB**:
  - 可以通过配置 `@EnableAspectJAutoProxy(proxyTargetClass = true)` 强制使用 CGLIB 动态代理。

---

> **总结**: 
- JDK 动态代理适用于目标类实现了接口的场景，性能较高。
- CGLIB 动态代理适用于目标类没有实现接口的场景，灵活性更强。
- 在实际开发中，根据目标类的特性和需求选择合适的代理方式。



### **AOP 的优点**
1. **解耦横切关注点**: 将日志、事务等逻辑从业务代码中分离。
2. **提高代码复用性**: 通用逻辑可以在多个模块中复用。
3. **增强代码可维护性**: 业务逻辑与横切逻辑分离，便于维护。

> **总结**: AOP 是一种强大的编程范式，能够帮助开发者更优雅地处理横切关注点，提升代码的可读性和可维
## String、StringBuffer 和 StringBuilder 的区别

| 特性               | String                | StringBuffer           | StringBuilder          |
|--------------------|-----------------------|------------------------|------------------------|
| **可变性**         | 不可变                | 可变                   | 可变                   |
| **线程安全**       | 是（不可变对象本质上线程安全） | 是（方法使用 `synchronized`） | 否                    |
| **性能**           | 较低                  | 较低（线程安全开销）    | 较高（无线程安全开销）  |
| **适用场景**       | 少量字符串操作         | 多线程环境下的字符串操作 | 单线程环境下的字符串操作 |
## 创建对象有几种方法?

在 Java 中，创建对象的方式有多种，以下是常见的几种方法：

### **1. 使用 `new` 关键字**
- **描述**: 最常见的方式，直接调用类的构造方法创建对象。
- **示例**:
  ```java
  MyClass obj = new MyClass();
  ```

---

### **2. 使用反射**
- **描述**: 通过 `Class` 类的 `newInstance()` 方法或 `Constructor` 类的 `newInstance()` 方法创建对象。
- **示例**:
  ```java
  // 使用 Class 的 newInstance()
  MyClass obj = MyClass.class.newInstance();

  // 使用 Constructor 的 newInstance()
  Constructor<MyClass> constructor = MyClass.class.getConstructor();
  MyClass obj2 = constructor.newInstance();
  ```

---

### **3. 使用克隆（Clone）**
- **描述**: 通过实现 `Cloneable` 接口并重写 `clone()` 方法创建对象。
- **示例**:
  ```java
  class MyClass implements Cloneable {
      @Override
      protected Object clone() throws CloneNotSupportedException {
          return super.clone();
      }
  }

  MyClass obj1 = new MyClass();
  MyClass obj2 = (MyClass) obj1.clone();
  ```

---

### **4. 使用序列化与反序列化**
- **描述**: 通过将对象序列化到文件中，再反序列化回内存中创建对象。
- **示例**:
  ```java
  // 序列化
  ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("object.dat"));
  out.writeObject(obj);

  // 反序列化
  ObjectInputStream in = new ObjectInputStream(new FileInputStream("object.dat"));
  MyClass obj2 = (MyClass) in.readObject();
  ```

---

### **5. 使用工厂方法**
- **描述**: 通过工厂方法模式，由工厂类创建对象。
- **示例**:
  ```java
  class MyClassFactory {
      public static MyClass createInstance() {
          return new MyClass();
      }
  }

  MyClass obj = MyClassFactory.createInstance();
  ```

---

### **6. 使用动态代理**
- **描述**: 通过动态代理生成目标对象的代理实例。
- **示例**:
  ```java
  MyInterface proxy = (MyInterface) Proxy.newProxyInstance(
      MyInterface.class.getClassLoader(),
      new Class<?>[]{MyInterface.class},
      (proxyObj, method, args) -> {
          System.out.println("Before method execution");
          return method.invoke(new MyClass(), args);
      }
  );
  ```

---

### **7. 使用 `Unsafe` 类**
- **描述**: 通过 `sun.misc.Unsafe` 类的 `allocateInstance()` 方法创建对象，绕过构造方法。
- **示例**:
  ```java
  import sun.misc.Unsafe;

  Unsafe unsafe = Unsafe.getUnsafe();
  MyClass obj = (MyClass) unsafe.allocateInstance(MyClass.class);
  ```

---

### **8. 使用 `Enum`**
- **描述**: 通过枚举类创建单例对象。
- **示例**:
  ```java
  enum Singleton {
      INSTANCE;
  }

  Singleton obj = Singleton.INSTANCE;
  ```

---

### **总结**

| 方法                  | 是否调用构造方法 | 是否常用 | 适用场景                          |
|-----------------------|------------------|----------|-----------------------------------|
| `new` 关键字          | 是               | 是       | 常规对象创建                     |
| 反射                  | 是               | 否       | 动态加载类                       |
| 克隆                  | 否               | 否       | 创建对象副本                     |
| 序列化与反序列化      | 否               | 否       | 对象持久化与恢复                 |
| 工厂方法              | 是               | 是       | 解耦对象创建逻辑                 |
| 动态代理              | 否               | 否       | AOP 或代理模式                   |
| `Unsafe` 类           | 否               | 否       | 特殊场景（如框架底层实现）        |
| 枚举                  | 否               | 是       | 单例模式                         |

> **注意**: 除了 `new` 关键字，其他方式通常用于特定场景，需根据实际需求选择合适的方式。