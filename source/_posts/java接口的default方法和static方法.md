---
title: java接口的default方法和static方法
date: 2020-10-23 14:14:20
tags:
 - jdk1.8
 - java
---

> 接口中的变量未声明类型，默认都是public static final也就是常量。方法默认都是public abstract 抽象方法。但是在jdk1.8中接口中可以定义default和static方法。我们一起来看看

声明两个接口DefaultAndStaticTest.java,里面包含了abstract method、default method、static method

``` bash

public interface DefaultAndStaticTest {

    /**
     *  必须被实现类重写
     */
    void  overideMethod();

    /**
     *  如果实现类继承的多个接口都有该default方法，需要在实现类中重写
     */
    default void commonDefault(){
        System.out.println("interface default method commonDefault");
    }

    /**
     * 可以被实现类重写，也可以不重写
     */
    default void sayDefault(){
        System.out.println("interface default method sayDefault");
    }

    /**
     * method does not override method from its superclass
     * 不能被实现类重写
     */
    static void sayStatic()
    {
        System.out.println("interface static method");
    }

}

```

声明另一个接口DefaultTest.java，包含一个default方法，和上面的接口相同签名的default方法

``` bash

public interface DefaultTest {

    default void commonDefault(){
        System.out.println("interface default method commonDefault");
    }
}

```

编写实现类TestUtil

``` bash

public class TestUtil implements DefaultAndStaticTest, DefaultTest {

    /**
     *  must either be declared abstract or implement abstract method
     *  必须声明或者实现抽象方法
     */
    @Override
    public void overideMethod() {
        System.out.println("必须实现接口中抽象方法");
    }

    /**
     *  inherits unrelated default for commonDefault() from DefaultAndStaticTest and DefaultTest
     *  当类实现的两个接口中都有default方法commonDefault，需要在实现类中重写该default方法
     */
    @Override
    public void commonDefault() {
        System.out.println("我是DefaultAndStaticTest和DefaultTest两个接口都有的default已经被重写");
    }

    public void sayStatic() {
        System.out.println("我是TestUtil中方法而非接口静态方法");
    }

    public static void main(String[] args) {
        new TestUtil().commonDefault();
        new TestUtil().sayStatic();
        DefaultAndStaticTest.sayStatic();
    }
}

```

控制台输出：

``` 
interface default method sayDefault
我是DefaultAndStaticTest和DefaultTest两个接口都有的default已经被重写
我是TestUtil中方法而非接口静态方法
interface static method
Process finished with exit code 0

```
<font color="orange">思考：如果interface中实现了default，实现类中也重写了该方法， 声明个子类继承实现类并实现接口，用子类实例调用default方法调用的是接口的default还是实现类重写的default呢？
