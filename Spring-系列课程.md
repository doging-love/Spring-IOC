##  第一章 引言

### 1.EJB存在的问题

<img src="D:%5C%E7%AC%94%E8%AE%B0%5CSpring-%E7%B3%BB%E5%88%97%E8%AF%BE%E7%A8%8B%5Cimage-20201116184935304.png" alt="image-20201116184935304" style="zoom:50%;float:left;" />

### 2.什么是Spring

```markdown
Spring是一个轻量级的JavaEE解决方案，整合众多优秀的设计模式
```

-   轻量级

    ```markdown
    1. 运行环境是没有额外要求的
    	开源：tomcat resion jetty
    	收费：weblogic websphere
    2. 代码移植性高
    	不需要实现额外接口
    ```

-   JavaEE的解决方案

    <img src="D:%5C%E7%AC%94%E8%AE%B0%5CSpring-%E7%B3%BB%E5%88%97%E8%AF%BE%E7%A8%8B%5Cimage-20201116185028089.png" alt="image-20201116185028089" style="zoom:50%;float:left;" />

-   整合设计模式

    ```markdown
    1. 工厂设计模式
    2. 代理设计模式
    3. 模板设计模式
    4. 策略设计模式
    ```

### 3.设计模式

```markdown
1. 广义概念
面向对象设计中解决特定问题的经典代码
2. 狭义概念
GOF4人帮定义的23中设计模式：工厂、适配器、装饰器、门面、代理、模板...
```

### 4.工厂设计模式

#### 4.1 什么是工厂设计模式

```markdown
1. 概念：通过工厂类，创建对象
	User user = new User();
	UserDao userDao = new UserDAOImpl();
2. 好处：解耦合
  耦合：指的是代码间的强关联关系，一方改变会影响另一方
  问题：不利于代码维护
  简单：把接口的实现类，硬编码在程序中
  	UserService userService = new UserServiceImpl(); 
```

#### 4.2 简单工厂设计

```java
package com.nfhao.basic;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

/**
 * @description:
 * @author: 小聂同学
 * @create: 2020-11-17 14:48
 */

public class BeanFactory {
    private static Properties env = new Properties();

    static {
        try {
            //第一步 获得IO输入流
            InputStream inputStream = BeanFactory.class.getResourceAsStream("/applicationContext.properties");
            //第二步 文件内容 封装 Properties集合中 key = userService value = com.nfhao.basic.UserServiceImpl
            env.load(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /*
        对象的创建方式：
            1.直接调用构造方法 创建对象 UserServer userService = new UserServiceImpl();
            2.通过调用反射的方式 创建对象 解耦合
            Class clazz = Class.forName("com.nfhao.basic");
            User
     */
    public static UserService getUerService(){
        UserService userService = null;
        try {
            Class clazz = Class.forName(env.getProperty("userService"));
            userService = (UserService) clazz.newInstance();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
        return userService;
    }

    public static UserDAO getUserDAO(){
        UserDAO userDAO = null;
        try {
            Class clazz = Class.forName(env.getProperty("userDAO"));
            userDAO = (UserDAO) clazz.newInstance();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
        return userDAO;
    }
}
```

#### 4.3 通用工厂设计

-   问题

    ```markdown
    简单工厂会存在大量代码冗余
    ```

    ![image-20201119165755940](D:%5C%E7%AC%94%E8%AE%B0%5CSpring-%E7%B3%BB%E5%88%97%E8%AF%BE%E7%A8%8B%5Cimage-20201119165755940.png)

-   通用工厂的代码

    ```java
    package com.nfhao.basic;
    import java.io.IOException;
    import java.io.InputStream;
    import java.util.Properties;
    /**
     * @description:创建一切想要的对象
     * @author: 小聂同学
     * @create: 2020-11-17 14:48
     */
    public class BeanFactory {
        private static Properties env = new Properties();
        static {
            try {
                //第一步 获得IO输入流
                InputStream inputStream = BeanFactory.class.getResourceAsStream("/applicationContext.properties");
                //第二步 文件内容 封装 Properties集合中 key = userService value = com.nfhao.basic.UserServiceImpl
                env.load(inputStream);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        /**
        * @description: 通用工厂
        * @author 小聂同学
        * @dateTime: 2020/11/19 17:32
        * @param: [key]
        * @return java.lang.Object
        */
        public static Object getBean(String key){
            Object obj = null;
            try {
                Class clazz = Class.forName(env.getProperty(key));
                obj = clazz.newInstance();
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            } catch (InstantiationException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            }
            return obj;
        }
    }
    ```

#### 4.4 通用工厂使用方式

```markdown
1. 定义类型 (类)
2. 通过配置文件的配置告知工厂(applicationContext.properties)
   key = value
3. 通过工厂获得类的对象
   Object obj = BeanFactory.getBean("key");
```

### 5.总结

```markdown
Spring本质：工厂 ApplicationContext(applicationContext.xml)
```

## 第二章 第一个Spring程序

### 1.软件版本

```markdown
1. JDK1.8+
2. Maven3.6+
3. IDEA2018+
4. SpringFramework5.1.4
   官方网站 www.spring.io
Maven3.6+IDEA2019(容易出问题)
```

### 2.环境搭建

-   Spring相关的jar包

    ```xml
    <!-- 设置pom 依赖 -->
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.1.14.RELEASE</version>
    </dependency>
    ```

-   Spring相关的配置文件

    ```markdown
    1. 配置文件的放置位置: 任意位置 没硬性要求
    2. 配置文件的命名   : 没有硬性要求 建议: applicationContext.xml
    
    思考: 日后应用Spring框架时，需要进行配置文件路径的设置。
    ```

    ![image-20201119180710695](D:%5C%E7%AC%94%E8%AE%B0%5CSpring-%E7%B3%BB%E5%88%97%E8%AF%BE%E7%A8%8B%5Cimage-20201119180710695.png)

### 3.Spring的核心API

-   ApplicationContext

    ```markdown
    作用：Spring提供的ApplicationContext这个工厂，用于对象的创建
    好处：解耦合
    ```

    -   ApplicationContext接口类型

        ```markdown
        接口：屏蔽实现的差异
        非web环境：ClassPathXmlApplicationContext (main junit)
        web环境  ：XmlWebApplicationContext
        ```

        ![image-20201119184532090](D:%5C%E7%AC%94%E8%AE%B0%5CSpring-%E7%B3%BB%E5%88%97%E8%AF%BE%E7%A8%8B%5Cimage-20201119184532090.png)

    -   重量级资源

        ```markdown
        ApplicationContext工厂的对象占用大量内存
        不会频繁的创建对象:一个应用只会创建一个工厂对象
        AoolicationContext工厂：一定是线程安全的(多线程并发访问)
        ```

### 4.程序开发

```markdown
1. 创建类型
2. 配置文件的配置 applicationContext.xml
	<bean id="person" class="com.nfhao.basic.Person"/>
3. 通过工厂类，获得对象
   ApplicationContext
   		|-ClassPathXmlApplicationContext
    ApplicationContext ctx = new ClassPathApplicationContext("applicationContext.xml");
    Person person = (Person) ctx.getBean("person");
```

### 5.细节分析

-   名词解释

    ```markdown
    Spring工厂创建的对象，叫做bean或者组件(componet)
    ```

-   Spring工厂的相关方法

    ~~~java
    //1.第一种重构
    Person person = (Person) ctx.getBean("person");
    System.out.println(person);
    
    //2.第二种重构 通过这种方式获得对象就不需要强制类型转换了
    Person person1 = ctx.getBean("person", Person.class);
    System.out.println(person1);
    
    //3.第三种重构 当前Spring的配置文件中只能有一个<bean class是Person类型
    Person person2 = ctx.getBean(Person.class);
    System.out.println(person2);
    
    //获取的是 Spring工厂配置文件中所有的bean标签的id值 person person1
    String[] beanDefinitionNames = ctx.getBeanDefinitionNames();
    for (String beanDefinitionName : beanDefinitionNames) {
        System.out.println("beanDefinitionName:"+beanDefinitionName);
    }
    
    //根据类型来获得String配置文件中对应的id值
    String[] beanNamesForType = ctx.getBeanNamesForType(Person.class);
    for (String s : beanNamesForType) {
        System.out.println("id:"+s);
    }
    
    //判断是否存在指定id值的Bean
    boolean person4 = ctx.containsBeanDefinition("person");
    System.out.println(person4);
    
    //判断是否存在指定id值的Bean
    boolean person5 = ctx.containsBean("person");
    System.out.println(person5);
    ~~~

-   配置文件中需要注意的细节

    ```markdown
    1. 只配置class属性  
    <bean id="" class="" />
    a)上述这种配置 有没有id值 com.nfhao.basic.Person#0
    b)应用场景：如果这个bean只需要使用一次，那么就可以省略id值
    			如果这个bean会使用多次，或者被其他bean引用则需要设置id值
    			
    			
    2. name属性
    作用：用于在Spring的配置文件中为Bean对象定义别名(小名)
    相同：
    	1. ctx.getBean("id|name"); --->Object
    	2. <bean id="" class="" />
    		等效
    		<bean name="" class="" />
    区别：
       1. 别名可以定义多个，但是id属性只能定义一个值
       2. XML的id属性值，命名要求：必须以字母开头，字母 数字 下划线 连字符 不能以特殊字符开头 /person
       		name属性的值，命名没有要求 /person
       		name属性会应用在特殊命名的场景下： /person
       	
       		XML发展到了今天：ID属性的限制不存在 /person
        3. 代码
      	    //判断是否存在指定id值的Bean 也可以判断name值
            boolean person2 = ctx.containsBeanDefinition("perspn");
            System.out.println(person2);
            
             //判断是否存在指定id值的Bean 不可以判断name值
            boolean person3 = ctx.containsBean("p");
            System.out.println(person3);
    ```
    

### 6.Spring工厂的底层原理(简易版)

**Spring工厂**

![image-20201120161319461](D:%5C%E7%AC%94%E8%AE%B0%5CSpring-%E7%B3%BB%E5%88%97%E8%AF%BE%E7%A8%8B%5Cimage-20201120161319461.png)

### 7.思考

```markdown
问题：未来在开发过程中，是不是所有的对象，都会交给Spring工厂来创建呢？
回答：理论上是， 但是有特例 ：实体对象(entity)是不会交给Spring创建，它是持久层框架进行创建。
```

## 第三章 Spring5.x与日志框架的整合

```markdown
Spring与日志框架进行整合，日志框架就可以在控制台输出Spring框架运行过程中的一些重要的信息。
好处：便于了解Spring框架的运行过程，利于程序的调试
```

-   Spring如何整合日志框架

    ```markdown
    默认
        Spring1.2.3早期都是与cpmmons-logging.jar
        Spring5.x默认整合的日志框架 logback log4j2
        
    Spring5.x整合log4j
    	1. 引入log4j jar包
    	2. 引入log4j.perproties配置文件
    
    ```

    -   pom

        ```xml
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.25</version>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
        ```

    -   log4j.properties
    
        ```properties
        log4j.rootLogger=debug,stdout,info,debug,warn,error
        
        #console
        log4j.appender.stdout=org.apache.log4j.ConsoleAppender
        log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
        log4j.appender.stdout.layout.ConversionPattern= [%d{yyyy-MM-dd HH:mm:ss,SSS a}]:%p %l%m%n
        #info log
        log4j.logger.info=info
        log4j.appender.info=org.apache.log4j.DailyRollingFileAppender
        log4j.appender.info.DatePattern='_'yyyy-MM-dd'.log'
        log4j.appender.info.File=./src/com/hp/log/info.log
        log4j.appender.info.Append=true
        log4j.appender.info.Threshold=INFO
        log4j.appender.info.layout=org.apache.log4j.PatternLayout
        log4j.appender.info.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
        #debug log
        log4j.logger.debug=debug
        log4j.appender.debug=org.apache.log4j.DailyRollingFileAppender
        log4j.appender.debug.DatePattern='_'yyyy-MM-dd'.log'
        log4j.appender.debug.File=./src/com/hp/log/debug.log
        log4j.appender.debug.Append=true
        log4j.appender.debug.Threshold=DEBUG
        log4j.appender.debug.layout=org.apache.log4j.PatternLayout
        log4j.appender.debug.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss:SSS a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
        #warn log
        log4j.logger.warn=warn
        log4j.appender.warn=org.apache.log4j.DailyRollingFileAppender
        log4j.appender.warn.DatePattern='_'yyyy-MM-dd'.log'
        log4j.appender.warn.File=./src/com/hp/log/warn.log
        log4j.appender.warn.Append=true
        log4j.appender.warn.Threshold=WARN
        log4j.appender.warn.layout=org.apache.log4j.PatternLayout
        log4j.appender.warn.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
        #error
        log4j.logger.error=error
        log4j.appender.error = org.apache.log4j.DailyRollingFileAppender
        log4j.appender.error.DatePattern='_'yyyy-MM-dd'.log'
        log4j.appender.error.File = ./src/com/hp/log/error.log
        log4j.appender.error.Append = true
        log4j.appender.error.Threshold = ERROR
        log4j.appender.error.layout = org.apache.log4j.PatternLayout
        log4j.appender.error.layout.ConversionPattern = %d{yyyy-MM-dd HH:mm:ss a} [Thread: %t][ Class:%c >> Method: %l ]%n%p:%m%n
        ```

## 第四章 注入(injection)

### 1.什么是注入

```markdown
通过Spring的工厂及配置文件，为所创建对象的成员变量赋值
```

#### 1.1 为什么需要注入

通过编码的方式，为成员变量进行赋值，存在耦合

![image-20201121135541379](D:%5C%E7%AC%94%E8%AE%B0%5CSpring-%E7%B3%BB%E5%88%97%E8%AF%BE%E7%A8%8B%5Cimage-20201121135541379.png)

#### 1.2 如何进行注[开发步骤]

-   类的成员变量提供set get方法

-   配置spring的配置文件

    ```xml
    <bean id="person" class="com.nfhao.basic.Person">
        <property name="id">
            <value>10</value>
        </property>
        <property name="name">
            <value>哈哈</value>
        </property>
    </bean>
    ```

#### 1.3 注入的好处

```markdown
解耦合
```

### 2.Spring注入的原理（简易版）

**Spring底层会通过调用对象属性对应的set方法，完成变量的赋值**

```markdown
Spring注入的理分析
1. <bean id="account" class="xxx.Account">
	等效于 Account account = new Account();
2. <property name="name">
       <value>xiaonie</value>
   </property>
   	等效于account.setName("xiaonie");
3. <property name="password">
       <value>abcde</value>
   </property>
   	等效于account.setName("abcde");
测试：当实体类Account中缺少set方法会出现异常。
```

![image-20201122110633117](Spring-系列课程/image-20201122110633117.png)

## 第五章 set注入详解

```markdown
针对于不同类型的成员变量，在<property>标签，需要嵌套其他标签
<property>
	******
	******
	******
</property>
```

![image-20201122112141859](Spring-系列课程/image-20201122112141859.png)

### 1.JDK内置类型

#### 1.1 String+8种基本类型

```xml
<property name="id">
    <value>10</value>
</property>
<property name="name">
    <value>哈哈</value>
</property>
```

#### 1.2 数组

```xml
<property name="emails">
    <list>
        <value>502006690@qq.com</value>
        <value>xiaonie@qq.com</value>
    </list>
</property>
```

#### 1.3 Set集合

```xml
<property name="tels">
    <set>
        <value>13845678912</value>
        <value>15678945634</value>
        <value>17899754681</value>
        <value>17899754681</value>
        <value>17899754681</value>
    </set>
</property>
<set>
	<ref bean=""></ref>
    <value></value>
</set>
```

#### 1.4 List集合

```xml
<property name="addresses">
    <list>
        <value>蜀山区</value>
        <value>望江西路</value>
        <value>555号</value>
        <value>安徽新华学院</value>
    </list>
</property>
<list>
	<value></value>
    <ref bean=""></ref>
    <list></list>
</list>
```

#### 1.5 Map集合

```xml
<property name="qqs">
    <map>
        <entry>
            <key>
                <value>xiaonie</value>
            </key>
            <value>502006690</value>
        </entry>
        <entry>
            <key>
                <value>小聂</value>
            </key>
            <value>10001</value>
        </entry>
    </map>
</property>
```

#### 1.6 Properties

```markdown
Properties类型 特殊的Map key=String value=String
```

```xml
        <property name="p">
            <props>
                <prop key="13栋">436</prop>
                <prop key="实验四">205</prop>
            </props>
        </property>
```

#### 1.7 复杂的JDK类型

```markdown
需要程序员自定义类型转换器 处理
```

### 2.用户自定义类型

#### 2.1 第一种方式

- 为成员变量提供set get方法

- 配置文件中进行注入(赋值)

    ```xml
    <bean id="userDAO" class="xxx.xxx.UserServiceImpl">
    	<property name="userDAO">
        	<bean class="xxx.xxx.UserDAOImpl"/>
        </property>
    </bean>
    ```

#### 2.2 第二种方式

- 第一种赋值方式存在的问题

    ```markdown
    1. 配置代码冗余
    2. 被注入的对象(UserDAO)，多次创建，浪费(JVM)内存资源
    ```

- 为成员变量提供set get方法

- 配置文件中进行配置

    ```xml
    <bean id="userDAO" class="com.nfhao.basic.UserDAOImpl"/>
    <bean id="userService" class="com.nfhao.basic.UserServiceImpl">
        <property name="userDAO">
            <ref bean="userDAO"/>
        </property>
    </bean>
    
    #Spring4.x 废除了 <ref local=""/> 基本等效 <ref bean=""/>
    ```

### 3.Set注入的简化写法

#### 3.1 基于属性简化

```xml
JDK类型注入
<bean id="person" class="com.nfhao.basic.Person">
    <property name="name">
        <value>xiaonie</value>
    </property>
</bean>

<bean id="person" class="com.nfhao.basic.Person">
    <property name="name" value="xiaonie" />
</bean>

注意：value属性 只能简化 8种基本类型+String 注入标签

用户自定义类型
<property name="userDAO" ref="userDAO"/>
```

#### 3.1 基于p命名空间简化

```xml
JDK类型注入
<bean id="person" class="com.nfhao.basic.Person">
    <property name="name">
        <value>xiaonie</value>
    </property>
</bean>

<bean id="person" class="com.nfhao.basic.Person" p:name="xiaonie" p:id="100"/>
注意：value属性 只能简化 8种基本类型+String 注入标签

用户自定义类型
<bean id="userService" class="com.nfhao.basic.UserServiceImpl" p:userDAO-ref="userDAO"/>
```

## 第六章 构造注入

```markdown
注入：通过Spring的配置文件，为成员变量赋值。
Set注入：Spring调用set方法 通过配置文件 为成员变量赋值
构造注入：Spring调用构造方法 通过配置文件 为成员变量赋值
```

### 1.开发步骤

- 提供有参构造方法

    ```java
    package com.nfhao.basic.constructer;
    
    import java.io.Serializable;
    
    /**
     * @program: spring5
     * @description: 有参构造注入
     * @author: 小聂同学
     * @create: 2020-11-24 11:02
     */
    public class Customer implements Serializable {
    
        private String name;
        private int age;
    
        public Customer(String name, int age) {
            this.name = name;
            this.age = age;
        }
    
        @Override
        public String toString() {
            return "Customer{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
        }
    }
    ```

- Spring的配置文件

    ```xml
    <bean id="customer" class="com.nfhao.basic.constructer.Customer">
        <!-- constructor-arg标签的个数就是构造参数标签的个数，顺序就是构造函数中参数的顺序 -->
        <constructor-arg><!-- 注入name -->
            <value>xiaonie</value>
        </constructor-arg>
        <constructor-arg><!-- 注入age -->
            <value>18</value>
        </constructor-arg>
    </bean>
    ```

### 2.构造方法重载

#### 2.1 构造参数个数不同时

```markdown
通过控制<constructor-arg>的数量进行区分
```

#### 2.2 构造参数个数相同时

```markdown
通过在标签引入 type属性 进行区分 <constructor-arg type="">
```

### 3.注入的总结

```markdown
未来的实战中，应用set注入还是构造注入
答案：set注入更多
	1. 构造注入麻烦(重载)
	2. Spring框架底层 大量应用了 set注入
```

![image-20201124132505572](Spring-系列课程/image-20201124132505572.png)

## 第七章 反转控制 与 依赖注入

###  1.反转（转移）控制(IOC Inverse of Control)

```markdown
控制：对于成员变量赋值的控制权
反转控制：对于成员变量赋值的控制权，从代码中反转（转移）到Spring工厂和配置文件中完成
	好处：解耦合
底层实现：工厂设计模式
```

![image-20201124201229022](Spring-系列课程/image-20201124201229022.png)

### 2.依赖注入（Dependency Injection DI）

```markdown
注入：通过Spring的工厂及配置文件，为对象（bean,组件）的成员变量赋值

依赖注入：当一个类需要另一个类时，就意味着依赖，一旦出现依赖就可以把另一个类作为本类的成员变量，最终通过Spring配置文件进行注入（赋值）。
	好处：解耦合
```

![image-20201124210539588](Spring-系列课程/image-20201124210539588.png)

## 第八章 Spring工厂创建复杂对象

![image-20201124211826220](Spring-系列课程/image-20201124211826220.png)

### 1.什么是复杂对象

```markdown
复杂对象：指的是不能通过new构造方法创建的对象
	Connection
	SqlSessionFactory
```

### 2.Spring工厂创建复杂对象的3种方式

#### 2.1 FactoryBean接口

- 开发步骤

    - 实现FactoryBean接口

      ![image-20201125140702462](Spring-系列课程/image-20201125140702462.png)

    - Spring配置文件的配置
    
        ```xml
        # 如果class中指定的类型 是FactoryBean接口的实现类，那么通过id值获得的是这个歌类创建的复杂对象 Connection
        <bean id="conn" class="com.nfhao.factorybean.ConnectionFactoryBean"/>
        ```
    
    - 测试代码
    
        ```java
        /**
        * @Description: 用于测试FactoryBean接口
        * @Author: xiaonie
        * @DateTime: 2020/11/25-14:47
        */
        @Test
        public void test12(){
            ApplicationContext ctx = new ClassPathXmlApplicationContext("/applicationContext.xml");
            Connection conn = (Connection) ctx.getBean("conn");
            System.out.println("conn = " + conn);
        }
        ```
    
- 细节

    - 如果就想获得FactroyBean类型的对象 ctx.getBean("&conn")

        **获得的就是ConnectionFactoryBean**

    - isSingleton方法

        返回 true 只会创建一个复杂对象

        返回 false 每一次都会创建新的对象

        问题：根据这个对象的特点，决定是返回 true （SqlSessionFactory）还是 flase （Connection）

        SqlSessionFactory是线程安全的，也是重量级的，可以共用一个对象

        Connection则不可以

    - mysql高版本连接创建时，需要制定SSL证书，解决问题的方式

        ```\
        url = "jdbc:mysql://localhost:3306/mybatis?useSSL=false"
        ```

    - 依赖注入的体会（DI）

        ```markdown
        把ConnectionFactoryBean中依赖的4个字符串信息，进行配置文件注入
        好处：解耦合
        <!-- FactoryBean接口实现工厂 -->
        <bean id="conn" class="com.nfhao.factorybean.ConnectionFactoryBean">
            <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC"/>
            <property name="username" value="root"/>
            <property name="password" value="123456"/>
        </bean>
        ```

- FactoryBean的实现原理[简易版]

    ```markdown
    接口回调
    1. 为什么Spring规定FactoryBean接口 实现 并且 getObject()?
    2. ctx.getBean("conn")获得是复杂对象Connection 而没有 获得ConnectionFactoryBean(&)
    
    String内部运行流程
    1. 通过conn获得 ConnectionFactoryBean类的对象，进而通过instanceof判断出是FactoryBean接口的实现类
    2. Spring按照规定 getObject() ---> Connection
    3. 返回Connection
    ```
    ![image-20201125170924973](Spring-系列课程/image-20201125170924973.png)
- FactoryBean总结

    ```markdown
    Spring中用于创建复杂对象的一种方式，也是Spring原生提供的，后续讲解Spring整合其他框架，大量应用FactoryBean
    ```

#### 2.2 实例工厂

```markdown
1. 避免Spring框架的侵入
2. 整合遗留系统
```

- 开发步骤

    ```xml
    <!-- 实例工厂 -->
    <bean id="connFactory" class="com.nfhao.factorybean.ConnectionFactory"/>
    <bean id="conn" factory-bean="connFactory" factory-method="getConnection"/>
    ```

#### 2.3 静态工厂

- 开发步骤

    ```xml
    <!-- 静态工厂 -->
    <bean id="conn" class="com.nfhao.factorybean.StaticConnectionFactory" factory-method="getConnection"/>
    ```

### 3.Spring创建对象的总结

![image-20201125182241698](Spring-系列课程/image-20201125182241698.png)

## 第九章 控制Spring工厂创建对象的次数

### 1.如何控制简单对象的创建次数

```xml
<bean id="account" acope="singleton|prototype" class="xxx.Account"/>
sigleton : 只会创建一次简单对象 默认值
prototype : 每一次都会创建新的对象
```

### 2.如何控制复杂对象的创建次数 

```markdown
FactoryBean{
	isSingleton(){
		return true;//只会创建一次
		return false;//每一次多会创建新的
	}
}
如果没有isSingleton方法 还是通过scope属性 进行对象创建次数控制
```

### 3.为什么要控制对象的创建次数？

```markdown
好处：节省不必要的内存浪费
```

- 什么样的对象只创建一次

    ```markdown
    1. SqlSessionFactroy
    2. DAO
    3. Service
    ```

- 什么样的对象 么一次都要创建新的

    ```markdown
    1. Connection
    2. SqlSession | Session
    3. Struts2 Action
    ```


## 第十章 对象的生命周期

### 1.什么是对象的生命周期

```markdown
指的是对象的创建、存活、消亡的一个完整过程
```

### 2.为什么要学习对象的生命周期

```java
由Spring来负责对象的创建、存活、销毁，了解生命周期，有利于我们使用好Spring为我们创建的对象
```

### 3.生命周期的三个阶段

![image-20201126213119043](Spring-系列课程/image-20201126213119043.png)

- 创建阶段

    ```markdown
    Spring工厂何时帮我们创建对象
    ```

    - scope="singleton"

        ```markdown
        Spring工厂创建的同时，对象的创建
        
        注意：设置scope=singleton 这种情况下 也需要在获取对象的同时创建对象
        <bean lazy-init="true"/>
        ```

    - scope="prototype"

        ```markdown
        Spring工厂会在获取对象的同时，创建对象
        ctx.getBean("");
        ```

- 初始化阶段

    ```markdown
    Spring工厂在创建完对象后，会调用对象的初始化方法，完成对应的初始化操作
    
    1. 初始化方法提供：程序员根据需求，提供的初始化方法，最终完成初始化操作
    2. 初始化方法调用：Spring工厂
    ```

    - 进行调用InitializingBean接口

        ```java
        //程序员根据需求，实现的方法，完成初始化操作
        public void afterPropertiesSet() throws Exception {
            System.out.println("xxx.afterPropertiesSet");
        }
        ```

    -   对象中提供一个普通的方法

        ```java
        public void myInit() {
            
        }
        <bean id="product" class="xxx.Product" init-method=""/>
        ```

    - 细节分析

        1. 如果一个对象即实现InitializingBean同时有提供了 普通的初始化方法 顺序

            ```markdown
            先执行构造方法Product.Product
            1. afterPropertiesSet
            2. 普通初始化方法
            ```

        2. 注入一定发生在初始化操作的前面

        3. 什么叫做初始化操作

            ```markdown
            资源的初始化：数据库 IO 网络 ......
            ```

- 销毁阶段

    ```markdown
    Spring销毁对象前，会调用对象的销毁方法，完成销毁操作
    
    1. Spring什么时候销毁所创建的对象?
        ctx.close();
    2. 销毁方法：程序员根据自己的需求，定义销毁方法，完成销毁操作
          调用：Spring工厂来完成调用
    ```

    - DisposableBean

        ```java
        public void destroy() throws Exception {
        
        }
        ```

    - 定义一个普通的销毁方法

        ```java
        public void myDestory(){
        
        }
            <bean id="" class=" destroy-method="myDestory">
        ```

    - 细节分析

        1. 销毁方法的操作只适用于scope="singleton"

        2. 什么叫做销毁操作

            ```markdown
            主要的就是 资源的释放 io.close() connection.close();
            ```

## 第十一章 配置文件参数化

```markdown
把Spring配置文件中需要经常修改的字符串信息，转移到一个更小的配置文件中

1. Spring的配置文件中存在需要经常修改的字符串?
    存在 以数据库连接相关的参数 代表
2. 经常变化的字符串，在Spring配置文件中，直接修改
    不利于项目维护（修改）
3. 转移到一个小的配置文件（.properties）
	利于维护（修改）

配置文件参数化：利于Spring配置文件的维护（修改）
```

### 1.配置文件参数化的开发步骤

- 提供一个小的配置文件(.properties)

    名字：随便

    放置位置：随便

    ```properties
    jdbc.driverClassName = com.mysql.cj.jdbc.Driver\
    jdbc.url = jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC
    jdbc.username = root
    jdbc.password = 123456
    ```

- Spring的配置文件与小配置文件整合

    ```xml
    applicationContext.xml
    <!-- Spring配置文件与小配置文件的整合 -->
    <context:property-placeholder location="classpath:/db.properties"/>
    ```

- 在Spring的配置文件中通过${key}获取小配置文件中对应的值

    ![image-20201127153537181](Spring-系列课程/image-20201127153537181.png)

## 第十二章 自定义类型转换器

### 1.类型转换器

```markdown
作用：Spring通过类型转换器把配置文件中的字符串类型的数据，转换成了对象中成员变量类型的数据，进而完成了 注入。
```

![image-20201127160937008](Spring-系列课程/image-20201127160937008.png)

### 2.自定义类型转换器

```markdown
原因：当Spring内部没有提供特定的类型转换器时，而程序员在应用的过程中还需要使用，那么就需要程序员自己定义类型转换器
```

- 类 implements Converter接口

    ```java
    package com.nfhao.converter;
    
    import org.springframework.core.convert.converter.Converter;
    
    import java.text.ParseException;
    import java.text.SimpleDateFormat;
    import java.util.Date;
    
    /**
     * @program: spring5
     * @description: 自定义的类型转换器String转Date
     * @author: 小聂同学
     * @create: 2020-11-27 16:36
     */
    public class MyDateConverter implements Converter<String, Date> {
    
        /**
        * @Description: 方法作用：将String类型转换成Date类型
         * param:source 代表的就是配置文件中 那个日期字符串 <value>2020-11-27</value>
        * @Author: xiaonie
        * @DateTime: 2020/11/27-16:43
        */
    
        public Date convert(String source) {
            Date date = null;
            try {
                SimpleDateFormat sdf = new SimpleDateFormat("yy-MM-dd");
                date = sdf.parse(source);
            } catch (ParseException e) {
                e.printStackTrace();
            } 
            return date;
        }
    }
    ```

- 在Spring的配置文件中进行配置

    - MyDateConverter对象创建出来

        ```xml
        <bean id="myDateConverter" class="xxx.MyDateConverter"/>
        ```

    - 类型转化器的注册

        ```markdown
        目的：告知Spring框架，我们所创建的MyDateConverter是一个类型转换器
        ```

### 3.细节

- MyDateConverter中的日期格式，通过依赖注入的方式，由配置文件来完成赋值。

    ```java
    package com.nfhao.converter;
    
    import org.springframework.core.convert.converter.Converter;
    import java.text.ParseException;
    import java.text.SimpleDateFormat;
    import java.util.Date;
    
    /**
     * @program: spring5
     * @description: 自定义的类型转换器String转Date
     * @author: 小聂同学
     * @create: 2020-11-27 16:36
     */
    public class MyDateConverter implements Converter<String, Date> {
    
        private String pattern;
    
        public String getPattern() {
            return pattern;
        }
    
        public void setPattern(String pattern) {
            this.pattern = pattern;
        }
    
        /**
        * @Description: 方法作用：将String类型转换成Date类型
         * param:source 代表的就是配置文件中 那个日期字符串 <value>2020-11-27</value>
        * @Author: xiaonie
        * @DateTime: 2020/11/27-16:43
        */
    
        public Date convert(String source) {
            Date date = null;
            try {
                SimpleDateFormat sdf = new SimpleDateFormat(pattern);
                date = sdf.parse(source);
            } catch (ParseException e) {
                e.printStackTrace();
            }
            return date;
        }
    }
    ```

    ```xml
    <!-- Spring创建MyDateConverter类型对象 -->
    <bean id="myDateConverter" class="com.nfhao.converter.MyDateConverter" p:pattern="yyyy-MM-dd"/>
    
    <!-- 用于注册类型转换器 -->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <ref bean="myDateConverter"/>
            </set>
        </property>
    </bean>
    
    <bean id="person" class="com.nfhao.converter.Person">
        <property name="name" value="xiaonie"/>
        <property name="birthday" value="2020-11-27"/>
    </bean>
    ```

- ConversionServiceFactoryBean 定义id属性值 必须为 conversionService

- Spring框架已经内置了日期的类型转换器

    ```markdown
    日期格式：2020/11/27 (不支持2020-11-27)
    ```

## 第十三章 后置处理Bean

```markdown
BeanPostProcessor作用：对Spring工厂所创建的对象，进行再加工。

AOP底层实现：

注意：BeanPostProcessor
	xxx(){
		//加工		
	}
```

- 后置处理Bean的运行原理分析

   ![image-20201128131920395](Spring-系列课程/image-20201128131920395.png)
   ```markdown
   程序员实现BeanPostProcessor接口中规定的方法：
   
   Object postProcessBeforeInitiallization(Object bean, String beanName)
   作用：Spring创建完对象，并进行注入后可以运行Bedore方法进行加工
   获得Spring创建好的对象：通过方法的参数
   最终通过返回值交给Spring框架
   
   Object postProcessAfterInitiallization(Object bean, String beanName)
   作用：Spring执行完对象的初始化操作后，可以运行After方法进行加工
   获得Spring创建好的对象：通过方法的参数
   最终通过返回值交给Spring框架
   
   实战中：
   很少处理Spring的初始化操作：没有必要区分Before和After.只需要实现其中的一个After方法即可
   ```

- BeanPostProcessor的开发步骤

    1. 类 实现 BeanPostProcessor接口

        ```java
        package com.nfhao.beanpost;
        
        import org.springframework.beans.BeansException;
        import org.springframework.beans.factory.config.BeanPostProcessor;
        
        /**
         * @program: spring5
         * @description: 实现BeanPostProcessor
         * @author: 小聂同学
         * @create: 2020-11-28 13:49
         */
        public class MyBeanPostProcessor implements BeanPostProcessor {
        
            public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
                return bean;
            }
        
            public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
                if (bean instanceof Categroy){//要判断要加工的类型
                    Categroy categroy = (Categroy) bean;
                    categroy.setId(20);
                }
                return bean;
            }
        }
        ```

    2. Spring的配置文件中进行配置

        ```xml
        <bean id="myBeanPostProcessor" class="xxx.MyBeanPoetProcessor"/> 
        ```

    3. BeanPostProcessor细节

        ```markdown
        BeanPostProcessor会对Spring工厂所有创建的对象进行加工。
        ```


