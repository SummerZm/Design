## <b>状态机 - 分析问题思路</b> ##

### <b>1. 状态机的组成要件</b> ###
> <font color="deepskyblue"><b>确定问题处理对象</b></font>
- 起点 [输入]
- 状态 [识别问题关键对象和对象状态]
- 条件 [问题的边界条件]
- 动作 [触发状态发生迁跃]
- 终点 [输出]

### <b>2. 状态机的分析思路</b> ###
> <font color="deepskyblue"><b>确定问题处理对象后，状态表可以清晰表示问题全貌, 方便查漏补缺</b></font>
- 横坐标为初始状态，纵坐标为的触发动作, 交叉位置为结束状态。
- 横坐标为结束状态，纵坐标为的初始动作, 交叉位置为触发动作。
- 横坐标为结束状态，纵坐标为的触发动作, 交叉位置为初始状态。
    <b>注：上述可见确定问题对象后需要确定状态[初始/结束]和动作的列表M</b>


### <b>3. 状态机的实现思路</b> ###
> <font color="deepskyblue"><b>不同的实现蕴含着对问题的不同切入角度</b></font>

- <b>在状态中判断事件</b>
    ```C
        while(condition)
        {
            int state = 1;
            switch(state)
            {
                // 隐含着事件判断优先级的逻辑
                case state1:
                {
                    if(condition1) {
                        state = 1;
                    }
                    else if(condition2) {
                        state = 2;
                    }
                    break;
                }
                case state2:
                case state3:
                    ...
            }
        }
    ```
- <b>在事件中判断状态</b>
    ```C
        void (*nextstate)();
        void fsmState2();
        void fsmState3();

        void fsmState1() {
            ....
            nextstate =  fsmState3();
        }

        int main()
        {
            if(nextstate) (*nextstate)();
            return 0;
        }
    ```
### <b>其他相关</b> ###
> <font color="deepskyblue"><b>状态跃迁时需要考虑临界条件</b></font>

 1. switch/case 最原始的实现方式,是很多的c程序员习惯采用的方式。
 2. 查找表[状态、事件、动作],稍微做了一点改进。有点类似MFC的雏形。
    ```
    用一个栈结构，激活的状态位于栈顶，自动的映射事件和动作的对应
    ```
 3. 面向对象的设计下、灵活运用模式。
