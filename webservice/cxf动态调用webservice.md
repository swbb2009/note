# cxf动态调用webservice
```
import java.util.Random;
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

import org.apache.cxf.endpoint.Client;
import org.apache.cxf.endpoint.dynamic.DynamicClientFactory;
import org.apache.cxf.jaxws.endpoint.dynamic.JaxWsDynamicClientFactory;
import org.apache.cxf.transport.http.HTTPConduit;
import org.apache.cxf.transports.http.configuration.HTTPClientPolicy;

public class WSClient {

    public static void main(String[] args) throws Exception {

        String wsdlUrl = "http://10.35.180.55:18090/services/OpenService?wsdl";
        // 动态调用的客户端工厂类
        DynamicClientFactory dcf = JaxWsDynamicClientFactory.newInstance();  
        final Client client = dcf.createClient(wsdlUrl);  
        // 设置超时单位为毫秒
        HTTPConduit conduit = (HTTPConduit)client.getConduit();  
        HTTPClientPolicy policy = new HTTPClientPolicy();  
        policy.setConnectionTimeout(15000);
        policy.setAllowChunking(false);
        policy.setReceiveTimeout(30000);
        conduit.setClient(policy);
        
        final String param = "<?xml version=\"1.0\" encoding=\"UTF-8\"?><DATA><OUTPUT><STATUS></STATUS><MESSAGE></MESSAGE></OUTPUT></DATA>";
        Object[] objects = client.invoke("openService", param);
        System.out.println(objects);

        // 用线程池试了下client对象线程安全性，发现是安全的
        ThreadPoolExecutor pool = new ThreadPoolExecutor(5, 50, 1000, TimeUnit.MICROSECONDS,
                new ArrayBlockingQueue<Runnable>(50));
        for (int i = 0; i < 100; i++) {
            pool.execute(new Runnable() {
                @Override
                public void run() {
                    try {
                        String threadName = Thread.currentThread().getName();
                        int a = new Random().nextInt(10);
                        int b = new Random().nextInt(10);
                        Object[] res = client.invoke("openService",param);
                        System.out.println(threadName + ":" + a + "+" + b + "=" + res[0]);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            });
        }
    }
    
}
```