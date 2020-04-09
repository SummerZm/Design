## 状态模式 ##

### <b>状态机简介</b> ####
- 状态模式是状态机的一种实现方式
- 状态机又叫有限状态机，它有 3 个部分组成：状态、事件、动作。
- 事件也称为转移条件。事件触发状态的转移及动作的执行。
- 动作不是必须的，也可能只转移状态，不执行任何动作。
- 状态机三种实现方式
    1. 分支逻辑法：利用 if-else 或者 switch-case 分支逻辑


    2. 查表法：通过二维数组来表示状态转移图，能极大地提高代码的可读性和可维护性
        ```java
        // 事件枚举: 表头
        public enum Event {
            GOT_MUSHROOM(0),
            GOT_CAPE(1),
            GOT_FIRE(2),
            MET_MONSTER(3);

            private int value;

            private Event(int value) {
                this.value = value;
            }

            public int getValue() {
                return this.value;
            }
        }

        // 状态枚举
        public enum State {
            SMALL(0),
            SUPER(1),
            CAPE(2),
            FIRE(3);

            private int value;

            private State(int value) {
                this.value = value;
            }

            public int getValue() {
                return this.value;
            }
        }

        public class MarioStateMachine {
            private int score;
            private State currentState;

            private static final State[][] transitionTable = {
                {SUPER, CAPE, FIRE, SMALL},
                {SUPER, CAPE, FIRE, SMALL},
                {CAPE, CAPE, CAPE, SMALL},
                {FIRE, FIRE, FIRE, SMALL}
            };

            private static final int[][] actionTable = {
                {+100, +200, +300, +0},
                {+0, +200, +300, -100},
                {+0, +0, +0, -200},
                {+0, +0, +0, -300}
            };

            public MarioStateMachine() {
                this.score = 0;
                this.currentState = State.SMALL;
            }

            public void obtainMushRoom() {
                executeEvent(Event.GOT_MUSHROOM);
            }

            public void obtainCape() {
                executeEvent(Event.GOT_CAPE);
            }

            public void obtainFireFlower() {
                executeEvent(Event.GOT_FIRE);
            }

            public void meetMonster() {
                executeEvent(Event.MET_MONSTER);
            }

            private void executeEvent(Event event) {
                int stateValue = currentState.getValue();
                int eventValue = event.getValue();
                this.currentState = transitionTable[stateValue][eventValue];
                this.score = actionTable[stateValue][eventValue];
            }

            public int getScore() {
                return this.score;
            }

            public State getCurrentState() {
                return this.currentState;
            }
        }
        /*
            MarioStateMachine M = new MarioStateMachine();
            M.obtainMushRoom();
        */
        ```

    3. 状态模式：适合状态并多、状态转移比较简单，事件触发执行的动作里包含比较复杂业务逻辑的状态机
        ```java
        // 缺点：添加新事件，所有的类都需要修改
        // 双向依赖：修改自己
        public interface IMario {
            State getName();
            void obtainMushRoom(MarioStateMachine stateMachine);
            void obtainCape(MarioStateMachine stateMachine);
            void obtainFireFlower(MarioStateMachine stateMachine);
            void meetMonster(MarioStateMachine stateMachine);
        }

        public class SmallMario implements IMario {
            private static final SmallMario instance = new SmallMario();
            private SmallMario() {}
            public static SmallMario getInstance() {
                return instance;
            }

            @Override
            public State getName() {
                return State.SMALL;
            }

            @Override
            public void obtainMushRoom(MarioStateMachine stateMachine) {
                stateMachine.setCurrentState(SuperMario.getInstance());
                stateMachine.setScore(stateMachine.getScore() + 100);
            }

            @Override
            public void obtainCape(MarioStateMachine stateMachine) {
                stateMachine.setCurrentState(CapeMario.getInstance());
                stateMachine.setScore(stateMachine.getScore() + 200);
            }

            @Override
            public void obtainFireFlower(MarioStateMachine stateMachine) {
                stateMachine.setCurrentState(FireMario.getInstance());
                stateMachine.setScore(stateMachine.getScore() + 300);
            }

            @Override
            public void meetMonster(MarioStateMachine stateMachine) {
                // do nothing...
            }
        }

        // 省略SuperMario、CapeMario、FireMario类...
        public class MarioStateMachine {
            private int score;  // 统计数据
            private IMario currentState;    // 不同状态的操作

            public MarioStateMachine() {
                this.score = 0;
                this.currentState = SmallMario.getInstance();
            }

            public void obtainMushRoom() {
                this.currentState.obtainMushRoom(this);
            }

            public void obtainCape() {
                this.currentState.obtainCape(this);
            }

            public void obtainFireFlower() {
                this.currentState.obtainFireFlower(this);
            }

            public void meetMonster() {
                this.currentState.meetMonster(this);
            }

            public int getScore() {
                return this.score;
            }

            public State getCurrentState() {
                return this.currentState.getName();
            }

            public void setScore(int score) {
                this.score = score;
            }

            public void setCurrentState(IMario currentState) {
                this.currentState = currentState;
            }
        }

        /*
            MarioStateMachine M = new MarioStateMachine();
            M.setCurrentState(SmallMario.getInstance());
            M.obtainMushRoom();
        */
        ```