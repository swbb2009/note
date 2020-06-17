# Lombok

`@Getter / @Setter`：可以作用在类上和属性上，放在类上，会对所有的非静态(non-static)属性生成Getter/Setter方法，放在属性上，会对该属性生成Getter/Setter方法。并可以指定Getter/Setter方法的访问级别。

`@EqualsAndHashCode` ：默认情况下，会使用所有非瞬态(non-transient)和非静态(non-static)字段来生成equals和hascode方法，也可以指定具体使用哪些属性。

`@ToString `生成toString方法，默认情况下，会输出类名、所有属性，属性会按照顺序输出，以逗号分割。

`@NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor`：无参构造器、部分参数构造器、全参构造器

`@Data`：包含@ToString, @EqualsAndHashCode, 所有属性的@Getter, 所有non-final属性的@Setter和@RequiredArgsConstructor的组合，通常情况下，基本上使用这个注解就足够了。
**@Builder和@Accessors有一些相似之处**
`@Builder`：可以进行Builder方式初始化。不能用new的方式初始化，可以在初始化的时候链式调用set，如果字段有默认值需要加`@Default`注解，初始化后便不能用链式调用set，建造者模式
```
        UserEntity user = UserEntity.builder().username("liming").age("5").build();

```

`@Accessors` Accessor的中文含义是存取器，@Accessors用于配置getter和setter方法的生成结果，下面介绍三个属性

fluent的中文含义是流畅的，设置为true，则getter和setter方法的方法名都是基础属性名，且setter方法返回当前对象。如下

```
@Data
@Accessors(fluent = true)
public class User {
    private Long id;
    private String name;
    
    // 生成的getter和setter方法如下，方法体略
    public Long id() {}
    public User id(Long id) {}
    public String name() {}
    public User name(String name) {}
}
```

chain的中文含义是链式的，设置为true，则setter方法返回当前对象。如下
```
@Data
@Accessors(chain = true)
public class User {
    private Long id;
    private String name;
    
    // 生成的setter方法如下，方法体略
    public User setId(Long id) {}
    public User setName(String name) {}
}
```
prefix的中文含义是前缀，用于生成getter和setter方法的字段名会忽视指定前缀（遵守驼峰命名）。如下
```
@Data
@Accessors(prefix = "p")
class User {
	private Long pId;
	private String pName;
	
	// 生成的getter和setter方法如下，方法体略
	public Long getId() {}
	public void setId(Long id) {}
	public String getName() {}
	public void setName(String name) {}
}
```

