# 《设计模式之美》之SOLID-D-依赖反转原则

依赖反转原则英文叫做“Dependency Inversion Principle”，简称DIP

也叫做依赖倒置

先从几个容易混淆的概念入手：控制反转、依赖注入

### 控制反转

英文叫做“Inversion Of Control”，简称IOC

那么是什么控制被反转了呢？这个概念的意思是

> 对程序执行流程的控制，反转了，通过引入框架，由原来的开发者来控制，反转成由框架来控制

举个例子

```
public class UserServiceTest {
  public static boolean doTest() {
    // ... 
  }
  
  public static void main(String[] args) {//这部分逻辑可以放到框架中
    if (doTest()) {
      System.out.println("Test succeed.");
    } else {
      System.out.println("Test failed.");
    }
  }
}
```

上面例子是一个单元测试用例，程序员通过编写UserServiceTest类来完成测试。下面来看一下如何通过引入框架实现控制反转

测试框架核心代码如下所示

```
public abstract class TestCase {
  public void run() {
    if (doTest()) {
      System.out.println("Test succeed.");
    } else {
      System.out.println("Test failed.");
    }
  }
  
  public abstract boolean doTest();
}

public class JunitApplication {
  private static final List<TestCase> testCases = new ArrayList<>();
  
  public static void register(TestCase testCase) {
    testCases.add(testCase);
  }
  
  public static final void main(String[] args) {
    for (TestCase case: testCases) {
      case.run();
    }
  }
```

现在，我们使用测试框架完成之前的测试逻辑

```
public class UserServiceTest extends TestCase {
  @Override
  public boolean doTest() {
    // ... 
  }
}

// 注册操作还可以通过配置的方式来实现，不需要程序员显示调用register()
JunitApplication.register(new UserServiceTest();
```

- 控制反转是一种思想，而非具体的编程技巧
- 依赖注入就是一种实现了控制反转思想的编程技巧

### 依赖注入

英文叫做“Dependency Injection”，简称DI

依赖注入特别容易理解和应用，通过代码一看便知

```

// 非依赖注入实现方式
public class Notification {
  private MessageSender messageSender;
  
  public Notification() {
    this.messageSender = new MessageSender(); //此处有点像hardcode
  }
  
  public void sendMessage(String cellphone, String message) {
    //...省略校验逻辑等...
    this.messageSender.send(cellphone, message);
  }
}

public class MessageSender {
  public void send(String cellphone, String message) {
    //....
  }
}
// 使用Notification
Notification notification = new Notification();

// 依赖注入的实现方式
public class Notification {
  private MessageSender messageSender;
  
  // 通过构造函数将messageSender传递进来
  public Notification(MessageSender messageSender) {
    this.messageSender = messageSender;
  }
  
  public void sendMessage(String cellphone, String message) {
    //...省略校验逻辑等...
    this.messageSender.send(cellphone, message);
  }
}
//使用Notification
MessageSender messageSender = new MessageSender();
Notification notification = new Notification(messageSender);
```

一句话，通过函数传参代替内部创建对象，就是依赖注入

### 依赖反转原则

现在进入正题，依赖反转的意思是
> 高层模块（high-level modules）不要依赖低层模块（low-level）。高层模块和低层模块应该通过抽象（abstractions）来互相依赖。除此之外，抽象（abstractions）不要依赖具体实现细节（details），具体实现细节（details）依赖抽象（abstractions）

高层模块、低层模块：在调用链上，调用者属于高层，被调用者属于低层

- 在上层业务开发中，高层模块调用低层模块其实很普遍，也并没有问题
- 而DIP为什么要求高层不能依赖低层呢，是因为这个原则主要是给框架设计用的

比如Tomcat就使用了DIP思想

- Tomcat是一个web应用的容器
- web应用程序放到Tomcat指定的目录下，Tomcat就可以执行web应用
- Tomcat作为调用方，是高层模块；所以web应用就是低层模块
- Tomcat并没有直接依赖某个具体的web应用，同样web应用也并不依赖Tomcat代码
- 两者共同依赖了Servlet规范，web应用按照该规范在指定的API中处理http请求，在指定的API中将响应发送给请求方；Tomcat根据Servlet规范，创建web应用实例、启动web应用等