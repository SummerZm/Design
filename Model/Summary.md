### <b>设计模式总结</b> ###
> <b>构造型设计模式： 对象的创建问题</b>
- 单例模式：
    1. 创建全局唯一的对象
    2. 饿汉式、懒汉式、双重检测、静态内部类、枚举

- 工厂模式：
    1. 隔离复杂性：封装复杂的创建逻辑，减少代码修改量。
    2. DI注入框架

- 建造者模式：
    1. 降低创建对象的复杂度
        - 必填的属性有很多
        - 类的属性之间有一定的依赖关系或者约束条件
        - 希望创建不可变对象
    
- 原型模式：
    1. 对象的创建成本比较大 [浅/深拷贝]

> <b>结构型设计模式：对象的组合问题 </b>
- 代理模式：
    1. 主要目的是控制访问，而非加强功能  
    2. 静态代理实现: a. 代理类和原始类实现同样的接口. b. 代理类继承原始类。
    3. 业务系统中一些非功能性需求，比如：监控、统计、鉴权、限流、事务、幂等、日志。将附加功能与业务功能解耦
    4. 可以用在 RPC、缓存等应用场景中
    5. 动态代理: Java使用反射实现
    
- 装饰器模式：
    1. 装饰器模式主要解决继承关系过于复杂的问题。
    2. 通过组合来替代继承，原始类嵌套使用多个不同的装饰器。
    3. 装饰器类需要具有一个或多个跟原始类继承相同的抽象类或者接口。

- 适配器模式:     
    1. 将不兼容的接口转换为可兼容的接口。
    2. 区别: 代理模式、装饰器模式提供的都是跟原始类相同的接口，而适配器提供跟原始类不同的接口。
    3. 实现：类适配器使用继承关系来实现，对象适配器使用组合关系来实现。
    4. 补救设计上的缺陷
        - 封装有缺陷的接口
        - 设计统一多个类的接口设计
        - 替换依赖的外部系统
        - 兼容老版本接口
        - 适配不同格式的数据

- 桥接模式：[不常用]
    1. 一种类之间的组合关系, 多个维度开发
    2. 例如：根据不同的紧急程度通过不同是通知方式发生消息
    3. 解决：引入用户维度选择创建发送者，通过类组合决定发送什么消息
    4. 心得：不要总想着在一个层面去解决所有难点。[引入用户编码维度]

- 门面模式： 
    1. 通过封装细粒度的接口，提供组合各个细粒度接口的高层次接口
    2. 解决性能问题 - 减少网络请求
    3. 分布式问题 - 原子性[这里只是门面思想的体现，具体实现由其他相关技术保证]
    4. 接口的易用性
 
- 组合模式： 
    1. 主要是用来处理树形结构数据
    2. 用递归处理结点，能让代码变得非常简洁。

- 享元模式：
    1. 复用对象，节省内存，前提是享元对象是不可变对象。
    2. 例子：棋盘的实现

> <b>结构型设计模式：对象的交互问题 </b>
- 观察者模式：[应用场景非常广]
    1. 目的： 将观察者和被观察者代码解耦
    2. 实现：
        - 同步阻塞：最经典的实现方式，主要是为了代码解耦
        - 异步非阻塞：代码解耦加执行效率 [EventBus框架]
        - 进程间的观察者模式：一般是基于消息队列来实现
        - 框架: 隐藏实现细节，降低开发难度，实现代码复用，解耦业务与非业务代码
    
    3. 应用：
        - 代码层面，架构层面的解耦
        - 产品的设计思路
        - 邮件订阅、RSS Feeds

- 模板模式：
    1. 子类在不改变算法整体框架的情况下，重新定义算法中的某些步骤或拓展框架
        - 子类可以复用父类中提供的模板方法的代码
        - 通过模板模式提供功能扩展点，可以在不修改框架源码的情况下，基于扩展点定制化框架的功能。[回调]

    2. 名词解释：回调
        - A 类事先注册某个函数 F 到 B 类，A 类在调用 B 类的 P 函数的时候，B 类反过来调用 A 类注册给它的 F 函数。
        - 上述 F 函数就是“回调函数”。A 调用 B，B 反过来又调用 A，这种调用机制就叫作“回调”。
       
    3. 回调基于组合关系来实现，模板模式基于继承关系来实现。

- 策略模式：
    1. 避免冗长的 if-else 或 switch 分支判断; 提供框架的扩展点; 使算法的变化独立于使用它们的客户端（这里的客户端代指使用算法的代码）
    2. 完整的策略模式
        - 策略类的定义: 一个策略接口和一组实现这个接口的策略类。
        - 策略的创建: 由工厂类来完成，封装策略创建的细节。
        - 策略的使用: 编译时静态确定 - 运行时动态确定。

- 职责链模式：
    1. 用来实现过滤器、拦截器功能，让框架的使用者在不需要修改框架源码的情况下，添加新的过滤、拦截功能。
    
- 迭代器模式： 
    1. 迭代器模式让添加新的遍历算法更加容易，更符合开闭原则。
    2. 通过迭代器来遍历集合元素的同时，增加或者删除集合中的元素，有可能会导致某个元素被重复遍历或遍历不到
        - 解决方案1：遍历的时候不允许增删元素 [很难确定迭代器使用结束的时间点]
        - 解决方案2：增删元素之后让遍历报错 [Java语言采用]

- 状态模式: 
    1. 状态模式一般用来实现状态机; 有限状态机组成：状态、事件、动作
    2. 事件触发状态的转移及动作的执行
    3. 实现方式
        - 分支逻辑法: 利用 if-else 或者 switch-case 分支逻辑，参照状态转移图，将每一个状态转移原模原样地直译成代码
        - 查表法: 对于状态很多、状态转移比较复杂的状态机来说，查表法比较合适。通过二维数组来表示状态转移图，能极大地提高代码的可读性和可维护性。
        - 状态模式: 对于状态并不多、状态转移也比较简单，但事件触发执行的动作包含的业务逻辑可能比较复杂的状态机来说，我们首选这种实现方式。
        
- 访问者模式: [可读性差]
    0. 允许一个或者多个操作应用到一组对象上
    1. 如果某种语言支持 Double Dispatch，那就不需要访问者模式
    2. 学习的主要难点在代码实现。而代码实现比较复杂的主要原因是，函数重载在大部分面向对象编程语言中是静态绑定的。也就是说，调用类的哪个重载函数，是在编译期间，由参数的声明类型决定的, 函数重载在大部分面向对象编程语言中是静态绑定的。也就是说，调用类的哪个重载函数，是在编译期间，由参数的声明类型决定的，而非运行时，根据参数的实际类型决定的


- 备忘录模式：
    1. 不违背封装原则的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，以便之后恢复对象为先前的状态。

- 命令模式：
    1. 将函数封装成对象

- 解释器模式：
    1. 核心思想，就是将语法解析的工作拆分到各个小类中，以此来避免大而全的解析类。
    
- 中介模式：
    1. 引入中介这个中间层，将一组对象之间的交互关系（或者说依赖关系）从多对多（网状关系）转换为一对多（星状关系）。
    2. 观察者模式的应用场景中，参与者之间的交互比较有条理，一般都是单向的，一个参与者只有一个身份，要么是观察者，要么是被观察者。
    3. 中介模式的应用场景中，参与者之间的交互关系错综复杂，既可以是消息的发送者、也可以同时是消息的接收者。