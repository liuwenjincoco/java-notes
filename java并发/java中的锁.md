同步
---- 

# volatile变量

* volatile变量，用来确保将变量的更新操作通知到其他线程，当变量被声明为volatile的时候，编译器和运行时都会注意到该变量是共享的，因此不会将该变量上的操作与其他内存操作一起进行重排序。
* volatile不会被缓存到寄存器或者其他处理器不可见的地方，所以在读取volatile变量时总能读取到最新写入的值。
* 访问volatile变量时**不会进行加锁操作**，因此不会使执行线程阻塞，因此volatile变量是比sychronized更轻量级的同步机制。
* volatile变量通常用作某个操作完成、发生中断或者状态的标志
* volatile只能确保可见性，无法确保原子性。加锁是可以既确保原子性也确保可见性的。
* 应当在满足如下条件时，才使用volatile
    * 对变量的写操作不依赖该变量当前值，或者确保只有单个线程更新变量的值。
    * 该变量不会与其他状态变量一起纳入不变形条件中。
    * 在访问变量时不需要加锁。
* 正确使用volatile变量:https://www.ibm.com/developerworks/cn/java/j-jtp06197.html

# 线程封闭
* ad-hoc封闭
* 栈封闭
    * 局部变量，线程内使用或者线程局部使用
    * 局部变量的初始化也必须栈封闭，要不然也可能出现线程不安全的操作
* ThreadLocal
    * 每个使用该变量的线程，都保存一份副本
    * 通常用于防止对可变的单实例变量或全局变量进行共享

# final域
* 不可变
* 基础数据类型，int，变量不变，因为拿不到引用
* 若是final ClassA a = new ClasssA()的形式，仅仅只是说明a的引用不会变，但是具体ClassA的对象是可变的

# 并发容器
* ConcurrentHashMap

    https://www.ibm.com/developerworks/cn/java/java-lo-concurrenthashmap/index.html

*  



    