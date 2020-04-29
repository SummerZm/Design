## <b>适配器模式</b> ##
> 作用：提供对内对外一致的接口, 便于系统的集成替换

### <b>Summary</b> ###
1. 封装设配第三方SDK
2. 更换接口组件
    
### <b>应用场景</b> ###
- 封装有缺陷的接口 [重点: 抽象接口]
    ```java
    public class CD { //这个类来自外部sdk，我们无权修改它的代码
      public static void staticFunction1() { //... }
      public void uglyNamingFunction2() { //... }
      public void tooManyParamsFunction3(int paramA, int paramB, ...) { //... }
      public void lowPerformanceFunction4() { //... }
    }

    // 使用适配器模式进行重构
    public class ITarget {
      void function1();
      void function2();
      void fucntion3(ParamsWrapperDefinition paramsWrapper);
      void function4();
      //...
    }

    // 注意：适配器类的命名不一定非得末尾带Adaptor
    public class CDAdaptor extends CD implements ITarget {
      public void function1() {
         super.staticFunction1();
      }
      
      public void function2() {
        super.uglyNamingFucntion2();
      }
      
      public void function3(ParamsWrapperDefinition paramsWrapper) {
         super.tooManyParamsFunction3(paramsWrapper.getParamA(), ...);
      }
      
      public void function4() {
        //...reimplement it...
      }
    }
    ```

- 统一多个第三方类类库接口 [重点: 适配器统一接口带来的好处]
    ```java
    // 第三方接口参数个数不一致问题如何解决? wait
    public class ASensitiveWordsFilter { // A敏感词过滤系统提供的接口
      //text是原始文本，函数输出用***替换敏感词之后的文本
      public String filterSexyWords(String text) {
        // ...
      }
      
      public String filterPoliticalWords(String text) {
        // ...
      } 
    }

    public class CSensitiveWordsFilter { // C敏感词过滤系统提供的接口
      public String filter(String text, String mask) {
        //...
      }
    }

    // 未使用适配器模式之前的代码：代码的可测试性、扩展性不好
    public class RiskManagement {
      private ASensitiveWordsFilter aFilter = new ASensitiveWordsFilter();
      private CSensitiveWordsFilter cFilter = new CSensitiveWordsFilter();
      
      public String filterSensitiveWords(String text) {
        String maskedText = aFilter.filterSexyWords(text);
        maskedText = aFilter.filterPoliticalWords(maskedText);
        maskedText = bFilter.filter(maskedText);
        maskedText = cFilter.filter(maskedText, "***");
        return maskedText;
      }
    }

    // 使用适配器模式进行改造
    // 框架类调用接口，子类根据各自逻辑实现接口
    // 如果子类来自第三方，可通过继承封装多一层。封装方式参照上一个例子
    public interface ISensitiveWordsFilter { // 统一接口定义
      String filter(String text);
    }

    public class ASensitiveWordsFilterAdaptor implements ISensitiveWordsFilter {
      private ASensitiveWordsFilter aFilter;
      public String filter(String text) {
        String maskedText = aFilter.filterSexyWords(text);
        maskedText = aFilter.filterPoliticalWords(maskedText);
        return maskedText;
      }
    }
    //...省略BSensitiveWordsFilterAdaptor、CSensitiveWordsFilterAdaptor...

    // 扩展性更好，更加符合开闭原则，如果添加一个新的敏感词过滤系统，
    // 这个类完全不需要改动；而且基于接口而非实现编程，代码的可测试性更好。
    public class RiskManagement { 
      private List<ISensitiveWordsFilter> filters = new ArrayList<>();
     
      public void addSensitiveWordsFilter(ISensitiveWordsFilter filter) {
        filters.add(filter);
      }
      
      public String filterSensitiveWords(String text) {
        String maskedText = text;
        for (ISensitiveWordsFilter filter : filters) {
          maskedText = filter.filter(maskedText);
        }
        return maskedText;
      }
    }
    ```    

- 替换系统依赖的组件 [重点: 适配器的组装使用方式]
    ```java
    // 外部系统A
    public interface IA {
      //...
      void fa();
    }
    public class A implements IA {
      //...
      public void fa() { //... }
    }
    // 在我们的项目中，外部系统A的使用示例
    public class Demo {
      private IA a;
      public Demo(IA a) {
        this.a = a;
      }
      //...
    }
    Demo d = new Demo(new A());

    // 将外部系统A替换成外部系统B
    public class BAdaptor implemnts IA {
      private B b;
      public BAdaptor(B b) {
        this.b= b;
      }
      public void fa() {
        //...
        b.fb();
      }
    }
    // 借助BAdaptor，Demo的代码中，调用IA接口的地方都无需改动，
    // 只需要将BAdaptor如下注入到Demo即可。
    Demo d = new Demo(new BAdaptor(new B()));
    ```

### <b>类适配器/对像适配器</b> ###
    ```java
    // 类适配器: 基于继承
    public interface ITarget {
      void f1();
      void f2();
      void fc();
    }

    public class Adaptee {
      public void fa() { //... }
      public void fb() { //... }
      public void fc() { //... }
    }

    public class Adaptor extends Adaptee implements ITarget {
      public void f1() {
        super.fa();
      }
      
      public void f2() {
        //...重新实现f2()...
      }
      
      // 这里fc()不需要实现，直接继承自Adaptee，这是跟对象适配器最大的不同点
    }

    // 对象适配器：基于组合
    public interface ITarget {
      void f1();
      void f2();
      void fc();
    }

    public class Adaptee {
      public void fa() { //... }
      public void fb() { //... }
      public void fc() { //... }
    }

    public class Adaptor implements ITarget {
      private Adaptee adaptee;
      
      public Adaptor(Adaptee adaptee) {
        this.adaptee = adaptee;
      }
      
      public void f1() {
        adaptee.fa(); //委托给Adaptee
      }
      
      public void f2() {
        //...重新实现f2()...
      }
      
      public void fc() {
        adaptee.fc();
      }
    }

    ```
