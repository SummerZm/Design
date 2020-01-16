## <b>OOP and Design</b> ##

### <font color="deepskyblue"><b>Leafxu</b></font> ###
 - <font color="deepskyblue">Design rule is core.</font>
 - <font color="deepskyblue">设计原则和面向对象语言特性初衷都是更好的工程维护性</font>
 - <font color="deepskyblue">代码设计需要开发人员对工程这一概念有一定的理解</font>

### <b>OOP Origin</b> ###
> <font color="deepskyblue"><b>实践理论往往源于现实中的问题和需求！</b></font>
- 需求1：数据的安全和可控   [封装]
- 需求2：减少重复冗余工作   [抽象/继承]
- 需求3：程序管理的便捷性   [多态]

### <b>OOP Feature</b> ###
- Package: &nbsp;访问权限控制,&nbsp;保证数据安全,&nbsp;有利于用户
    - 目的：隐藏内部数据属性, 避免误操作
    - 实现：需要语言机制支持
    - 注意：不要滥用getter/setter.

- Inherit: &nbsp;代码重用,&nbsp;有利于开发人员
    - 目的：代码复用，第三方类的mock
    - 实现：需要语言机制支持

- Abstract: &nbsp;屏蔽细节/实现,&nbsp;有利于用户和开发人员
    - 目的：关注功能，不关心逻辑实现
    - 实现：函数的封装等，不限于接口，类的抽象【容易与面向对象中封装特性搞混】
    - 要求：合理命名规范

- Polymorphism: &nbsp;灵活替换,&nbsp;依赖于Inherit,&nbsp;许多设计原则的基础,&nbsp;有利于开发人员
    - 目的：程序自动识别调符合约定规范的api
    - 实现：继承，接口类，动态语言的duck-type语法

<font size=2>*用户&nbsp;:&nbsp;包括第三方开发人员*</font>

### <b>设计原则 [SOLID]</b> ###
> <font color="deepskyblue"><b>生活一些问题需求处理方法的抽象. 设计源于生活</b></font>
- <b>S</b>单一职责原则[Single Responsibility Principle]
    1. 同类型生活用具最好都收在同类型的收纳箱里, 找起来省时,高效,方便。[分类思想]
    2. 注意：不要分得太细， 也不要分得太糙。[过犹不及]

- <b>O</b>开闭原则[Open Closed Principle]

#### <b>编程语言层面</b> ####
> <font color="deepskyblue"><b>最初基于面向对象语言，而后摆脱特定语言的束缚，成为可维护编码的指导思路</b></font>
- 以封装, 抽象, 继承, 多态面向对象编程特性为基础.
- 以指导编写出灵活/可维护的代码为目的.

### <b>设计模式</b> ###
- 结合应用场景和设计原则总结出的一套套解决方案.

### <b>编程规范</b> ###
- 以保证代码的可读性, 可维护性为目的.



