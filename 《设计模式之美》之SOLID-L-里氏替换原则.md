# 《设计模式之美》之里氏替换原则

里氏替换原则，英文叫做`Liskov Substitution Principle`，简称`LSP`

英文原话是

> Functions that use pointers of references to base classes must be able to use objects of derived classes without knowing it。

中文描述

> 子类对象（object of subtype/derived class）能够替换程序（program）中父类对象（object of base/parent class）出现的任何地方，并且保证原来程序的逻辑行为（behavior）不变及正确性不被破坏


## LSP VS 多态

看意思，感觉LSP和多态很像，那区别是什么？

- 有一个`Transporter`类，核心方法是`sendRequest`，发送请求并拿到响应数据
- 有一个子类`SecurityTransporter `，重写了`sendRequest`，增加了appid和token校验逻辑

```
public class Transporter {
  private HttpClient httpClient;
  
  public Transporter(HttpClient httpClient) {
    this.httpClient = httpClient;
  }

  public Response sendRequest(Request request) {
    // ...use httpClient to send request
  }
}

public class SecurityTransporter extends Transporter {
  private String appId;
  private String appToken;

  public SecurityTransporter(HttpClient httpClient, String appId, String appToken) {
    super(httpClient);
    this.appId = appId;
    this.appToken = appToken;
  }

  @Override
  public Response sendRequest(Request request) {
    if (StringUtils.isNotBlank(appId) && StringUtils.isNotBlank(appToken)) {
      request.addPayload("app-id", appId);
      request.addPayload("app-token", appToken);
    }
    return super.sendRequest(request);
  }
}

public class Demo {    
  public void demoFunction(Transporter transporter) {    
    Reuqest request = new Request();
    //...省略设置request中数据值的代码...
    Response response = transporter.sendRequest(request);
    //...省略其他逻辑...
  }
}

// 里式替换原则
Demo demo = new Demo();
demo.demofunction(new SecurityTransporter(/*省略参数*/););
```

该例子中，子类`SecurityTransporter `对便是符合LSP原则的

再看下面例子

```
// 改造前：
public class SecurityTransporter extends Transporter {
  //...省略其他代码..
  @Override
  public Response sendRequest(Request request) {
    if (StringUtils.isNotBlank(appId) && StringUtils.isNotBlank(appToken)) {
      request.addPayload("app-id", appId);
      request.addPayload("app-token", appToken);
    }
    return super.sendRequest(request);
  }
}

// 改造后：
public class SecurityTransporter extends Transporter {
  //...省略其他代码..
  @Override
  public Response sendRequest(Request request) {
    if (StringUtils.isBlank(appId) || StringUtils.isBlank(appToken)) {
      throw new NoAuthorizationRuntimeException(...);
    }
    request.addPayload("app-id", appId);
    request.addPayload("app-token", appToken);
    return super.sendRequest(request);
  }
}
```

改造后的`SecurityTransporter `便**不符合LSP原则**

因为改造后的`sendRequest`方法使得`SecurityTransporter `不能保证原程序逻辑的前提下替换父类对象的位置

- 父类中`sendRequest`总是会发送请求，而子类中如果appid或token有问题，则会抛出异常而不会发送请求

所以

- 多态只是一个具体的语言特性；而LSP则是一种编程思想
- 多态并不等同于LSP
- LSP通常要依赖多态的特性去实现

## 常见的违背LSP情况

1. 子类违背父类声明要实现的功能
2. 子类违背对输入、输出、异常的约定
3. 子类违背父类注释中所罗列的任何特殊说明

## 总结

LSP就是一种父类、子类设计思想，一种使得子类能够完美替换父类的思想