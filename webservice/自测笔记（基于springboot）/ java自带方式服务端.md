# java自带方式服务端

**1，暴露的webservice接口类**
```
import javax.jws.WebMethod;
import javax.jws.WebParam;
import javax.jws.WebService;

/**
 * 面向接口的webservice发布方式
 * @author Administrator
 *
 */
@WebService
public interface LoginService {

    public String login(String username, String password);

}
```
**2，webservice实现类（这里有个坑，详见下面注释）**
```
import com.limingda.webservice.server.Utils.SpringBeanUtils;
import com.limingda.webservice.server.service.LoginService;
import com.limingda.webservice.server.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.stereotype.Service;
import org.springframework.web.context.ContextLoaderListener;
import org.springframework.web.context.support.SpringBeanAutowiringSupport;
import org.springframework.web.context.support.WebApplicationContextUtils;

import javax.jws.WebMethod;
import javax.jws.WebParam;
import javax.jws.WebService;

@WebService(endpointInterface = "com.limingda.webservice.server.service.LoginService"
                  ,targetNamespace="http://xiu.limingda.com", serviceName="LoginServiceImpl")
public class LoginServiceImpl implements LoginService {

        //因为webservice不在spring的context里，所以在这里@autowird无效，要想注入spring管理的bean，只能通过工具类获取spring上下文
        //使用getBean()的方式注入
        private UserService userService;

        @Override
        public String login(@WebParam(name="username") String username, @WebParam(name="password") String password){
            userService = SpringBeanUtils.getBean(UserServiceImpl.class);
            return userService.find();

        }
}

```
**3，工具类，为了获取spring的上下文环境**
```
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Service;

@Service
public class SpringBeanUtils implements ApplicationContextAware {
    private static ApplicationContext context;
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.context=applicationContext;
    }
    public static <T> T getBean(Class<T> requiredType){

        if(context==null){
            throw new IllegalStateException("spring 环境没有启动！");
        }
        return context.getBean(requiredType);
    }
    public static <T> T getBean(String beanName,Class<T> requiredType){

        if(context==null){
            throw new IllegalStateException("spring 环境没有启动！");
        }
        return context.getBean(beanName,requiredType);

    }

    public static ApplicationContext getContext(){


        if(context==null){
            throw new IllegalStateException("spring 环境没有启动！");
        }
        return context;
    }
}

```
**4，服务的发布 ,简单通过一个controller方法进行发布**

```
import com.limingda.webservice.server.service.LoginService;
import com.limingda.webservice.server.serviceImpl.LoginServiceImpl;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.xml.ws.Endpoint;

@Controller
public class WebController {

    /*
        java自带webservice发布服务
     */
    @RequestMapping("/index")
    public String index(){
        LoginService jobService = new LoginServiceImpl();
        String address = "http://127.0.0.1:9999/ws/LoginService";
        //通过javax包自带方法发布
        Endpoint.publish(address, jobService);
        System.out.println("wsdl地址:"+address+"?WSDL");
        return "index";
    }
}
```