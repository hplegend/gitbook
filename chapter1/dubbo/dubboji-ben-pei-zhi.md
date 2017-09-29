ref：[http://dubbo.io/](http://dubbo.io/)

dubbo是阿里出的一个RPC框架，支持远程服务调用。

## provider

服务的提供方： 提供服务接口，同时实现服务。

![](/assets/img01.png)

interface:

```
public interface DemoService {
    String sayHello(String name);
}
```

implement:

```
public class DemoServiceImpl  implements DemoService{

    public String sayHello(String name) {
        return "Hello " + name;
    }
}
```

xml:

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <dubbo:application name="demo-provider"/>
    <dubbo:registry address="multicast://224.5.6.7:1234"/>
    <dubbo:protocol name="dubbo" port="20880"/>
    <dubbo:service interface="com.XXXX.dubbo.service.DemoService" ref="demoService"/>
    <bean id="demoService" class="com.XXXX.dubbo.service.impl.DemoServiceImpl"/>
</beans>
```



## consumer

调用

```
public class main {

    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(
                new String[]{"classpath:consumer.xml"});
        context.start();
        DemoService demoService = (DemoService) context.getBean("demoService"); // obtain proxy object for remote invocation
        String hello = demoService.sayHello("hp"); // execute remote invocation
        System.out.println(hello); // show the result
    }

}
```

xml：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

    <dubbo:application name="demo-consumer"/>
    <dubbo:registry address="multicast://224.5.6.7:1234"/>
    <dubbo:reference id="demoService" check="false" interface="com.XXXX.dubbo.service.DemoService"/>
</beans>
```

pom：

```
<dependency>
            <groupId>com.XXXX.XXXXX</groupId>
            <artifactId>dubboprovider</artifactId>
            <version>1.0-SNAPSHOT</version>
</dependency>

```



特别注意：

    **消费者需要知道服务提供者提供的接口，否则无法调用，也无法配置xml。通过在pom中引入接口提供的包，或者导入接口的jar包。**







