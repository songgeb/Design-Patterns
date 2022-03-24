# 《设计模式之美》之SOLID-I-接口隔离原则

接口隔离原则英文叫做“Interface Segregation Principle”，简称ISP

意思是

> 客户端不应该被强迫依赖它不需要的接口。其中的“客户端”，可以理解为接口的调用者或者使用者

该原则有些抽象，理解它的关键点在于“接口”二字，此处的接口可以理解成如下三种意思：

- 一组API接口集合
- 单个API接口或函数
- OOP中的接口（协议）概念

### 接口理解为一组API集合时

- 比如有一组用户操作接口，比如获取、更新用户信息
- 后面希望增加删除用户功能
- 但删除功能比较严格，不是所有业务方都需要

这种情况就，不能要求所有使用该接口集合的业务方，都看到删除功能

如下，需要将删除功能单独行程一个接口集合进行使用

```
public interface UserService {
  boolean register(String cellphone, String password);
  boolean login(String cellphone, String password);
  UserInfo getUserInfoById(long id);
  UserInfo getUserInfoByCellphone(String cellphone);
}

public interface RestrictedUserService {
  boolean deleteUserByCellphone(String cellphone);
  boolean deleteUserById(long id);
}

public class UserServiceImpl implements UserService, RestrictedUserService {
  // ...省略实现代码...
}
```

### 把接口理解为单个API或函数

这时候，接口隔离的原则是，希望api或函数设计上应该功能单一

你会发现，这种理解好像和单一职责原则很像。确实很像，有一点区别：
- 单一职责原则针对的是模块、类、接口的设计
- 接口隔离原则相对于单一职责原则而言，一方面侧重于接口的设计
- 另一方面，单一职责原则提供了判断接口是否单一职责的标准：通过调用者如何使用来间接的判定。如果调用者只使用接口的部分功能，那说明不够职责单一

### 把接口裂解为OOP中的接口概念

这种情况下，在设计接口（协议）时，方法列表要尽量单一，不要让接口的实现者和调用者，依赖不需要的接口函数

