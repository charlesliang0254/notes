<h1>Spring框架补充学习笔记</h1>

## lombok学习笔记

1、引入依赖：在创建Spring Boot项目的时候添加lombok支持，或者在maven中添加lombok的依赖项。

2、给IDEA添加lombok支持：打开设置（Settings），在插件市场中搜索并安装lombok，在注解处理器（annotation processor）中开启注解处理

3、实现原理：实现JSR 269 API，修改抽象语法树，然后再生成字节码文件

4、使用方法：

@Getter/@Setter：作用在类上，生成所有字段的getter、setter；作用在字段上，生成单个字段的getter、setter；可以指定访问控制类型、懒加载

@ToString：作用在类上，可以通过of属性显示某些字段，通过exclude属性排除某些字段	

@EqualsAndHashcode：重写equals和hashcode方法

@NonNull：作用于成员变量和参数中，表示不能为空，否则抛出空指针异常

@NoArgsConstructor, @RequiredArgsConstructor, @AllArgsConstructor：作用于类上，用于生成构造方法。有staticName、access等属性。三个注解分别表示无参构造器、包含final和NonNull字段的构造器、全参构造器

@Data：作用于类上，是以下注解的集合：@ToString、@EqualsAndHashcode、@Getter、@Setter、@RequiredArgsConstructor

@Builder：作用于类上，将类转变为建造者模式

```java
User user = User.builder()
    .username("ljh")
    .password("123")
    .uid(123L).build();
```

@Log：作用于类上，生成日志变量。针对不同的日志实现产品，有不同的注解

@Cleanup：自动关闭资源，针对实现了Closeable接口的对象有效

@SneakyThrows：可以对受检异常进行捕捉并抛出