# SpringBean生命周期

## 1、SpringBean生命周期

1、如果实现了 BeanNameAware 接口，则调用 setBeanName 方法
2、如果实现了 BeanFactoryAware 接口，则调用 setBeanFactory 方法
3、如果实现了 ApplicationContextAware 接口，则调用 setApplicationContext 方法
4、有实现了 BeanPostProcessor 的 Bean 调用其 postProcessBeforeInitialization 方法（如果自身实现了BeanPostProcessor则不会进行这一步）
5、如果实现了 InitializingBean 接口，则调用 afterPropertiesSet 方法
6、如果指定了自定义的 init 方法，则调用自定义的 init 方法
7、有实现了 BeanPostProcessor 的 Bean 调用其 postProcessAfterInitialization 方法（如果自身实现了BeanPostProcessor则不会进行这一步）
8、Bean 初始化完成可以使用了
关闭容器 ... ...
9、如果 Bean 实现了 DisposableBean 接口，则调用 destroy 方法
10、如果指定了自定义的 destroy 方法，则调用 自定义的 destroy 方法

## 2、代码演示

### 1）spring-context.xml 配置

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean id="messageService" class="com.example.spring.beanlife.MessageService" init-method="customInit"
      destroy-method="customDestroy"/>

  <bean id="myBeanPostProcessor" class="com.example.spring.beanlife.MyBeanPostProcessor"/>

</beans>
```

### 2）测试代码

```
package com.example.spring.beanlife;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.BeanFactory;
import org.springframework.beans.factory.BeanFactoryAware;
import org.springframework.beans.factory.BeanNameAware;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

/**
 * @author jingtao
 * @version $Id: MessageService.java, v0.1 2021/01/23 22:25:10 jingtao Exp $
 */
public class MessageService implements BeanNameAware, BeanFactoryAware, ApplicationContextAware, InitializingBean,
    DisposableBean {

  private String name;

  @Override
  public void afterPropertiesSet() throws Exception {
    //4、如果有实现了 BeanPostProcessor 的 Bean 则先调用其 postProcessBeforeInitialization 方法
    System.out.println("5、如果实现了 InitializingBean 接口，则调用 afterPropertiesSet 方法");
  }

  public void customInit() {
    System.out.println("6、如果指定了自定义的 init 方法，则调用自定义的 init 方法");
  }

  //7、如果有实现了 BeanPostProcessor 的 Bean 则先调用其 postProcessBeforeInitialization 方法
  public void hello() {
    System.out.println("8、Bean 初始化完成可以使用了，Hello!");
  }

  @Override
  public void destroy() throws Exception {
    System.out.println("9、如果 Bean 实现了 DisposableBean 接口，则调用 destroy 方法");
  }

  public void customDestroy() {
    System.out.println("10、如果指定了自定义的 destroy 方法，则调用 自定义的 destroy 方法");
  }

  @Override
  public void setBeanName(String name) {
    System.out.println("1、如果实现了 BeanNameAware 接口，则调用 setBeanName 方法，接受参数为 Bean 的名称");
  }

  @Override
  public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
    System.out.println("2、如果实现了 BeanFactoryAware 接口，则调用 setBeanFactory 方法，接受参数为 Bean 所在的 beanFactory");
  }

  @Override
  public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
    System.out.println("3、如果实现了 ApplicationContextAware 接口，则调用 setApplicationContext 方法，接受参数为 Bean 所在的 applicationContext");
  }
}
```

BeanPostProcessor ：

```
package com.example.spring.beanlife;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

/**
 * @author jingtao
 * @version $Id: MyBeanPostProcessor.java, v0.1 2021/01/24 00:14:15 jingtao Exp $
 */
public class MyBeanPostProcessor implements BeanPostProcessor {
  @Override
  public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
    System.out.println("4、有实现了 BeanPostProcessor 的 Bean 调用其 postProcessBeforeInitialization 方法");
    return bean;
  }

  @Override
  public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
    System.out.println("7、有实现了 BeanPostProcessor 的 Bean 调用其 postProcessAfterInitialization 方法");
    return bean;
  }
}
```

测试类：

```
package com.example.spring.beanlife;

import org.springframework.beans.factory.config.ConfigurableBeanFactory;
import org.springframework.beans.factory.xml.XmlBeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.core.io.ClassPathResource;

/**
 * 加载 XML 启动 Spring 上下文
 *
 * @author jingtao
 * @version $Id: SpringBeanLifeDemo.java, v0.1 2021/01/23 22:22:41 jingtao Exp $
 */
public class SpringBeanLifeDemo {

  public static void main(String[] args) {
    //1、ApplicationContext
    applicationContext();

    //2、BeanFactory
    //beanFactory();
  }

  private static void applicationContext() {
    ClassPathXmlApplicationContext applicationContext =
        new ClassPathXmlApplicationContext("classpath:spring-context.xml");
    MessageService messageService = (MessageService) applicationContext.getBean("messageService");
    messageService.hello();
    System.out.println("关闭容器 ... ...");
    applicationContext.close();
  }


  private static void beanFactory() {
    //BeanFactory 容器初始化 Bean时，不会调用 ApplicationContextAware 接口的 setApplicationContext() 方法
    ConfigurableBeanFactory beanFactory = new XmlBeanFactory(new ClassPathResource("spring-context.xml"));
    //beanFactory 需要手动注册 BeanPostProcessor
    beanFactory.addBeanPostProcessor(new MyBeanPostProcessor());
    MessageService messageService = (MessageService) beanFactory.getBean("messageService");
    messageService.hello();
    System.out.println("关闭容器 ... ...");
    beanFactory.destroySingletons();

  }
}
```
