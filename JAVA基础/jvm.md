# jvm内存结构(运行时数据区)
![JVM运行时数据区](media/JVM%E8%BF%90%E8%A1%8C%E6%97%B6%E6%95%B0%E6%8D%AE%E5%8C%BA.png)
# java 内存模型
![opp-klass模型](media/opp-klass%E6%A8%A1%E5%9E%8B.jpeg)


# 垃圾收集器

# 内存分配策略

# 语法糖
* 泛型及类型擦除
    * 伪泛型，只是进行编译时的检查，编译过后的代码，会在使用的地方使用强制类型转化
    * 方法的特征签名
        * 代码层面：方法名称+参数，方法的返回值不包含在特征签名中
        * 字节码层面：特征签名包括返回值，和受检查异常表，范围更大一些
    * 类型擦除只是在方法code属性中进行字节码的擦除，但是类的元数据中还是保留着泛型信息
    * 导致public  void method(List<String> list)，public void method(List<Integer> list)无法进行重载
        * public  String method(List<String> list)
        * public int method(List<Integer> list)
        * 上述连个方法，javac是编译可以通过的，因为字节码层面，返回值是加入到方法特征签名的，所以这种事允许共存的。并且编译器是可以通过泛型类型去判断调用哪个方法的，所以这种形式的重载是允许的。
        * **如果两个泛型方法在擦除泛型信息后，如果只是具有相同的参数类型，而返回值不一样，就可以进行重载**，此种编译类似在**jdk1.7+**已经无法编译通过了，jdk6是可以的。
* 自动装箱拆箱
    * 自动装箱：编译器调用包装类的valueOf将原始类型值，转化为对象
    * 自动拆箱：编译器通过调用类似intValue，doubleValue方法将对象转化为原始类型的值
*  Integer valueof 缓存池问题
    *  缓存了-128到127之间的值,因为负数在计算机中使用反码进行表示，那么存在一个问题[0000000]和[10000000]分别为0和-0，其实是一样的，所以8位中就使用了[00000000]来表示0，[10000000]用来表示-128
*  类型的取值范围
    *  原码，反码，补码(https://www.cnblogs.com/zhangziqiu/archive/2011/03/30/ComputerCode.html)
        *  原码，最高位为符号位，比如+1的原码为 00000001，-1的原码为10000001，所以8位二进制数的取值范围就是[1111111,0111111]即[-127,127]
        *  反码，正数的反码是其本身，负数的反码是符号位不变，其余按位取反
            ```
            [+1] = [00000001]原 = [00000001]反
            [-1] = [10000001]原 = [11111110]反
            ```
            
        *  补码，正数的补码是其本身，负数的补码，在反码的基础上+1
            ```
            [+1] = [00000001]原 = [00000001]反 = [00000001]补
            [-1] = [10000001]原 = [11111110]反 = [11111111]补
            ```
            
        *  补码的引入，是为了解决。让计算计算减法的时候，使用加法去代替
            ```
            1-1 = 1 + (-1) = [0000 0001]原 + [1000 0001]原 = [0000 0001]补 + [1111 1111]补 = [0000 0000]补=[0000 0000]原
            ```  
            
     * 并且[10000000]可以用来表示-128
     ```
     (-1) + (-127) = [1000 0001]原 + [1111 1111]原 = [1111 1111]补 + [1000 0001]补 = [1000 0000]补
     ```       
* 条件编译
    * 例子
    
        ```java
        public class Hello { 
            public static void main(String[] args) { 
                if(false) { 
                    System.out.println("Hello, world!"); 
                } 
            } 
        }
        
        此段代码经过编译后，产生的字节码文件反编译后查看如下 
        
         public class Hello { 
            public static void main(String[] args) { 
            } 
        }
        ```
    * 编译器在编译时会对代码进行优化，对于条件永远为false(需要时常量分支，final修饰的变量)的语句，java编译器将不会对其生成字节码。
        
* 变长参数
    * 编译器将变长参数包装成一个数组，所以在重载方法是，String[] args和String... vars会被当作是相同签名的方法，故在源码级别是不可能同时存在，所以无法编译通过的。
* 遍历循环
    * 遍历循环编译器会编译为迭代器的迭代的方式
* 内部类
    * outer.java里面定义了一个内部类inner，编译器，会生成连个完全不相同的class文件，分别是outer.class和outer$inner.class
    * 成员内部类
        * 作为外部类的一个成员，故可以无限制的访问外部类的所有成员属性和方法
        * 外部类需要通过内部类的实例访问内部类的属性
    * 局部内部类和匿名内部类
        * 局部内部类嵌套在方法和作用域
        * 局部内部类和匿名内部类引用外部变量时，外部的变量需要final
        * 为什么引用的变量要声明为final，因为若未声明为final，则在方法运行完毕后，局部变量会被回收，在可能出现内部类在引用一个不存在的局部变量。将局部变量声明为final，局部变量在被clean up时，会把final局部变量替换成常量，即将局部变量编程局部内部类变量的数据成员，这也就是java的闭包
    * 静态内部类
* 枚举类
    * 编译器会将枚举类编译成一个普通类（继承java.lang.Enum）
    * 并且添加一个values的方法和valueof方法
* 断言语句
* 对枚类
* 字符串的switch支持(jdk1.7+)
    * int类型：直接比较整数值
    * char类型：比较ascii码
    * String类型：使用hashCode和equal方法
    
    ```
        public class switchDemoString {
            public static void main(String[] args) {
                String str = "world";
                switch (str) {
                case "hello":
                    System.out.println("hello");
                    break;
                case "world":
                    System.out.println("world");
                    break;
                default:
                    break;
                }
            }
        }
        
        class文件反编译
        public class switchDemoString
        {
            public switchDemoString()
            {
            }
            public static void main(String args[])
            {
                String str = "world";
                String s;
                switch((s = str).hashCode())//使用了hashcode
                {
                default:
                    break;
                case 99162322:
                    if(s.equals("hello"))
                        System.out.println("hello");
                    break;
                case 113318802:
                    if(s.equals("world"))
                        System.out.println("world");
                    break;
                }
            }
        }
    ``` 
    * **switch只能使用int类型,其他的数据类型是转换成整形之后再使用switch case的**
* try语句中定义和关闭资源（try-with-resource）
    * try-catch-finally
    
    ```
    public class Demo {
        public static void main(String[] args) {
            BufferedInputStream bin = null;
            BufferedOutputStream bout = null;
            try {
                bin = new BufferedInputStream(new FileInputStream(new File("test.txt")));
                bout = new BufferedOutputStream(new FileOutputStream(new File("out.txt")));
                int b;
                while ((b = bin.read()) != -1) {
                    bout.write(b);
                }
            }
            catch (IOException e) {
                e.printStackTrace();
            }
            finally {
                if (bin != null) {
                    try {
                        bin.close();
                    }
                    catch (IOException e) {
                        e.printStackTrace();
                    }
                    finally {
                        if (bout != null) {
                            try {
                                bout.close();
                            }
                            catch (IOException e) {
                                e.printStackTrace();
                            }
                        }
                    }
                }
            }
        }
    }
    ```
    * try-with-resource 
       
    ```
    public class TryWithResource {
        public static void main(String[] args) {
            try (BufferedInputStream bin = new BufferedInputStream(new FileInputStream(new File("test.txt")));
                 BufferedOutputStream bout = new BufferedOutputStream(new FileOutputStream(new File("out.txt")))) {
                int b;
                while ((b = bin.read()) != -1) {
                    bout.write(b);
                }
            }
            catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
    ```
    
    * 使用try-with-resource,资源必须实现AutoClosable接口，重写close方法
    
    ```
        public class Connection implements AutoCloseable {
            public void sendData() {
                System.out.println("正在发送数据");
            }
            @Override
            public void close() throws Exception {
                System.out.println("正在关闭连接");
            }
        }
        
        public class TryWithResource {
            public static void main(String[] args) {
                try (Connection conn = new Connection()) {
                    conn.sendData();
                }
                catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
        
        class反编译结果:
        
        public class TryWithResource {
            public TryWithResource() {
            }
            public static void main(String[] args) {
                try {
                    Connection e = new Connection();
                    Throwable var2 = null;
                    try {
                        e.sendData();
                    } catch (Throwable var12) {
                        var2 = var12;
                        throw var12;
                    } finally {//自动生成了finally语句块
                        if(e != null) {
                            if(var2 != null) {
                                try {
                                    e.close();
                                } catch (Throwable var11) {
                                    var2.addSuppressed(var11);//解决异常屏蔽问题
                                }
                            } else {
                                e.close();
                            }
                        }
                    }
                } catch (Exception var14) {
                    var14.printStackTrace();
                }
            }
        }
    ```
    
    


# java内存模型(线程内存模型)
![JVM线程模型](media/JVM%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9E%8B.png)

* happens-before规则
《JSR-133:Java Memory Model and Thread Specification》中定义
    * **程序顺序规则**:一个线程中的每个操作，happens-before于该线程中的任意后续操作
    * **监视器锁规则**:对一个锁的解锁，happens-before于随后对于这个锁的加锁
    * **volatile变量规则**:对一个volatile域的写，happens-before于任意后续对这个volatile域的读
    * **传递性**:如果A happens-before B,且B happens-before C,那么A happens-before C
    * **start()规则**:如果线程A执行操作ThreadB.start()(启动线程B),那么A线程的ThreadB.start()操作happens-before于线程B中的任意操作
    * **join()规则**:如果线程A执行操作ThreadB.join()并成功返回，那么线程B中的任意操作happens-before于线程A从ThreadB.join()操作成功返回

# JDK命令
* jps(jvm process status tool )
    显示制定下系统内所有hotspot虚拟机进程
* jstat(jvm statistics Monitoring tool)
    监视虚拟机各种运行状态信息
* jinfo(configuration info for java)
    实时查看和调整虚拟机的个性参数
* jmap(memory map for java)
    生成堆转储快照,查询finalize执行队列，Java堆和永久代的详细信息，如空间使用率、当前使用的垃圾收集器的类型
* jhat(jvm heap analusis tool)
    虚拟机堆转储快照分析工具,一般都是使用mat对堆dump进行分析
* jstack(stack trace for java)
    java堆栈跟踪工具，生成线程快照
* HSDIS
    JIT生成代码反汇编
* BTrace
    visualVM的插件，动态日志追踪，程序运行时，增量加入日志代码，以调试程序        
