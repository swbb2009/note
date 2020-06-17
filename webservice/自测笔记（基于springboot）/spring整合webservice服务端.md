# spring整合webservice服务端

**1，服务端暴露接口类**
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
**2，服务端接口实现类**
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

@Component
@WebService(endpointInterface = "com.limingda.webservice.server.service.LoginService"
                  ,targetNamespace="http://xiu.limingda.com", serviceName="LoginServiceImpl")
public class LoginServiceImpl implements LoginService {
        
        //随意的一个业务service
        @Autowired
        private UserService userService;

        @Override
        public String login(@WebParam(name="username") String username, @WebParam(name="password") String password){
        
            return userService.find();
        }


}
```
**3，服务端发布**（这个SimpleJaxWsServiceExporter的bean会通过spring容器启动自动发布）
```
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.remoting.jaxws.SimpleJaxWsServiceExporter;

@Configuration
public class WebServiceConfig {

    @Bean
    public SimpleJaxWsServiceExporter jaxWsServiceExporter(){
        SimpleJaxWsServiceExporter simpleJaxWsServiceExporter = new SimpleJaxWsServiceExporter();
        //客户端通过下边 "http://127.0.0.1:9999/ws/"   + 服务端接口实现类注解中的serviceName参数：LoginServiceImpl
        //通过http://127.0.0.1:9999/ws/LoginServiceImpl?wsdl 可以访问到wsdl文件 
        simpleJaxWsServiceExporter.setBaseAddress("http://127.0.0.1:9999/ws/");
        return simpleJaxWsServiceExporter;
    }
}

```
