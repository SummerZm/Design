## <b>观察者模式/订阅模式</b> ##
> 在对象之间定义一个一对多的依赖，当一个对象状态改变的时候，所有依赖的对象都会自动收到通知

### 观察者模式的类型 ###
- 同步阻塞是最经典的实现方式，主要是为了代码解耦
- 异步非阻塞除了能实现代码解耦之外，还能提高代码的执行效率
- 进程间的观察者模式一般是基于消息队列来实现，用来实现不同进程间的被观察者和观察者之间的交互。

### 进程内同步阻塞观察者模式 ###
```java
    /*  
     *  消息产生时，总控调用每个用户有自己的处理方式
     *  用户们提供方式，订阅中心选择加载这些方式，消息发生时订阅中心通知用户们
     */
    public interface Subject {
        void registerObserver(Observer observer);
        void removeObserver(Observer observer);
        void notifyObservers(Message message);
    }

    public interface Observer {
        void update(Message message);
    }

    public class ConcreteSubject implements Subject {
        private List<Observer> observers = new ArrayList<Observer>();

        @Override
        public void registerObserver(Observer observer) {
            observers.add(observer);
        }

        @Override
        public void removeObserver(Observer observer) {
            observers.remove(observer);
        }

        @Override
        public void notifyObservers(Message message) {
            for (Observer observer : observers) {
            observer.update(message);
            }
        }

    }

    public class ConcreteObserverOne implements Observer {
        @Override
        public void update(Message message) {
            //TODO: 获取消息通知，执行自己的逻辑...
            System.out.println("ConcreteObserverOne is notified.");
        }
    }

    public class Demo {
        public static void main(String[] args) {
            ConcreteSubject subject = new ConcreteSubject();
            subject.registerObserver(new ConcreteObserverOne());
            subject.notifyObservers(new Message());
        }
    }

    /*
     * 场景：一件事由许多件子事情组成，并且这件事可以灵活组合
     * 原则：单一的原则 [一个函数只做一件事]
     * 方法：维护一个可定制事件列表
     * 解析：事件是被观察者，子事件代码所在的模块的观察者
     * 缺点：register() 函数依次调用执行每个观察者的 handleRegSuccess() 函数，等到都执行完成之后，才会返回结果给客户端
     * 解决：启动一个新的线程来执行观察者的 handleRegSuccess() 函数
     */
    public interface RegObserver {
        void handleRegSuccess(long userId);
    }

    public class RegPromotionObserver implements RegObserver {
        private PromotionService promotionService; // 依赖注入

        @Override
        public void handleRegSuccess(long userId) {
            promotionService.issueNewUserExperienceCash(userId);
        }
    }

    /* [订阅中心] */
    public class UserController {
        private UserService userService; // 依赖注入 - [被观察者]
        private List<RegObserver> regObservers = new ArrayList<>();

        // 一次性设置好，之后也不可能动态的修改
        // 设置 - [观察者]
        public void setRegObservers(List<RegObserver> observers) {
            regObservers.addAll(observers);
        }

        public Long register(String telephone, String password) {
            // 省略输入参数的校验代码
            // 省略userService.register()异常的try-catch代码
            // 执行完被观察者代码，再执行观察者代码，最后才返回客户端。【同步阻塞】
            long userId = userService.register(telephone, password);

            for (RegObserver observer : regObservers) {
                observer.handleRegSuccess(userId);
            }
            return userId;
        }
    }
```

### 进程内异步非阻塞观察者模式 ###
- 加多一个中间，抽出一个注册表对象
- 代码示例：
```java
    
    public class UserController {
        private UserService userService; // 依赖注入

        private EventBus eventBus;
        private static final int DEFAULT_EVENTBUS_THREAD_POOL_SIZE = 20;

        public UserController() {
            //eventBus = new EventBus(); // 同步阻塞模式
            eventBus = new AsyncEventBus(Executors.newFixedThreadPool(DEFAULT_EVENTBUS_THREAD_POOL_SIZE)); // 异步非阻塞模式
        }

        public void setRegObservers(List<Object> observers) {
            for (Object observer : observers) {
                eventBus.register(observer);
            }
        }

        public Long register(String telephone, String password) {
            //省略输入参数的校验代码
            //省略userService.register()异常的try-catch代码
            long userId = userService.register(telephone, password);

            eventBus.post(userId);

            return userId;
        }
    }

    /* java 所有的对象都是从object派生出来的 */
    public class RegPromotionObserver {
        private PromotionService promotionService; // 依赖注入

        @Subscribe
        public void handleRegSuccess(long userId) {
            promotionService.issueNewUserExperienceCash(userId);
        }
    }

    public class RegNotificationObserver {
        private NotificationService notificationService;

        @Subscribe
        public void handleRegSuccess(long userId) {
            notificationService.sendInboxMessage(userId, "...");
        }
    }

    /* Event bus 总线实现代码 - 观察者 */
    @Retention(RetentionPolicy.RUNTIME)
    @Target(ElementType.METHOD)
    @Beta
    public @interface Subscribe {}

    /* 观察者 */
    public class ObserverAction 
    {
        private Object target;
        private Method method;

        public ObserverAction(Object target, Method method) {
            this.target = Preconditions.checkNotNull(target);
            this.method = method;
            this.method.setAccessible(true);
        }

        public void execute(Object event) { // event是method方法的参数
            try {
                method.invoke(target, event);
            } catch (InvocationTargetException | IllegalAccessException e) {
                e.printStackTrace();
            }
        }
    }

    /* 观察者注册表 */
    public class ObserverRegistry 
    {
       private ConcurrentMap<Class<?>, CopyOnWriteArraySet<ObserverAction>> registry = new ConcurrentHashMap<>();

        public void register(Object observer) 
        {
            Map<Class<?>, Collection<ObserverAction>> observerActions = findAllObserverActions(observer);
            for (Map.Entry<Class<?>, Collection<ObserverAction>> entry : observerActions.entrySet()) 
            {
                Class<?> eventType = entry.getKey();
                Collection<ObserverAction> eventActions = entry.getValue();
                CopyOnWriteArraySet<ObserverAction> registeredEventActions = registry.get(eventType);
                if (registeredEventActions == null) {
                    registry.putIfAbsent(eventType, new CopyOnWriteArraySet<>());
                    registeredEventActions = registry.get(eventType);
                }
                registeredEventActions.addAll(eventActions);
            }
        }

        public List<ObserverAction> getMatchedObserverActions(Object event) 
        {
            List<ObserverAction> matchedObservers = new ArrayList<>();
            Class<?> postedEventType = event.getClass();
            for (Map.Entry<Class<?>, CopyOnWriteArraySet<ObserverAction>> entry : registry.entrySet()) 
            {
                Class<?> eventType = entry.getKey();
                Collection<ObserverAction> eventActions = entry.getValue();
                if (postedEventType.isAssignableFrom(eventType)) {
                    matchedObservers.addAll(eventActions);
                }
            }
            return matchedObservers;
        }

        private Map<Class<?>, Collection<ObserverAction>> findAllObserverActions(Object observer) 
        {
            Map<Class<?>, Collection<ObserverAction>> observerActions = new HashMap<>();
            Class<?> clazz = observer.getClass();
            for (Method method : getAnnotatedMethods(clazz)) {
                Class<?>[] parameterTypes = method.getParameterTypes();
                Class<?> eventType = parameterTypes[0];
                if (!observerActions.containsKey(eventType)) {
                    observerActions.put(eventType, new ArrayList<>());
                }
                observerActions.get(eventType).add(new ObserverAction(observer, method));
            }
            return observerActions;
        }

        private List<Method> getAnnotatedMethods(Class<?> clazz) 
        {
            List<Method> annotatedMethods = new ArrayList<>();
            for (Method method : clazz.getDeclaredMethods()) {
            if (method.isAnnotationPresent(Subscribe.class)) {
                Class<?>[] parameterTypes = method.getParameterTypes();
                Preconditions.checkArgument(parameterTypes.length == 1,
                        "Method %s has @Subscribe annotation but has %s parameters."
                                + "Subscriber methods must have exactly 1 parameter.",
                        method, parameterTypes.length);
                annotatedMethods.add(method);
            }
            }
            return annotatedMethods;
        }
    }

    public class EventBus {
        private Executor executor;
        private ObserverRegistry registry = new ObserverRegistry();

        public EventBus() {
            // 同步阻塞的方式
            this(MoreExecutors.directExecutor());
        }

        protected EventBus(Executor executor) {
            // 注入线程池 - 实现异步非阻塞
            this.executor = executor;
        }

        public void register(Object object) {
            registry.register(object);
        }

        public void post(Object event) {
            List<ObserverAction> observerActions = registry.getMatchedObserverActions(event);
            for (ObserverAction observerAction : observerActions) {
                executor.execute(new Runnable() {
                    @Override
                    public void run() {
                    observerAction.execute(event);
                    }
                });
            }
        }
    }

    public class AsyncEventBus extends EventBus {
        public AsyncEventBus(Executor executor) {
            super(executor);
        }
    }

```