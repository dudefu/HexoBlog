---
title: Java单例模式
categories: Java
tags: 设计模式
abbrlink: 86ceb8f1
date: 2018-05-01 20:01:46
---
1. 饿汉式
```Java
//饿汉式单例类，在类初始化时，已经自行实例化
public class Singleton1 {
    //私有的默认构造方法
    private Singleton1(){}
    //已经自行实例化
    private static final Singleton1 single = new Singleton1() ;
    //静态工厂方法
    private static Singleton1 getInstance(){
        return single ;
    }
}
```
<!---more--->
2. 懒汉式单例类
```Java
//懒汉式单例类，在第一次调用的时候实例化
public class Singleton2 {
    //私有的默认构造方法
    private Singleton2(){}
    //注意，这里没有final
    private static Singleton2 single = null ;
    //静态工厂方法，如果不加synchronized线程不安全
    public synchronized static Singleton2 getInstance() {
        if(single == null ){
            single = new Singleton2() ;
        }
        return single ;
    }
}
```
3. 登记式单例类
```Java
public class RegistSingleton {
    //用ConcurrentHashMap来维护映射关系，这是线程安全的
    public static final Map<String,Object> REGIST=new ConcurrentHashMap<String, Object>();
    static {
        //把RegistSingleton自己也纳入容器管理
        RegistSingleton registSingleton=new RegistSingleton();
        REGIST.put(registSingleton.getClass().getName(),registSingleton);
    }
    private RegistSingleton(){}
    public static Object getInstance(String className){
        //如果传入的类名为空，就返回RegistSingleton实例
        if(className==null)
            className=RegistSingleton.class.getName();
            //如果没有登记就用反射new一个
        if (!REGIST.containsKey(className)){
            //没有登记就进入同步块
            synchronized (RegistSingleton.class){
            //再次检测是否登记
                if (!REGIST.containsKey(className)){
                    try {
                    //实例化对象
                        REGIST.put(className,Class.forName(className).newInstance());
                    } catch (InstantiationException e) {
                        e.printStackTrace();
                    } catch (IllegalAccessException e) {
                        e.printStackTrace();
                    } catch (ClassNotFoundException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
        //返回单例
        return REGIST.get(className);
    }
}
```
## 总结
java中单例模式是一种常见的设计模式，单例模式分三种：懒汉式单例、饿汉式单例、登记式单例三种。
单例模式有一下特点：
1、单例类只能有一个实例。
2、单例类必须自己自己创建自己的唯一实例。
3、单例类必须给所有其他对象提供这一实例。
单例模式确保某个类只有一个实例，而且自行实例化并向整个系统提供这个实例。在计算机系统中，线程池、缓存、日志对象、对话框、打印机、显卡的驱动程序对象常被设计成单例。这些应用都或多或少具有资源管理器的功能。每台计算机可以有若干个打印机，但只能有一个Printer Spooler，以避免两个打印作业同时输出到打印机中。每台计算机可以有若干通信端口，系统应当集中管理这些通信端口，以避免一个通信端口同时被两个请求同时调用。总之，选择单例模式就是为了避免不一致状态，避免政出多头。