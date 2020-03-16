## <b>桥接模式</b> ##
> Decouple an abstraction from its implementation so that the two can vary independently 

### <b>桥接模式理解</b> ###
- 将抽象和实现解耦，让它们可以独立变化
- 一个类存在多个独立变化的维度，通过组合的方式，让这多个维度可以独立进行扩展。

### <b>代码实现</b> ###
    ```java
    // 应用场景：不同类型，不同渠道消息的发送。

    public interface MsgSender {
      void send(String message);
    }

    public class TelephoneMsgSender implements MsgSender {
      private List<String> telephones;

      public TelephoneMsgSender(List<String> telephones) {
        this.telephones = telephones;
      }

      @Override
      public void send(String message) {
        //...
      }

    }

    public class EmailMsgSender implements MsgSender {
      // 与TelephoneMsgSender代码结构类似，所以省略...
    }

    public class WechatMsgSender implements MsgSender {
      // 与TelephoneMsgSender代码结构类似，所以省略...
    }

    public abstract class Notification {
      protected MsgSender msgSender;

      public Notification(MsgSender msgSender) {
        this.msgSender = msgSender;
      }

      public abstract void notify(String message);
    }

    public class SevereNotification extends Notification {
      public SevereNotification(MsgSender msgSender) {
        super(msgSender);
      }

      @Override
      public void notify(String message) {
        msgSender.send(message);
      }
    }

    public class UrgencyNotification extends Notification {
      // 与SevereNotification代码结构类似，所以省略...
    }
    public class NormalNotification extends Notification {
      // 与SevereNotification代码结构类似，所以省略...
    }
    public class TrivialNotification extends Notification {
      // 与SevereNotification代码结构类似，所以省略...
    }
    ```

