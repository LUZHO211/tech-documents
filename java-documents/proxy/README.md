# Java三种代理：静态代理、动态代理、CGLIB代理
**代理模式**就是设置一个中间代理来控制访问原目标对象，以达到增强原对象的功能和简化访问方式的目的。代理模式还能达到程序解耦的目的。
## 1 Java静态代理
**`Java`静态代理**的实现，需要**代理类**和**目标对象**实现同样的接口。

- 优点：可以在不修改目标对象的前提下扩展目标对象的功能。
- 缺点：一旦新增接口，代理类和目标对象的类都要修改。

>下面以售票处售票为模型，阐述静态代理模式

- 定义一个售票处的接口

```java
public interface TicketOffice {

    /**
     * 售票接口方法
     */
    void sellTicket();

}
```

- 定义售票处代理类

```java
public class TicketOfficeProxy implements TicketOffice {

    private TicketOffice ticketOffice;

    public TicketOfficeProxy(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }


    @Override
    public void sellTicket() {
        ticketOffice.sellTicket();
    }

    public TicketOffice getTicketOffice() {
        return ticketOffice;
    }

    public void setTicketOffice(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

}
```

- 假设全国各地都有售票处：这里定义分别杭州售票出和上海售票处的代理类

```java
public class HangzhouTicketOffice implements TicketOffice {

    private final static Logger logger = LoggerFactory.getLogger(HangzhouTicketOffice.class);

    @Override
    public void sellTicket() {
        logger.info("Hangzhou ticket office selling a ticket ...");
    }

}
```

```java
public class ShanghaiTicketOffice implements TicketOffice {

    private final static Logger logger = LoggerFactory.getLogger(ShanghaiTicketOffice.class);

    @Override
    public void sellTicket() {
        logger.info("Shanghai ticket office selling a ticket ...");
    }

}
```

- 测试静态代理

```java
public class Launcher {

    public static void main(String[] args) {
        // 杭州售票点代理类
        HangzhouTicketOffice hangzhouTicketOffice = new HangzhouTicketOffice();
        // 上海售票点代理类
        ShanghaiTicketOffice shanghaiTicketOffice = new ShanghaiTicketOffice();

        // 创建代理类：传入杭州代理点
        TicketOfficeProxy ticketOfficeProxy = new TicketOfficeProxy(hangzhouTicketOffice);
        ticketOfficeProxy.sellTicket();

        // 切换成上海代理点
        ticketOfficeProxy.setTicketOffice(shanghaiTicketOffice);
        ticketOfficeProxy.sellTicket();
    }

}
```

测试静态代理所输出的日志为：

```java
16:27:46.215 [main] INFO com.luzho211.proxy.HangzhouTicketOffice - Hangzhou ticket office selling a ticket ...
16:27:46.215 [main] INFO com.luzho211.proxy.ShanghaiTicketOffice - Shanghai ticket office selling a ticket ...
```

- 如果需要扩展功能，例如在售票方法`sellTicket`执行之前添加监控日志，仅修改代理类就能达到目的：

```java
public class TicketOfficeProxy implements TicketOffice {

    private final static Logger logger = LoggerFactory.getLogger(TicketOfficeProxy.class);
    private TicketOffice ticketOffice;

    public TicketOfficeProxy(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }


    @Override
    public void sellTicket() {
        // 在执行sellTicket之前新增日志监控功能
        logger.info("Someone wants to buy a ticket.");
        ticketOffice.sellTicket();
    }

}
```

- 静态代理扩展功能测试

```java
public class Launcher {

    public static void main(String[] args) {
        HangzhouTicketOffice hangzhouTicketOffice = new HangzhouTicketOffice();
        TicketOfficeProxy ticketOfficeProxy = new TicketOfficeProxy(hangzhouTicketOffice);
        ticketOfficeProxy.sellTicket();
    }

}
```

```bash
17:40:40.411 [main] INFO com.luzho211.proxy.HangzhouTicketOffice - Someone wants to buy a ticket.
17:40:40.417 [main] INFO com.luzho211.proxy.HangzhouTicketOffice - Hangzhou ticket office selling a ticket ...
```

可以看出，仅修改代理类也能完成功能的扩展，而不需要修改目标对象类的实现（如果实现类很多，就只能一个个去修改）；也不需要在每个调用目标对象的地方修改。

- 新增退票功能接口方法：因为静态代理下，代理类和目标类都必须实现同样的接口（`TicketOffice`），如果`TicketOffice`接口类新增了接口方法，代理类和所有的目标实现类都必须修改。如果实现类很多，工作量将会很大。

```java
public interface TicketOffice {

    /**
     * 售票接口方法
     */
    void sellTicket();

    /**
     * 退票接口方法
     */
    void refundTicket();
    
}
```

新增了`refundTicket()`接口方法，那么代理类`TicketOfficeProxy.java`和目标实现类`HangzhouTicketOffice.java`和`ShanghaiTicketOffice.java`都要修改。

## 2 Java动态代理

## 3 CGLIB代理
