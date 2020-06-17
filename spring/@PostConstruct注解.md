# @PostConstruct注解
PostConstruct注释用于在完成依赖项注入以执行任何初始化之后需要执行的方法。必须在类投入使用之前调用此方法。
所有支持依赖注入的类都必须支持此注释。即使类没有请求注入任何资源，也必须调用使用PostConstruct注释的方法。
只有一个方法可以使用此批注进行批注。
应用PostConstruct注释的方法必须满足以下所有条件：除了拦截器之外，方法绝不能有任何参数，在这种情况下它采用Interceptor规范定义的InvocationContext对象。
在拦截器类上定义的方法必须具有以下签名之一：
void <METHOD>（InvocationContext）Object <METHOD>（InvocationContext）抛出异常注意：
PostConstruct拦截器方法不能抛出应用程序异常，但可以声明它抛出检查异常，包括java.lang.Exception，
如果相同的拦截器方法除了生命周期事件之外插入业务或超时方法。
如果PostConstruct拦截器方法返回一个值，容器将忽略它。
在非拦截器类上定义的方法必须具有以下签名：void <METHOD>（）应用PostConstruct的方法可以是public，protected，package private或private。
除应用程序客户端外，该方法绝不能是静态的。
该方法可能是最终的。如果该方法抛出一个未经检查的异常，那么该类绝不能投入使用，除非EJB可以处理异常甚至从它们恢复的EJB

```
@Service
public class BeanA {

    @Autowired
    private BeanB beanB;

    public BeanA() {
        System.out.println("这是Bean A 的构造方法");
    }


    @PostConstruct
    private void init() {
        System.out.println("这是BeanA的 init 方法");
        beanB.testB();
    }
}
```
```
@Service
public class BeanB {

    @PostConstruct
    private void init() {
        System.out.println("这是BeanB 的init 方法");
    }

    public BeanB() {
        System.out.println("这是Bean B的 构造方法");
    }

    void testB() {
        System.out.println("这是Bean B 的 testB 方法");
    }
}
```
输出
这是Bean A 的构造方法
这是Bean B的 构造方法
这是BeanB 的init 方法
这是BeanA的 init 方法
这是Bean B 的 testB 方法

所以得到结论： 构造方法 > @Autowired > @PostConstruct