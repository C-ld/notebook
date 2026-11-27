
局部变量：可以在用之前，声明之后单独初始化。

> Java对一切类型都是值传递，只不过引用类型是传递的引用的值。而你修改引用值的副本，不影响原引用的指向。


![[Pasted image 20260922170118.png]]
### 访问控制和继承

请注意以下方法继承的规则：

- 父类中声明为 public 的方法在子类中也必须为 public。
    
- 父类中声明为 protected 的方法在子类中要么声明为 protected，要么声明为 public，不能声明为 private。
    
- 父类中声明为 private 的方法，不能够被子类继承。


## 非访问修饰符

为了实现一些其他的功能，Java 也提供了许多非访问修饰符。
static 修饰符，用来修饰类方法和类变量。
final 修饰符，用来修饰类、方法和变量，final 修饰的类不能够被继承，修饰的方法不能被继承类重新定义，修饰的变量为常量，是不可修改的。
abstract 修饰符，用来创建抽象类和抽象方法。
synchronized 和 volatile 修饰符，主要用于线程的编程。


### final 修饰符
**final 变量：**
final 表示"最后的、最终的"含义，变量一旦赋值后，不能被重新赋值。被 final 修饰的实例变量必须显式指定初始值。
final 修饰符通常和 static 修饰符一起使用来创建类常量。

**final 方法**
父类中的 final 方法可以被子类继承，但是不能被子类重写。
声明 final 方法的主要目的是防止该方法的内容被修改。
如下所示，使用 final 修饰符声明方法。

**final 类**
final 类不能被继承，没有类能够继承 final 类的任何特性。

### abstract 修饰符

**抽象类：**
抽象类**不能用来实例化对象**，声明抽象类的唯一目的是为了将来对该类进行扩充。
一个类不能同时被 abstract 和 final 修饰。如果一个类包含抽象方法，那么该类一定要声明为抽象类，否则将出现编译错误。
抽象类可以包含抽象方法和非抽象方法。

**抽象方法：**
抽象方法是一种没有任何实现的方法，该方法的具体实现由子类提供。
抽象方法不能被声明成 final 和 static。
任何继承抽象类的子类必须实现父类的所有抽象方法，除非该子类也是抽象类。
如果一个类包含若干个抽象方法，那么该类必须声明为抽象类。抽象类可以不包含抽象方法。
抽象方法的声明以分号结尾，例如：**public abstract sample();**。

### synchronized 修饰符
synchronized 关键字声明的方法同一时间只能被一个线程访问。synchronized 修饰符可以应用于四个访问修饰符。（`public`、`protected`、`default`、`private`）
```java
public synchronized void showDetails(){
.......
}
```

### transient 修饰符
当把一个对象序列化（比如写入文件或通过网络传输）时，被 `transient` 标记的实例变量会被 Java 虚拟机**跳过**，不参与序列化过程。

### volatile 修饰符
volatile 修饰的成员变量在每次被线程访问时，都强制从共享内存中重新读取该成员变量的值。而且，当成员变量发生变化时，会强制线程将变化值回写到共享内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。
```java
public class MyRunnable implements Runnable
{
    private volatile boolean active;
    public void run()
    {
        active = true;
        while (active) // 第一行
        {
            // 代码
        }
    }
    public void stop()
    {
        active = false; // 第二行
    }
}
```

### **volatile 关键字解释**

你选中的这段话描述了 `volatile` 解决的一个经典并发问题——**可见性问题**。
**核心问题：线程缓存导致变量修改不可见**
在多线程环境下，每个线程为了性能，会将共享变量的值从主内存拷贝一份到自己的线程缓存（CPU 缓存）中操作。当一个线程修改了变量，修改后的值可能只停留在它的缓存里，没有立即写回主内存，导致其他线程"看不见"这个变化。

**页面中的例子分析：​**
```java
public class MyRunnable implements Runnable {
    private volatile boolean active;  // volatile 修饰

    public void run() {
        active = true;
        while (active) {   // 第一行：循环条件判断
            // 代码
        }
    }

    public void stop() {
        active = false;    // 第二行：修改 active 为 false
    }
}

```


