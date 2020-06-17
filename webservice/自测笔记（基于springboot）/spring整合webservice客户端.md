# spring整合webservice客户端

1，配置一个JaxWsPortProxyFactoryBean的bean来调用服务端（这里需要加上@lazy，因为服务端一但连接不上那么spring容器启动初始化这个bean会报错）
```
import com.limingda.webservice.client.client.LoginService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Lazy;
import org.springframework.remoting.jaxws.JaxWsPortProxyFactoryBean;

import java.net.MalformedURLException;
import java.net.URL;
import java.util.HashMap;
import java.util.Map;

@Configuration
public class WebServiceConfig {

    /*
        声明了一个webservice客户端bean 类型为LoginService
     */
    @Bean
    @Lazy
    public JaxWsPortProxyFactoryBean loginWsService() throws MalformedURLException {
        JaxWsPortProxyFactoryBean proxyFactoryBean = new JaxWsPortProxyFactoryBean();
        proxyFactoryBean.setWsdlDocumentUrl(new URL("http://127.0.0.1:9999/ws/LoginServiceImpl?WSDL"));
        proxyFactoryBean.setServiceName("LoginServiceImpl");
        proxyFactoryBean.setPortName("LoginServiceImplPort");
        proxyFactoryBean.setServiceInterface(LoginService.class);
        proxyFactoryBean.setNamespaceUri("http://xiu.limingda.com");
        //设置超时时间  未测试，不确定是否正确
//        Map<String, Object> customProperty = new HashMap<>(2);
//        customProperty.put("javax.xml.ws.client.receiveTimeout", 5000);
//        customProperty.put("javax.xml.ws.client.connectionTimeout", 5000);
//        proxyFactoryBean.setCustomProperties(customProperty);
        return proxyFactoryBean;
    }
}
```

2，进行调用服务端 将上边声明的bean注入 LoginService，也需要加上@lazy懒加载，防止服务端wsdl访问不到启动报错
```
import com.limingda.webservice.client.client.LoginService;
import com.limingda.webservice.client.client.LoginServiceImpl;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Lazy;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.xml.ws.Service;

@Controller
public class WebController {

    @Autowired(required = false)
    @Lazy
    private LoginService loginWsServices;

    @RequestMapping("/index")
    public String index(){
        String returns = loginWsServices.login("123","456");
        System.out.println(returns);
        return "index";
    }

}

```