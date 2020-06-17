# ehcache
ehcache.xml文件
```
<ehcache>
    <!-- 磁盘存储:将缓存中暂时不使用的对象,转移到硬盘,类似于Windows系统的虚拟内存 path:指定在硬盘上存储对象的路径 -->
    <diskStore path="java.io.tmpdir" />
    <!-- defaultCache:默认的缓存配置信息,如果不加特殊说明,则所有对象按照此配置项处理 maxElementsInMemory:设置了缓存的上限,最多存储多少个记录对象 
        eternal:代表对象是否永不过期 timeToIdleSeconds:最大的发呆时间 timeToLiveSeconds:最大的存活时间 overflowToDisk:是否允许对象被写入到磁盘 -->
    <defaultCache maxElementsInMemory="10000" eternal="false"
        timeToIdleSeconds="120" timeToLiveSeconds="120" overflowToDisk="true" />
    <!-- cache:为指定名称的对象进行缓存的特殊配置 name:指定对象的完整名 -->
    <cache name="ehcache" maxElementsInMemory="10000"
        eternal="false" timeToIdleSeconds="300" timeToLiveSeconds="600"
        overflowToDisk="true" />
</ehcache>

```

```
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-cache</artifactId>
    </dependency>
```

```
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.cache.annotation.CacheConfig;
import org.springframework.cache.annotation.Cacheable;

import cn.shinelon.entity.User;
@CacheConfig(cacheNames="ehcache")
public interface User1Dao {
    //使用ehcache缓存技术
    @Select("select * from user where id=#{id}")
    @Cacheable
    public User select(@Param("id")int id);
}

```
注意，上面的注释@CacheConfig(cacheNames=”ehcache”)的属性cacheNames的值与配置文件xml中cache中name的属性一致，@Cacheable注解表示我们这里将使用缓存。最后，我们来看一下controller层的代码。
```
import org.apache.log4j.Logger;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

import cn.shinelon.config.DBConfig1;
import cn.shinelon.config.DBConfig2;
import cn.shinelon.entity.User;
import cn.shinelon.test1.dao.User1Dao;
import cn.shinelon.test1.services.User1Service;
import cn.shinelon.test2.services.User2Service;

@SpringBootApplication
@EnableAutoConfiguration
@ComponentScan(basePackages={"cn.shinelon.aop","cn.shinelon.config","cn.shinelon.datasource",
        "cn.shinelon.test1","cn.shinelon.test2"})
@RestController
@EnableConfigurationProperties(value= {DBConfig1.class,DBConfig2.class})
@EnableCaching      //支持缓存
public class UserController {
    //测试缓存
    @Autowired
    private User1Dao user1Dao;
    @ResponseBody
    @RequestMapping("/cache")
    public String cache(int id) {
        return user1Dao.select(id).toString();
    }

```
然后，我们访问使用localost:8080/cache？id=4来访问服务器，可以访问到数据，然后删除数据库中的这条数据，继续访问，还是可以查询到数据，因为这条数据已经存入到缓存中了，这时，有一个问题，有时我们不需要缓存，这时该怎么清除缓存呢？这很简单，我们只需要在controller层通过cacheManager来管理清除缓存即可，需要插入的代码是：

```
    //清除缓存
    @Autowired
    private CacheManager cacheManager;
    @ResponseBody
    @RequestMapping("/clear")
    public String clear() {
        cacheManager.getCache("ehcache").clear();
        return "success";
    }
```
接着，我们通过localhost:8080/clear访问来清除缓存后，再查询上面的那条数据，服务器将会报出不存在该记录的信息，至此，我们了解到了ehcahe缓存技术的使用，这也在项目的开发中经常使用。