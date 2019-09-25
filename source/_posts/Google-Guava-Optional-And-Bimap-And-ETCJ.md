---
title: Google Guava Optional And Bimap And ETCJ
categories: Coder
date: 2019-05-26 15:24:51
tags: Google Guava
description:
---

Guava是Google基于java7开发了一套工具类，包括：集合，缓存，基元支持，并发库，通用注释，字符串处理，I / O等

基于Guava方便快速的构建你的代码，目的在于“方便，快速”，这个是目标

> 不要忘了你为什么出发

<!-- more  -->

导入jar包：
~~~
<!-- https://mvnrepository.com/artifact/com.google.guava/guava -->
       <dependency>
           <groupId>com.google.guava</groupId>
           <artifactId>guava</artifactId>
           <version>27.1-jre</version>
       </dependency>
~~~
#### Google Guava EventBus
EventBus是Guava的事件处理机制，是设计模式中的观察者模式（生产/消费者编程模型）的实现。
对于生存/消费模式，基于注解（@Subscribe）的方式实现，不用创建复杂的类和接口层次结构。

消息封装类：

~~~
//被观察者
public class EventTest {

    private final Integer message;

    public EventTest(Integer message) {
        this.message = message;
        System.out.println("event message"+message);
    }

    public Integer getMessage(){
        return message;
    }
}
~~~
消费者类：
~~~
//观察者
public class EventListener {

    public Integer lastMessage=0;

    //只要在消费方法签名加上注解，无需实现复杂的接口
    @Subscribe
    public void listen(EventTest eventTest){
        lastMessage=eventTest.getMessage();
        System.out.println("Message:"+lastMessage);
    }
    public Integer getLastMessage(){
        return lastMessage;
    }

}
~~~
测试：
~~~
@RunWith(SpringRunner.class)
@SpringBootTest
public class TestEventBus {

    @Test
    public void testReceiveEvent() throws Exception {

        EventBus eventBus = new EventBus("test");
        EventListener listener = new EventListener();

        eventBus.register(listener);

        eventBus.post(new EventTest(200));
        eventBus.post(new EventTest(300));
        eventBus.post(new EventTest(400));

        System.out.println("LastMessage:"+listener.getLastMessage());
    }


}
~~~

什么是观察者模式：
观察者模式（又被称为发布-订阅（Publish/Subscribe）模式，属于行为型模式的一种，它定义了一种一对多的依赖关系，让多个观察者对象同时监听某一个主题对象。这个主题对象在状态变化时，会通知所有的观察者对象，使他们能够自动更新自己。

如下是自己定义接口实现观察者模式[参考](https://blog.csdn.net/itachi85/article/details/50773358)：
~~~
抽象观察者
public interface Observer {
    public void update(String message);
}
具体观察者  对应Guava：EventListener
public class Wechat implements Observer {
    //
    private String name;
    public Wechat(String name) {
        this.name = name;
    }
    @Override
    public void update(String message) {
        System.out.println(name + "-" + message);
    }


}
抽象被观察者
public interface Subject {

    /**
     * 增加订阅者
     * @param observer
     */
    public void attach(Observer observer);
    /**
     * 删除订阅者
     * @param observer
     */
    public void detach(Observer observer);
    /**
     * 通知订阅者更新消息
     */
    public void notify(String message);
}
具体被观察者：
public class SubscriptionSubject implements Subject {
    //储存订阅公众号的微信用户
    private List<Observer> wechatUserList = new ArrayList<Observer>();

    @Override
    public void attach(Observer observer) {
        wechatUserList.add(observer);
    }

    @Override
    public void detach(Observer observer) {
        wechatUserList.remove(observer);
    }

    @Override
    public void notify(String message) {
        for (Observer observer : wechatUserList) {
            observer.update(message);
        }
    }
}
测试：
public class Client {
    public static void main(String[] args) {
        SubscriptionSubject mSubscriptionSubject=new SubscriptionSubject();
        //创建微信用户
        WechatObserver user1=new WechatObserver("name1");
        WechatObserver user2=new WechatObserver("naem2");
        WechatObserver user3=new WechatObserver("name3");
        //订阅公众号
        mSubscriptionSubject.attach(user1);
        mSubscriptionSubject.attach(user2);
        mSubscriptionSubject.attach(user3);
        //公众号更新发出消息给订阅的微信用户
        mSubscriptionSubject.notify("一乐行更新《如何看待中国的户籍制度》");
    }
}
~~~

#### Optional
Optional 解决来在代码中存在的null意义不确定的问题，比如在map.get(key)中，返回一个null
，可以是没有值，可能是没有key，引入Opional可以让程序设计者主动的去思考null在你程序中的意义。

~~~
        //创建Optional的三种方式
        //1.创建指定引用的Optional实例，若引用为null则快速失败
        Optional<Integer> possible1 = Optional.of(5);//若为null，抛出java.lang.NullPointerException
        //2.创建指定引用的Optional实例，若引用为null则表示缺失
        Optional<Integer> possible2 = Optional.fromNullable(null);//若为null，不会抛出异常
        //3.创建引用缺失的Optional实例
        Optional<Object> possible3=Optional.absent();

        //demo-method
        //如果Optional包含非null的引用（引用存在），返回true
        possible1.isPresent();
        //返回Optional所包含的引用，若引用缺失，则抛出java.lang.IllegalStateException
        possible1.get();
        //返回Optional所包含的引用，若引用缺失，返回指定的值
        possible1.or(1);
        //返回Optional所包含的引用，若引用缺失，返回null
        possible1.orNull();
        //返回Optional所包含引用的单例不可变集，如果引用存在，返回一个只有单一元素的集合，如果引用缺失，返回一个空集合。
        possible1.asSet();

        // Optional解决了什么问题：强迫开发者去思考可能出现null 的情况

~~~
#### BiMap

在有些场景中，根据key寻找value，而有是否会反转过来，根据value找到key。BiMap可以找到这一点
case1:
~~~
@Test
   public void BimapTest(){
       BiMap<Integer,String> userMap = HashBiMap.create();
       userMap.put(1,"name1");
       userMap.put(2,"name2");
       userMap.put(3,"name3");
       System.out.println("userMap:"+userMap);
       BiMap<String,Integer> newUserMap = userMap.inverse();
       System.out.println("newUserMap:"+newUserMap);
       //实现key ，value 值的反转
   }
~~~
case2:
在使用BiMap时，会要求Value的唯一性。如果value重复了则会抛出错误：java.lang.IllegalArgumentException
如果我们确实需要插入重复的value值，那可以选择forcePut方法。但是我们需要注意的是前面的key也会被覆盖了。
~~~
@Test
    public void BimapTest2(){
        BiMap<Integer,String> userMap = HashBiMap.create();
        userMap.forcePut(1,"name1");
        userMap.forcePut(2,"name2");
        userMap.forcePut(3,"name3");
        userMap.forcePut(4,"name4");
        userMap.forcePut(5,"name4");
        System.out.println("userMap:"+userMap);
        BiMap<String,Integer> newUserMap = userMap.inverse();
        System.out.println("newUserMap:"+newUserMap);
    }
~~~
case3:对于inverse反转以后的newBiMap不是一个全新的Map，而是基于实现了一种试图关联，也就是说对于反转后的Map的操作都会影响原来的map
~~~
    @Test
    public void BimapTest3(){

        BiMap<Integer,String> userMap = HashBiMap.create();
        userMap.put(1,"name1");
        userMap.put(2,"name2");
        userMap.put(3,"name3");
        System.out.println("userMap:"+userMap);
        BiMap<String,Integer> newUserMap= userMap.inverse();
        newUserMap.put("name4",4);
        System.out.println("newUserMap:"+newUserMap);
        System.out.println("userMap:"+userMap);
        /*
        *
        * 输出：
        * userMap:{1=name1, 2=name2, 3=name3}
        * newUserMap:{name1=1, name2=2, name3=3, name4=4}
        * userMap:{1=name1, 2=name2, 3=name3, 4=name4}
        *
        * */


    }
~~~


#### 使用 Guava 复写Object常用方法
重写equals方法：
~~~
    /*
    * 重写equals方法的要求：
    1、自反性：对于任何非空引用x，x.equals(x)应该返回true。
    2、对称性：对于任何引用x和y，如果x.equals(y)返回true，那么y.equals(x)也应该返回true。
    3、传递性：对于任何引用x、y和z，如果x.equals(y)返回true，y.equals(z)返回true，那么x.equals(z)也应该返回true。
    4、一致性：如果x和y引用的对象没有发生变化，那么反复调用x.equals(y)应该返回同样的结果。
    5、非空性：对于任意非空引用x，x.equals(null)应该返回false。
    * */

    @Override
    public boolean equals(Object o) {
        if (o instanceof BeanDomain) {
            BeanDomain that = (BeanDomain) o;
            return Objects.equal(id, that.id)
                    && Objects.equal(name, that.name)
                    && Objects.equal(sex, that.sex);
        }
        return false;
    }
~~~
重写hashcode：Objects.hashCode(Object...obj)
~~~
@Override
    public int hashCode() {
        return Objects.hashCode(this.id,this.name,this.sex);
    }
~~~
重写compareTo：
~~~
@Override
   public int compareTo(BeanDomain beanDomain) {
       return ComparisonChain.start()
               .compare(name, beanDomain.id)
               .compare(id, beanDomain.name)
               .compare(sex, beanDomain.sex, Ordering.natural().nullsLast())
               .result();
   }
~~~
参考：
> [芋道源码 Guava](http://www.iocoder.cn/Guava/good-collection/)
> [看云 Guava](https://www.kancloud.cn/wizardforcel/java-opensource-doc/112614)
