# spring_rmi
**服务端**
首先将一个service发布为一个rmi服务
```
@Configuration
public class BeanConfig {
    @Bean
    public RmiServiceExporter  rmiServiceExporter(PersonServiceImpl personService){
        //RmiServiceExporter可以把spring管理的bean发布为RMI服务
        RmiServiceExporter rmiServiceExporter = new RmiServiceExporter();
        //创建的RMI服务名
        rmiServiceExporter.setServiceName("PersonService");
        //创建服务实际的业务处理（实现了暴露接口的bean）
        rmiServiceExporter.setService(personService);
        //暴露为RMI服务的接口
        rmiServiceExporter.setServiceInterface(PersonService.class);
        return rmiServiceExporter;
    }
}
```
service rmi暴露接口
```
public interface PersonService {
    String test();
}

```
service实现类
```
@Service("personService")
public class PersonServiceImpl implements PersonService {

    private Logger log = LoggerFactory.getLogger(this.getClass());

    @PostConstruct
    public void init(){
        log.info("PersonService 初始化");
    }
    @Override
    public String test(){
        return "limingda";
    }
}

```


**客户端**

```
@Bean
public RmiProxyFactoryBean rmiProxyFactoryBean(){
    //创建一个远程代理bean
    RmiProxyFactoryBean rmiProxyFactoryBean = new RmiProxyFactoryBean();
    //设置远程调用服务url
    rmiProxyFactoryBean.setServiceUrl("rmi://localhost/PersonService");
    //设置调用远程服务实现的接口类
    rmiProxyFactoryBean.setServiceInterface(PersonService.class);
    return rmiProxyFactoryBean;
}
```
service接口，正常应该通过jar包引入服务端的接口
```
public interface PersonService {
    String test();
}
```
测试类
```
@Service
public class TestService {

    private Logger log = LoggerFactory.getLogger(this.getClass());
    //注入PersonService的远程服务实现
    @Autowired
    private PersonService personService;
    
    @PostConstruct
    public void init(){
      //测试是否调用成功
      String string =  personService.test();
      log.info(string);
    }
}

```

