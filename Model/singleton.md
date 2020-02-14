## <b>单例模式</b> ##
> 定义: 单例类中对象是唯一的

### 1. 资源访问冲突问题解决方案？ ###
1. 单例模式
2. 分布式锁
3. 并发队列 (多个线程同时往并发队列里写日志，一个单独的线程负责将并发队列中的数据，写入到日志文件)

### 2. 单例模式的缺点 ###
1. 单例对代码的扩展性不友好 (通过多例解决)
2. 单例对代码的可测试性不友好
3. 单例不支持有参数的构造函数
4. 替代方案
    - 工厂模式、IOC 容器（比如 Spring IOC 容器）
    - 自己在编写代码的时候自己保证不要创建两个类对象

### 3. 单例模式的实现注意点 ###
1. 考虑是否支持延迟加载
  - 在涉及到异步的网络数据交换如读取服务器配置或者数据库，则此构造过程可能会被操作系统打断而没有完成加载，所以需要延时加载。

2. 对象创建时的线程安全问题
  - 多线程时，每个线程都从内存中读取数据到寄存器中，volatile可以强制使用内存数据，避开的寄存器中的数据不一致问题。

3. getInstance() 性能是否高（是否加锁）
4. 加锁时的指令重排序问题（低版本java，解决：volatile修饰） 
  - javav编译的重排序主要是先执行比较耗时的IO操作。
  - 指令执行的原子性需要CPU支持相关的微指令。

### 4. 单例模式的实现方式 ###
1. 饿汉式 - 在类加载的期间，就已经将 instance 静态实例初始化好了，所以是线程安全的。不支持延迟加载实例。
2. 懒汉式 - 延迟加载。这种实现方式会导致频繁加锁、释放锁，以及并发度低等问题，频繁的调用会产生性能瓶颈。
3. 不同语言有不同实现。比如：java的类锁(指令重排序问题)。

### 5. 单例模式的唯一性的范围 ###
1. 线程单例
    - 对象级别的锁
    - 某些线程库的机制

2. 进程单例
    - 全局变量
    - 静态变量
    - 其他

3. 集群单例（进程程间共享一个实例）
    - 单例类的序列化


### 6. 单例模式多例的实现方式 ###

> 多例模式创建的对象是同一个类但语义不同的对象; 工厂模式创建的对象是不同类的对象。

```java
// example 1
public class BackendServer {
  private long serverNo;
  private String serverAddress;

  private static final int SERVER_COUNT = 3;
  private static final Map<Long, BackendServer> serverInstances = new HashMap<>();

  static {
    serverInstances.put(1L, new BackendServer(1L, "192.134.22.138:8080"));
    serverInstances.put(2L, new BackendServer(2L, "192.134.22.139:8080"));
    serverInstances.put(3L, new BackendServer(3L, "192.134.22.140:8080"));
  }

  private BackendServer(long serverNo, String serverAddress) {
    this.serverNo = serverNo;
    this.serverAddress = serverAddress;
  }

  // 获取指定实例
  public BackendServer getInstance(long serverNo) {
    return serverInstances.get(serverNo);
  }

  // 获取随机实例
  public BackendServer getRandomInstance() {
    Random r = new Random();
    int no = r.nextInt(SERVER_COUNT)+1;
    return serverInstances.get(no);
  }
}

// example 2
public class Logger {
  private static final ConcurrentHashMap<String, Logger> instances
          = new ConcurrentHashMap<>();

  private Logger() {}

  public static Logger getInstance(String loggerName) {
    // 如果不存在，创建新实例
    instances.putIfAbsent(loggerName, new Logger());
    return instances.get(loggerName);
  }

  public void log() {
    //...
  }
}

//l1==l2, l1!=l3
Logger l1 = Logger.getInstance("User.class");
Logger l2 = Logger.getInstance("User.class");
Logger l3 = Logger.getInstance("Order.class");
```
