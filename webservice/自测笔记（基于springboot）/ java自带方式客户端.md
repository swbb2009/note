#  java自带方式客户端

**1，获取wsdl通过命令生成java文件，参数为字符集，包名，生成代码的文件夹层级，wsdl地址**
./wsdl2java -encoding utf-8 -p com.limingda.webservice.client.client -d d:\cxfClient -client http://127.0.0.1:9999/ws/LoginService?WSDL

**2，客户端调用服务端**
```
 @RequestMapping("/index1")
    public String index1(){
        LoginServiceImpl ws = new LoginServiceImpl();
        LoginService loginServiceImplPort = ws.getLoginServiceImplPort();
        String returns = loginServiceImplPort.login("123","456");
        System.out.println(returns);
        return "index";
    }
```