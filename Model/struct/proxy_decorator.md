## <b>代理模式&装饰者模式</b> ##
> 作用: 不修改原接口附加一些功能;分割业务代码和逻辑代码

### <b>实现代理模式的方法</b> ###
- 组合代理: 分割业务代码和逻辑代码
- 继承代理: 应用于第三方类库.
- 动态代理: 面向切面编程[AOP], 基于java反射机制.

### <b>两种模式的异同</b> ###
- 装饰器模式关注于在一个对象上动态的添加方法. [例子: java io实现]
- 代理模式关注于控制对对象的访问.
- 使用代理模式的时候，我们常常在一个代理类中创建一个对象的实例。(对它的客户隐藏一个对象的具体信息)
- 使用装饰器模式的时候，我们通常的做法是将原始对象作为一个参数传给装饰者的构造器。


### <b>代码实现</b> ###
```java
    // 从代码上看代理模式和装饰者模式的本质区别在于: 功能实现细节的隐藏程度.
    // 下面的代码
    // 对于使用者的区别在于：装饰者模式有更多的选择选择，需要使用者去组合.
    // 对于编写者的区别在于：对象的创建时机位置,代理模式内部创建，装饰者传参.
    // 本质需求是：增强对象的可拓展性，其次是把握功能实现细节隐藏的程度.

    // 代理模式
    public class Proxy implements Subject{
       private Subject subject;
       public Proxy(){
             //关系在编译时确定
            subject = new RealSubject();
       }
       public void doAction(){
             ….
             subject.doAction();
             ….
       }
    }

    private Subject subject;
    public Proxy(){
         //关系在编译时确定
        subject = new RealSubject();
    }
    public void doAction(){
         ….
         subject.doAction();
         ….
    }

    //装饰器模式
    public class Decorator implements Component{
            private Component component;
            public Decorator(Component component){
                this.component = component
            }
           public void operation(){
                ….
                component.operation();
                ….
           }
    }

    //装饰器的客户
    public class Client{
        public static void main(String[] args){
            //客户指定了装饰者需要装饰的是哪一个类
            Component component = new Decorator(new ConcreteComponent());
            …
        }
    }
```
