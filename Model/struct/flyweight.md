## <b>享元模式</b> ##
> 作用: 减少内存中重复数据的开销

### <b>应用例子: 在线棋局游戏</b> ###
- 问题: 千百万人在线游戏的会在内存中生成重复的棋子大小颜色等数据数据？
- 解决:
    ```java
	//  棋盘 [chessbroad]
	//	 - 棋子 [ChessPiece]
	//	 	- 棋子的属性 [ChessPieceUnit] - 棋子属性静态工厂 [ChessPieceUnitFactory - 静态享元] 
	// 		- 棋子的位置 [X-Y]

    // 享元类 - [多余重复的属性]
    public class ChessPieceUnit {
      private int id;
      private String text;
      private Color color;

      public ChessPieceUnit(int id, String text, Color color) {
        this.id = id;
        this.text = text;
        this.color = color;
      }

      public static enum Color {
        RED, BLACK
      }

      // 共享的不可修改对象, 所以不能有setter方法.
      // ...省略其他属性和getter方法...
    }

    public class ChessPieceUnitFactory {
      private static final Map<Integer, ChessPieceUnit> pieces = new HashMap<>();

      // 通过静态实现共享 - 单例
      static {
        pieces.put(1, new ChessPieceUnit(1, "車", ChessPieceUnit.Color.BLACK));
        pieces.put(2, new ChessPieceUnit(2,"馬", ChessPieceUnit.Color.BLACK));
        //...省略摆放其他棋子的代码...
      }

      public static ChessPieceUnit getChessPiece(int chessPieceId) {
        return pieces.get(chessPieceId);
      }
    }

    public class ChessPiece {
      private ChessPieceUnit chessPieceUnit; // 静态对象
      private int positionX;
      private int positionY;

      public ChessPiece(ChessPieceUnit unit, int positionX, int positionY) {
        this.chessPieceUnit = unit;
        this.positionX = positionX;
        this.positionY = positionY;
      }
      // 省略getter、setter方法
    }

    public class ChessBoard {
      private Map<Integer, ChessPiece> chessPieces = new HashMap<>();

      public ChessBoard() {
        init();
      }

      private void init() {
        chessPieces.put(1, new ChessPiece(
                ChessPieceUnitFactory.getChessPiece(1), 0,0));
        chessPieces.put(1, new ChessPiece(
                ChessPieceUnitFactory.getChessPiece(2), 1,0));
        //...省略摆放其他棋子的代码...
      }

      public void move(int chessPieceId, int toPositionX, int toPositionY) {
        //...省略...
      }
    }
    ```

### <b>应用例子: java中的integer</b> ###
    ```java
    Integer i1 = 56;
    Integer i2 = 56;
    Integer i3 = 129;
    Integer i4 = 129;
    System.out.println(i1 == i2); // true
    System.out.println(i3 == i4); // false - 不是同一对象
   
    /*  解析: 
     *  0. java通过自动装/拆包机制将的基础类型封装成对象.
     *  1. "==":指的是指向同一个对象.
     *  2. java在jdk中对-127~128使用了享元模式优化了内存. 
     */
    ```

### <b>应用例子: java中的string</b> ###
    ```java
    String s1 = "小争哥";
    String s2 = "小争哥";
    String s3 = new String("小争哥");

    System.out.println(s1 == s2);   // true
    System.out.println(s1 == s3);   // false

    /*  Integer 类中要共享的对象，是在类加载的时候，就集中一次性创建好的
     *  对于字符串，没法事先知道要共享哪些字符串常量，所以没办法事先创建好
     *  只能在某个字符串常量第一次被用到的时候，存储到常量池中，之后再用时直接引用, 不需要再重新创建。
     */
    ```












