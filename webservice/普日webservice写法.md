# 普日webservice写法
1，	使用了cxf调用webservice方法，首先下载cxf压缩包，百度官网下即可

2，	cxf  bin目录下     
```
 wsdl2java -encoding utf-8 -p com -d d:\cxfClient -client d:\www.wsdl
参数的意义：   -p是生成代码的包名，-d是生成代码的文件夹路径（需要先创建）
              -client 是生成客户端代码  最后是需要生成代码的wsdl文档的路径
```
3，以厦门中医人事接口为例子
    一，POM依赖中加入
```
        <dependency>
            <groupId>org.apache.cxf</groupId>
            <artifactId>cxf-api</artifactId>
            <version>2.6.7</version>
        </dependency>
        <dependency>
            <groupId>org.apache.cxf</groupId>
            <artifactId>cxf-rt-frontend-jaxws</artifactId>
            <version>2.6.7</version>
        </dependency>
        <dependency>
            <groupId>org.apache.cxf</groupId>
            <artifactId>cxf-rt-bindings-soap</artifactId>
            <version>2.6.7</version>
        </dependency>
        <dependency>
            <groupId>org.apache.cxf</groupId>
            <artifactId>cxf-rt-transports-http</artifactId>
            <version>2.6.7</version>
        </dependency>
        <dependency>
            <groupId>org.apache.cxf</groupId>
            <artifactId>cxf-rt-ws-security</artifactId>
            <version>2.6.7</version>
        </dependency>
```
二、spring配置文件module.xml
头文件
```
<beans xmlns=http://www.springframework.org/schema/beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:jee="http://www.springframework.org/schema/jee" xmlns:tx="http://www.springframework.org/schema/tx"
xmlns:context="http://www.springframework.org/schema/context" xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:jaxws="http://cxf.apache.org/jaxws"  
    xmlns:cxf="http://cxf.apache.org/core"
    xmlns:http-conf="http://cxf.apache.org/transports/http/configuration"
    
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
    http://www.springframework.org/schema/tx 
    http://www.springframework.org/schema/tx/spring-tx-3.0.xsd 
    http://www.springframework.org/schema/jee 
    http://www.springframework.org/schema/jee/spring-jee-3.0.xsd 
    http://www.springframework.org/schema/aop 
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd 
    http://www.springframework.org/schema/context 
    http://www.springframework.org/schema/context/spring-context-3.0.xsd 
	http://cxf.apache.org/jaxws 
	http://cxf.apache.org/schemas/jaxws.xsd
    http://cxf.apache.org/transports/http/configuration 
    http://cxf.apache.org/schemas/configuration/http-conf.xsd"
default-autowire="byName">
```
添加一个cxf的bean实例用于autowird注入
```
<jaxws:client id="personWSInterfacePortType" serviceClass="${ws.person.serviceClass.catlog}" address="${ws.person.endPointAddress}" />
    <http-conf:conduit name="*.http-conduit">
        <http-conf:client ConnectionTimeout="${person.ConnectionTimeout}" ReceiveTimeout="${person.ReceiveTimeout}"/>
    </http-conf:conduit>
    
     <bean id="levelCode" class="java.lang.String">
		<constructor-arg value="${levelCode}"></constructor-arg>
	</bean>
```
conf.properties
```
ws.person.endPointAddress=http://192.169.2.89:1506/services/WSInterface?wsdl
ws.person.serviceClass.catlog=com.proper.hr.personnel.webservice.client.WSInterfacePortType
person.ConnectionTimeout=30000    
person.ReceiveTimeout=600000
```
调用流程：以人事新增人员接口为例子（具体看代码）
1，新增人员 提交后 人员数据存完业务表中后  调用推送的服务本例中为PushService
2，PushService中调用工具类PushTOHisUtil中的方法，进行推送方法中参数的xml的组装（具体拼装看服务方给的接口文档）
3，组装完毕后，通过WSInterfacePortType这个生成的类进行方法调用推送，并保存日志到数据库（这里是使用的spring的线程池进行多线程的推送）
