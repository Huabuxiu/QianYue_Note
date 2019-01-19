## Spring的bean管理（xml方式）

#### bean实例化的方式

**1.在spring里面通过配置文件创建对象**

**2.bean实例化三种方式实现**
第一种：使用类的 *无参数构造方法* 创建（重点）
*参照上面的 Spring入门案例*
**注：类里面如果没有无参数的构造方法，会出现异常**

第二种：使用静态工厂创建 **(开发中一般不用)**
（1）创建静态的方法，返回类的对象

**Bean：**

```java
public class Bean {
    public Bean() {
    }

    public void add() {
        System.out.println("bean的add方法....");
    }
}
```

**BeanFactory：**

```java
public class BeanFactory {
    //静态的方法，返回bean的对象
    public static Bean getBean() {
        return new Bean();
    }
}
```

**xml配置：**

```xml
<!--demo2   使用静态工厂创建对象-->
    <bean id="bean" class="cn.blinkit.demo2.BeanFactory" factory-method="getBean"></bean>
```

**测试代码**

```java
public class BeanTest {
    @Test
    public void beanTest() {
        //1.加载spring配置文件，根据创建对象
        ApplicationContext context = new ClassPathXmlApplicationContext("spring-config.xml");
        //2.得到配置创建的对象
        Bean bean = (Bean)context.getBean("bean");
        System.out.println(bean);
        bean.add();
    }
}
```

第三种：使用实例工厂创建 **(开发中一般不用)**
（2）创建不是静态的方法
**Bean和上面的一样**

**BeanFactory：**

```java
public class BeanFactory2 {
    //普通方法，返回Bean对象
    public Bean getBean() {
        return new Bean();
    }
}
```

**xml配置**

```xml
<!--demo2   使用实例工厂创建对象-->
    <!-- 创建工厂对象 -->
    <bean id="BeanFactory2" class="cn.blinkit.demo2.BeanFactory2"></bean>
    <bean id="bean2" factory-bean="BeanFactory2" factory-method="getBean"></bean>
```

**测试：**

```java
public class BeanTest2 {
    @Test
    public void beanTest2() {
        //1.加载spring配置文件，根据创建对象
        ApplicationContext context = new ClassPathXmlApplicationContext("spring-config.xml");
        //2.得到配置创建的对象
        Bean bean = (Bean)context.getBean("bean2");
        System.out.println(bean);
        bean.add();
    }
}
```



### Bean标签常用属性

**1. id属性**

- 起的名字，id属性值名称任意命名
- id属性值不能包含特殊符号
- 根据id得到配置对象

**2. class属性**

- 创建对象所在类的全路径

**3. name属性**

- 功能和id属性一样，id属性值不能包含特殊符号，但是在name值里面可以包含特殊符号

**4. scope属性**

- singleton： spring默认的，单例的

  - scope="singleton"

- prototype：多例的

  - scope="prototype"

- request：创建对象把对象放到request域里面

  - WEB项目中，Spring创建一个Bean的对象，将对象存入到request域中。

- session：创建对象把对象放到session域里面

  - WEB项目中，Spring创建一个Bean的对象，将对象存入到session域中。

- globalSession：

  全局session(单点登录)

   

  创建对象把对象放到globalSession域里面

  - WEB项目中，**应用在Porlet环境**，如果没有Porlet环境那么globalSession相当于session。

