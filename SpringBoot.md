# 1.SpringBoot2入门

## 1.1 maven设置

```xml
<mirrors>
      <mirror>
        <id>nexus-aliyun</id>
        <mirrorOf>central</mirrorOf>
        <name>Nexus aliyun</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
      </mirror>
  </mirrors>
 
  <profiles>
         <profile>
              <id>jdk-1.8</id>
              <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>1.8</jdk>
              </activation>
              <properties>
                <maven.compiler.source>1.8</maven.compiler.source>
                <maven.compiler.target>1.8</maven.compiler.target>
                <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
              </properties>
         </profile>
  </profiles>
```

## 1.2 一个HelloWorld程序

需求：浏览发送/hello请求，响应 Hello，Spring Boot 2 

1. 创建一个maven项目

2. 引入依赖，自带常见的依赖

   ```xml
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-parent</artifactId>
       <version>2.3.4.RELEASE</version>
   </parent>
   
   
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
   </dependencies>
   ```

3. 创建主程序

   ```java
   /**
    * 主程序类
    * @SpringBootApplication：这是一个SpringBoot应用
    */
   @SpringBootApplication
   public class MainApplication {
   
       public static void main(String[] args) {
           SpringApplication.run(MainApplication.class,args);
       }
   }
   ```

4. 编写控制类

   ```java
   // @RestController是由@Controller和@ResponseBody定义的
   @RestController
   public class HelloController {
   
       @RequestMapping("/hello")
       public String handle01(){
           return "Hello, Spring Boot 2!";
       }
   }
   ```

5. 书写配置文件简化配置

   配置文件名：application.properties

   ```xml
   配置端口号
   server.port=8888
   ```

6. 简化部署

   把项目打成jar包，直接在目标服务器执行即可。

```xml
 <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

执行方式为在cmd控制台jar包路径下输入：

```xml
java -jar 01-HelloWorld-1.0-SNAPSHOT.jar
```

原本需要在pom.xml文件中配置

```xml
<packaging>jar</packaging>
```

注意点：

- 取消掉cmd的快速编辑模式

# 2.SpringBoot2特点

## 2.1 依赖管理

- 父项目做依赖管理，几乎声明了所有开发中常用的依赖的版本号,自动版本仲裁机制

```xml
依赖管理    
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
</parent>

他的父项目
 <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.3.4.RELEASE</version>
  </parent>
```

- 开发导入starter场景启动器

```xml
1、见到很多 spring-boot-starter-* ： *就某种场景
2、只要引入starter，这个场景的所有常规需要的依赖我们都自动引入
3、SpringBoot所有支持的场景
https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter
4、见到的  *-spring-boot-starter： 第三方为我们提供的简化开发的场景启动器。
5、所有场景启动器最底层的依赖
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
  <version>2.3.4.RELEASE</version>
  <scope>compile</scope>
</dependency>
```

- 无需关注版本号，自动版本仲裁

```xml
1、引入依赖默认都可以不写版本
2、引入非版本仲裁的jar，要写版本号。仲裁版本号查看地址:进入org.springframework.boot
```

- 可以修改默认版本号

```xml
1、查看spring-boot-dependencies里面规定当前依赖的版本用的 key。
2、在当前项目里面重写配置
    <properties>
        <mysql.version>5.1.43</mysql.version>
    </properties>
```

## 2.2 自动配置

### 2.2.1 自动配置Tomcat

- - 引入Tomcat依赖。

- - 配置Tomcat

```
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <version>2.3.4.RELEASE</version>
      <scope>compile</scope>
</dependency>
```

### 2.2.2 自动配置SpringMVC及Web常见功能

- - 引入SpringMVC全套组件
  - 自动配好SpringMVC常用组件（功能）：如dispatcherServlet
  - 自动配好Web常见功能，如：字符编码问题

- - SpringBoot帮我们配置好了所有web开发的常见场景

### 2.2.3 默认的包扫描规则

- - 主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来
  - 无需以前的包扫描配置
  - 想要改变扫描路径，@SpringBootApplication(scanBasePackages=**"com.atguigu"**)

- - - 或者@ComponentScan 指定扫描路径

```
@SpringBootApplication
等同于以下三个注解（此注解由以下三个注解定义）
@SpringBootConfiguration
@EnableAutoConfiguration
	@AutoConfigurationPackage（自动配置包，将主配置类所在包下的所有子包扫描进Spring容器中）
	@Import({AutoConfigurationImportSelector.class})（将所有需要导入的组件以全类名的方式返回并添加到容器中并配置好）
@ComponentScan("com.atguigu.boot")
```

### 2.2.4 配置文件

- - 默认配置最终都是映射到某个类上，如：MultipartProperties（文件上传限制的大小）
  - 配置文件的值最终会绑定每个类上，这个类会在容器中创建对象
- 按需加载所有自动配置项
- - 非常多的starter
  - 引入了哪些场景这个场景的自动配置才会开启
  - SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面
  - 
- ......



### 2.2.4 自动装配原理

​	       Springboot所有的自动配置都是在启动的时候扫描spring.factories并加载，但是不一定生效，具有判断条件（是否导入了对应的start），条件满足，就会有对应的启动器，有了启动器，自动装配就会生效，然后配置成功。                               

![image-20210617144031474](C:\Users\siicfl\AppData\Roaming\Typora\typora-user-images\image-20210617144031474.png)

​    具体步骤如下：

（1）  Springboot在启动的时候，从类路径下/META-INF/Spring.factories获取指定的值（自动配置类，以全类名的形式返回）

（2）  然后会将这些自动配置的类（XXXAutoConfiguration）导入容器，这些类会给容器导入场景所需要的组件（@Bean）并自动配置

## 2.3 容器功能

### 2.3.1 组件添加

类似Spring2.3节，接下来只写不同点：

- **@Configuration**：多了一个proxyBeanMethods = true/false属性，用于指定是否给配置类创建代理对象，从而设定是否**@Bean**注解的方法被代理从而实现bean的生命周期的行为。
  - proxyBeanMethods = true为Full模式，从容器外部调用配置类的方法获取对象（mainConfig.user()）会直接从容器中获取对象，因为容器中保存了配置类的代理对象会对容器容器中是否已有该对象做出判断
  - proxyBeanMethods = false为Lite模式，每次都会新创建对象
  - 与singleton不同的是，singleton模式是表示从容器中取对象是否为单例，而代理配置类是从外部调用配置类方法获取**@Bean**定义的对象是否为单例

```java
// 若proxyBeanMethods为ture表示开启代理，如果有Bean那么直接从容器中获取单实例的Bean没有就创建新的bean，并且走Bean的生命周期，false会创建新的Bean而不走Bean的生命周期
@Configuration(proxyBeanMethods = true)
public class MainConfig{
    @Bean
    public User user(){
       return new User();
    }
}

MainConfig bean = run.getBean(MainConfig.class);

User user1 = bean.user(); // 调用配置类的方法获取对象
User user2 = bean.user();
System.out.println(user1 == user2); // ture
```

```java
@Configuration(proxyBeanMethods = false)
public class MyConfig {

    /**
     * Full:外部无论对配置类中的这个组件注册方法调用多少次获取的都是之前注册容器中的单实例对象
     * @return
     */
    @Bean //给容器中添加组件。以方法名作为组件的id。返回类型就是组件类型。返回的值，就是组件在容器中的实例
    public User user01(){
        User zhangsan = new User("zhangsan", 18);
        //user组件依赖了Pet组件
        zhangsan.setPet(tomcatPet()); // 调用的还是配置类的方法
        return zhangsan;
    }

    @Bean("tom")
    public Pet tomcatPet(){
        return new Pet("tomcat");
    }
}

#############################@Configuration测试代码如下####################################
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("com.atguigu.boot")
public class MainApplication {

    public static void main(String[] args) {
        //1、返回我们IOC容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        //2、查看容器里面的组件
        String[] names = run.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println(name);
        }

        //3、从容器中获取组件

        Pet tom01 = run.getBean("tom", Pet.class);

        Pet tom02 = run.getBean("tom", Pet.class);

        System.out.println("组件："+(tom01 == tom02));


        //4、com.atguigu.boot.config.MyConfig$$EnhancerBySpringCGLIB$$51f1e1ca@1654a892
        MyConfig bean = run.getBean(MyConfig.class);
        System.out.println(bean);

        //如果@Configuration(proxyBeanMethods = true)代理对象调用方法。SpringBoot总会检查这个组件是否在容器中有。
        //保持组件单实例
        User user = bean.user01();
        User user1 = bean.user01();
        System.out.println(user == user1);


        User user01 = run.getBean("user01", User.class);
        Pet tom = run.getBean("tom", Pet.class);

        System.out.println("用户的宠物："+(user01.getPet() == tom));



    }
}
```

- **@Bean**、**@Component**、**@Controller**、**@Service**、**@Repository**、**@Import**：向容器中添加组件

- **@ComponentScan**：指定包扫描注解范围

- **@Conditional**：按照一定条件进行组件注入，SpringBoot中多了很多新的条件注解:

  - **@ConditionalOnBean**：可以加在类上或方法上，表示容器中有xxx组件时注入定义的组件

  - **@ConditionalOnMissingBean**：容器中没有xxx组件时，注入定义的组件

  - ...

    ![img](assets/image.png)

    注意：作为条件的组件方法一定要写在前面

    ```java
    @Configuration(proxyBeanMethods = false)
    public class MainConfig {
    	
        // 这个方法一定要写在下个方法之前
        @Bean
        public Cart cart(){
            return new Cart();
        }
    
        @ConditionalOnBean(Cart.class)
        @Bean
        public User user(){
            return new User();
        }
    }
    ```

- **@ImportResource**：可以在配置类上加上此注解，导入Sping配置文件，这样配置文件的功能就生效了

  ```java
  @ImportResource("classpath:applicationCOntext.xml")
  ```

### 2.3.2 配置绑定

- **@ConfigurationProperties**+**@Component**

  定义在实体类上

```java
/**
 * 只有在容器中的组件，才会拥有SpringBoot提供的强大功能，因此需要加上@Component
 * 根据配置列中的前缀进行属性和依赖（一个对象赋值给另一个对象）注入
 */
@Component
@ConfigurationProperties(prefix = "user")
public class User {
    @Value("${user.username:zhuwenchuan}") // 单个属性注入，若没有默认为zhuwenchuan
    private String username;
    private Integer age;
    private Cart cart;

    public User() {
    }

    public User(String username, Integer age, Cart cart) {
        this.username = username;
        this.age = age;
        this.cart = cart;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Cart getCart() {
        return cart;
    }

    public void setCart(Cart cart) {
        this.cart = cart;
    }

    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", age=" + age +
                ", cart=" + cart +
                '}';
    }
}
```

```properties
user.userName=zhu
user.age=11
```

- **@EnableConfigurationProperties** + **@ConfigurationProperties**+**@Component**

  **@EnableConfigurationProperties**定义在配置类上，指定哪个类需要配置绑定，并会自动创建指定组件，用于引入第三方组件

  ```java
  /**
   * 功能：
   * 1.开启属性配置功能，并且标定哪个组件需要属性注入
   * 2.自动创建组件，无需@Component，但是从容器中取出时需要写user-com.zhuwenchuan.pojo.User
   */
  @EnableConfigurationProperties(User.class)
  ```

## 2.4 自动配置原理

### 2.4.1 引导加载自动配置类

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
      @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
    
}
```

以上等同于注解**@SpringBootApplication**

- **@SpringBootConfiguration**由**@Configuration**定义，代表当前是一个配置类
- **@ComponentScan**：指定扫描哪些包下的Spring注解

- **@EnableAutoConfiguration**：由两个注解定义

  ```java
  @AutoConfigurationPackage // 自动配置包
  @Import(AutoConfigurationImportSelector.class)
  public @interface EnableAutoConfiguration {
  
  }
  ```

  - **@AutoConfigurationPackage**：自动配置包

    ```java
    @Import(AutoConfigurationPackages.Registrar.class)  //给容器中导入一系列组件
    public @interface AutoConfigurationPackage {
        
    }
    /**
     * 利用Registrar给容器中导入一系列组件
     * 将指定的一个包下的所有组件导入进来？MainApplication所在包下。
     *  Registrar底层代码register(registry, new PackageImports(metadata)
     *                                     .getPackageNames().toArray(new String[0]));
     */
    ```

  - **@Import(AutoConfigurationImportSelector.class)**

    ```java
    AutoConfigurationImportSelector.class也就是自定义注册bean
    
    1、利用getAutoConfigurationEntry(annotationMetadata);给容器中批量导入一些组件
    
    2、调用List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes)获取到所有需要导入到容器中的配置类
    
    3、利用工厂加载 Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader)；得到所有的组件
    
    4、从META-INF/spring.factories位置来加载一个文件从而加载加载组件
        默认扫描我们当前系统里面所有META-INF/spring.factories位置的文件
        spring-boot-autoconfigure-2.3.4.RELEASE.jar包里面也有META-INF/spring.factories
    ```

### 2.4.2 按需开启自动配置项

​        虽然我们127个场景的所有自动配置启动的时候默认全部加载。xxxxAutoConfiguration按照条件装配规则（@Conditional），最终会按需配置是否需要加载类。

### 2.4.3 修改用户的配置

```java
@Bean
// 容器中有这个类型组件
@ConditionalOnBean(MultipartResolver.class) 

// 容器中没有这个名字 multipartResolver 的组件，意味着用户的文件上传解析器名字不规范
@ConditionalOnMissingBean(name = DispatcherServlet.MULTIPART_RESOLVER_BEAN_NAME)

public MultipartResolver multipartResolver(MultipartResolver resolver) {
    
    // 给@Bean标注的方法传入了对象参数，这个参数的值就会从容器中找MultipartResolver，并且返回一个规范的名字multipartResolver
    // SpringMVC multipartResolver。防止有些用户配置的文件上传解析器不符合规范
    // Detect if the user has created a MultipartResolver but named it incorrectly
    return resolver;
    
}
给容器中加入了文件上传解析器；
```

​		底层有大量的**@Bean**与**@ConditionalOnMissingBean**，来查找容器中是否有用户自定义的组件，没有就由底层创建，有的话根据用户的。

总结：

- SpringBoot先加载所有的自动配置类  xxxxxAutoConfiguration
- 每个自动配置类按照条件进行生效，默认都会绑定配置文件指定的值。xxxxProperties里面拿。xxxProperties和配置文件进行了绑定
- 生效的配置类就会给容器中装配很多组件
- 只要容器中有这些组件，相当于这些功能就有了
- 定制化配置

- - 用户直接自己@Bean替换底层的组件
  - 用户去看这个组件是获取的配置文件什么值就去修改。

**xxxxxAutoConfiguration ---> 组件  --->** **xxxxProperties里面拿值但是受限制于@Conditional注解  ----> 所以要看application.properties是否由用户定义了，如字符编码集server.servlet.encoding,charset=GBK，如果自定义了那么久覆盖默认行为**

### 2.4.4 自动配置实践

- 查看官方文档引入场景依赖

- - <https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter>

- 查看自动配置了哪些（选做）

- - 自己分析，引入场景对应的自动配置一般都生效了
  - 配置文件中debug=true开启自动配置报告。Negative matches（不生效）\Positive matches（生效）

- 修改默认的自动配置

- - 参照文档修改配置项去替换默认配置

- - - <https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html#common-application-properties>
    - 自己分析。xxxxProperties绑定了配置文件的哪些。

- - 自定义加入或者替换组件

- - - @Bean、@Component。。。

- - 自定义器  **XXXXXCustomizer**；
  - ......

## 2.5 SpringBoot启动常见错误

### 2.5.1 8080端口被占用

1. win+r 打开cmd ，输入命令 netstat -aon|findstr "8080"
2.  输入命令格式：taskkill /t /f /pid 8080 进程id，杀掉8080进程

### 2.5.2 程序包SpringBoot不存在解决方案

勾选File->Settings->Maven->Runner->Delegate IDE build/run actions to Maven

### 2.5.3 Test阶段报错

程序中有错误，一般式mybatis没有在配置文档中进行配置

## 2.6 简化开发的技巧

### 2.5.1 Lombok

简化JavaBean开发

```xml
引入依赖，并且在idea的settings->plugins搜索lombok插件安装
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

```java
@Component
@Data
@NoArgsConstructor // 无参构造器
@AllArgsConstructor // 全参构造器
@EqualsAndHashCode // equals方法和hashcode方法
@ConfigurationProperties(prefix = "user")
public class User {

    private String username;

    private Integer age;

    @Autowired
    private Cart cart;
}
```

### 2.5.2 Spring Initailizr创建项目

File->New Project->选择Spring Initializr->Next->修改Group和Artifact（只能小写）->选择开发环境(Web/SQL等)

![image.png](assets/image-1614686787636.png)

# 3.配置文件

## 3.1基本语法

- key: value；kv之间有空格
- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释
- 字符串无需加引号，如果要加，双引号不会将\n作为换行输出，而单引号会直接输出\n字符串

## 3.2 各数据类型表示方法

- 字面量：单个的、不可再分的值。date、boolean、string、number、null

```yml
k: v
```

- 对象：键值对的集合。map、hash、set、object 

```yml
行内写法：  k: {k1:v1,k2:v2,k3:v3}
#或
k: 
  k1: v1
  k2: v2
  k3: v3
```

- 数组：一组按次序排列的值。array、list、queue

```yml
行内写法：  k: [v1,v2,v3]
#或者
k:
 - v1
 - v2
 - v3
```

示例

```yml
# 简单数据类型
person:
  userName: zhangsan
  # 单引号会将\n作为字符串输出，双引号会作为换行输出不会进行转义
  boss: true
  birth: 2019/12/9
  age: 18
  
# 数组赋值
#  interests: [篮球,足球]
  interests:
    - 篮球
    - 足球
  animal: [狗,猫]

# 存放两个简单数据类型赋值
#  score:
#    english: 80
#    math: 90
  score: {english:80, math:90}
  salarys:
    - 999.98
    - 999.99

  # 对象赋值
  pet:
    name: 狗
    weight: 99.99
  
  # map中含有list集合
  allPets:
    sick:
      - {name: 狗, weight: 99.99}
      - name: 猫
        weight: 88.8
    health:
      - {name: 猪, weight: 99}
      - {name: 牛, weight: 100}
```

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>

打包时不用带上spring-boot-configuration-processor
 <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.springframework.boot</groupId>
                            <artifactId>spring-boot-configuration-processor</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

# 4.Web开发

## 4.1 静态资源的访问

### 4.1.1 静态资源默认目录

**静态资源默认目录**：

​		只要静态资源放在类路径下都能由静态资源处理器进行处理： called `/static` (or `/public` or `/resources` or `/META-INF/resources`

**访问方式** :

​		当前项目根路径"/"(即localhost:8080/) + 静态资源名，因为静态资源的映射默认为/**包含了类路径下所有包

**注意**：

​		请求进来，先去找Controller看能不能处理。不能处理的所有请求又都交给静态资源处理器。静态资源也找不到则响应404页面

```java
@RestController
public class HelloController {

    @Autowired
    private Person person;
	
    // 虽然静态资源也是这个路径但是请求会优先交给Controller
    @RequestMapping("/bug.jpg")
    public Person person(){
        return person;
    }
}
```

**改变静态资源默认目录**：

```yml
spring:  
  web:
    resources:
      static-locations: classpath:/customerized/
```

### 4.1.2 静态资源访问前缀

- 默认状态下为/**无前缀

- 用户在yml自定义前缀，防止拦截器将静态资源也拦截课，网页中的地址为:

  ```yml
  spring:
    mvc:
      static-path-pattern: /resources/**
  ```

## 4.2 欢迎页支持

- 静态资源路径下  index.html

- - 可以配置静态资源路径
  - 但是不可以配置静态资源的访问前缀。否则导致 index.html不能被默认访问，因为底层写死了必须/**才可以访问欢迎页，而配置了静态资源路径必须要加上/resources/

```yml
spring:
#  mvc:
#    static-path-pattern: /res/**   这个会导致welcome page功能失效

  resources:
    static-locations: [classpath:/haha/]
```

- 自定义 Favicon

  favicon.ico 放在静态资源目录下即可。

  ```yml
  spring:
  #  mvc:
  #    static-path-pattern: /res/**   这个会导致 Favicon 功能失效，因为默认就是/**路径下的
  ```

## 4.3 请求参数处理

### 4.3.1 请求映射的使用规范

- @xxxMapping；
- Rest风格支持（*使用**HTTP**请求方式动词来表示对资源的操作*）

- - *以前：**/getUser*   *获取用户*     */deleteUser* *删除用户*    */editUser*  *修改用户*       */saveUser* *保存用户*
  - *现在： /user*    *GET-**获取用户*    *DELETE-**删除用户*     *PUT-**修改用户*      *POST-**保存用户*
  - 核心Filter；由于全段页面没有DELETE和PUT请求方式，因此需要引入HiddenHttpMethodFilter

- - - 用法： 前端中表单method=post（必须为POST请求才会受理隐藏域中的请求方式），隐藏域 设置_method=put

      ```jsp
      <form action="/user" method="post">
      	<input name="_method" type="hidden" value="DELETE"/>
      </form>
      ```

      ```java
      // @RequestMapping(value = "/user",method = RequestMethod.GET)
      @GetMapping("/user")
      public String getUser(){
          return "GET-张三";
      }
      
      // @RequestMapping(value = "/user",method = RequestMethod.POST)
      @PostMapping("/user")
      public String saveUser(){
          return "POST-张三";
      }
      
      @RequestMapping(value = "/user",method = RequestMethod.PUT)
      public String putUser(){
          return "PUT-张三";
      }
      
      // @RequestMapping(value = "/user",method = RequestMethod.DELETE)
      @DELETE
      public String deleteUser(){
          return "DELETE-张三";
      }
      ```

    - SpringBoot中手动开启，因为SpringBoot底层HiddenHttpMethodFilter是false的

      ```yml
      mvc:
        hiddenmethod:
          filter:
            enabled: true
      ```

- - 扩展：如何把_method 这个名字换成我们自己喜欢的。

- Rest原理（对表单来说的，因为表单只有POST和GET两种请求方式）
  - 表单提交会带上**_method=PUT**
  - **请求过来被**HiddenHttpMethodFilter拦截，
    - 首先会判定请求是否为POST请求
    - 获取到_Method的值
    - 兼容的请求有：PUT/DELETE/PATCH
    - **原生request（post），包装模式requesWrapper重写了getMethod方法，返回的是传入的值。**
    - **过滤器链放行的时候用wrapper。以后的方法调用getMethod是调用requestWrapper的。意思就是调用的是包装之后的request，因为修改了请求_method=PUT**

### 4.3.2 请求映射原理

![image-20210615091717404](C:\Users\siicfl\AppData\Roaming\Typora\typora-user-images\image-20210615091717404.png)

​		SpringMVC功能分析都从org.springframework.web.servlet.DispatcherServlet->doService（）->doDispatch（）开始

​		具体的执行流程如SpringMVC中1.5节所示

## 4.4 Web开发中的普通参数与基本注解

### 4.4.1 简单参数传递与获取



- **原理**：HandlerMapping找到能处理请求的Handler(对应具体Controller中的方法)，为当前的Handler找到合适的适配器HandlerAdapter(一般有下图几种，前2种居多，而第一种作为请求处理的会更多，第二种是支持函数式处理)。

  ​		然后调用如下方法执行目标方法，这个方法在执行目标方法执行前会进行参数解析（有26个参数解析器argumentResolvers，常见的有RequestParamMethodArgumentResolver,PathVariableMethodArgumentResolver）确定当前解析器是否支持解析这种参数（先从缓存获取，没有则26个解析器逐一遍历，同时满足的会放入缓存），支持则调用resolveArgument解析出具体的参数值。

  ​		目标方法执行完成后，将有是有的数据都放在ModelAndViewContainer，这里面包含要去的视图页面即view以及所有的数据，然后将其暴露出来放在请求域中。![image-20210615103023528](C:\Users\siicfl\AppData\Roaming\Typora\typora-user-images\image-20210615103023528.png)![image-20210615104656005](C:\Users\siicfl\AppData\Roaming\Typora\typora-user-images\image-20210615104656005.png)



- **@PathVariable**：路径变量
- **@RequestHeader**：获取请求头
- **@CookieValue**：获取cookie值
- **@RequestBody**：获取请求体Form Data的数据，也就是获取表单提交后的数据Map类型
- **@RequestParam**：获取?&表示的参数,如 /user/info?id=1&name=zhangsan&age=18&interests=basketball&interests=football
- **@ModelAttribute**：获取以“；”分隔的参数，如/user/info;id=1;name=zhangsan;age=18;intersts=basketball,football

```java
// 前端发送：car/1/owner/zuhwenchuan?interests=basketball&interests=football&age=18
// @PathVariable和@RequestHeader都是后端接收参数，分为单个参数接收和Map自动识别接收
@GetMapping("/car/{id}/owner/{username}")
public Map<String, Object> getCar(@PathVariable("id") Integer id,
                                  
                                  // rest风格的请求参数获取
								  @PathVariable("username") String name,
                                  
                                  // 接收所有参数,{"id":"1","username":"zhuwenchuan"}
								  @PathVariable Map<String,String> kv,
                                  
                                  // 获取请求头中的User-Agent信息
                                  @RequestHeader("User-Agent") String userAgent,
                                  
                                  // 接受所有请求头信息,输出{"Host":..., "User-Agent":...}
                                  @RequestHeader Map<String,String> headers,
                                  
                                  // ?参数&参数风格的参数获取，输出["basketball","football"]
                                  @RequestParam("interests") List<String> inters,
                                  
                                  // 接受所有参数，输出{"age":"18","interests":"basketball"}
                                  @RequestParam Map<String,String> params){
    							  
    							  // 重定向传递参数
    							  RdirectAttributes ra
    return null;
}
```

- **@RequestAttribute**：获取请求参数

```java
    @RequestMapping(value = "/user",method = RequestMethod.GET)
	//此处的方法发返回值不是一个json格式的，所以不要加@ResponseBody
    public String getUser(HttpServletRequest request){
        request.setAttribute("msg","成功");
        request.setAttribute("code",200);
        return "forward:/success";
    }

    @GetMapping("/success")
    @ResponseBody
    public String success(@RequestAttribute("msg") String msg, HttpServletRequest request){
        //两种方法获取值，使用@RequestAttribute和 request.getAttribute获取值
        request.getAttribute("code");
        return msg;
    }
```

- **@MatrixVariable**：利用矩阵变量进行参数传递

  **问题：**：如果Cookie被禁用如何获取Session中的内容

  **方法**：重写URL，利用矩阵变量的形式传递jsessionid即/user;jsessionid=xxx

  不用普通的@RequestParam的URL的方法是因为无法确定jsessionid是真正的url路径还是需要的参数；而@MatrixVariable形式的url会区分参数和真正的url

  **原理**：因为一个Session会有一个jsessionid，而jsessionid由cookie携带，每发一次请求都会携带cookie，当cookie被禁用时就不会携带cookie，就无法找到jsessionid了，这样就无法获取到session，因此需要重写URL传递jsessionid

  **注意**：

  ​		1.SpringBoot是默认禁用矩阵变量的，因为会自动忽略分号以后的内容，手动开启方式：
  
  ```java
  @Configuration
  public class WebConfig {
      //1、WebMvcConfigurer定制化SpringMVC的功能
      @Bean
      public WebMvcConfigurer webMvcConfigurer(){
          return new WebMvcConfigurer() {
              @Override
              public void configurePathMatch(PathMatchConfigurer configurer) {
                  UrlPathHelper urlPathHelper = new UrlPathHelper();
                  // 设定为false不移除分号后面的内容。矩阵变量功能就可以生效
                  urlPathHelper.setRemoveSemicolonContent(false);
                  configurer.setUrlPathHelper(urlPathHelper);
              }
          };
      }
  }
  ```

  ​		2.矩阵变量一定要放在路径变量中才能生效

  ​		3.语法格式：/boss/1;age=20/2;age=10，其中1;age=20和2;age=10作为PathVariable
  
  ```java
      // /boss/1;age=20/2;age=10
  
      @GetMapping("/boss/{bossId}/{empId}")
      public Map boss(@MatrixVariable(value = "age",pathVar = "bossId") Integer bossAge,
                      @MatrixVariable(value = "age",pathVar = "empId") Integer empAge){
          Map<String,Object> map = new HashMap<>();
  
          map.put("bossAge",bossAge); // 输出{bossAge,20}
          map.put("empAge",empAge); // 输出{empAge,10}
          return map;
  
      }
  }
  ```

### 4.4.2 复杂参数

- **Map**、**Model（map、model里面的数据会被放在request的请求域，等同于request.setAttribute）、**Errors/BindingResult、**RedirectAttributes（ 重定向携带数据）**、**ServletResponse（response）**、SessionStatus、UriComponentsBuilder、ServletUriComponentsBuilder
- **自定义对象参数绑定**：利用WebDataBinder里的Converters将请求数据String转成指定的JavaBean中的数据类型，再进行属性注入；此处可以扩展自定义转换规则

```java
@Configuration
public class WebConfig {
    //1、WebMvcConfigurer定制化SpringMVC的功能
    @Bean
    public WebMvcConfigurer webMvcConfigurer(){
        return new WebMvcConfigurer() {
			
            // 自定义convert
            @Override
            public void addFormatters(FormatterRegistry registry) {
                registry.addConverter(new Converter<String, Pet>() {

                    @Override
                    public Pet convert(String source) {
                        // 啊猫,3
                        if(!StringUtils.isEmpty(source)){
                            Pet pet = new Pet();
                            String[] split = source.split(",");
                            pet.setName(split[0]);
                            pet.setAge(Integer.parseInt(split[1]));
                            return pet;
                        }
                        return null;
                    }
                });
            }
        };
    }
}
```

## 4.5 内容协商（根据客户端接收能力不同，返回不同类型数据）

**实现多协议数据兼容。json（web场景就包括json依赖，所以是web项目就行）、xml（需要引入xml依赖）等类型**

postman分别测试返回json和xml，只需要改变请求头中Accept字段，http协议中规定的，该字段用于告诉服务器本客户端能够接收的数据类型

**0、**@ResponseBody 响应数据出去 调用 **RequestResponseBodyMethodProcessor** 处理

1、Processor 处理方法返回值。通过 **MessageConverter** 处理

2、所有 **MessageConverter** 合起来可以支持各种媒体类型数据的操作（读、写）

3、内容协商找到最终的 **messageConverter**；

SpringMVC的什么功能。一个入口给容器中添加一个  WebMvcConfigurer

```java
@Bean
public WebMvcConfigurer webMvcConfigurer(){
    return new WebMvcConfigurer() {

        @Override
        public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {

        }
    }
}
```

# 5.视图解析与模板引擎

## 5.1 Thymleaf的使用

```jsp
加入依赖
<html lang="en" xmlns:th="http://www.thymeleaf.org">
```

## 5.2 管理系统的搭建

### 5.2.1 前端路径构建

```html
th:action="@{login或/login}" 直接将/login拼接到工程路径即localhost:8080上

<div th:replace="common :: #commonscript"></div> 由选择器方式将common下的commonscript替换这个标签

访问外部页面需要return  "redirect:https://www.baidu.com/" 写全名
```

### 5.2.2 拦截器

见SpringMVC

### 5.2.3 文件上传

- 前端页面

  ```jsp
  <form method="post" action="/upload" enctype="multipart/form-data"> 多文件上传
      <input type="file" name="file"><br>
      <input type="submit" value="提交">
  </form>
  ```

- 文件上传后端代码

  ```java
  /**
   * MultipartFile 自动封装上传过来的文件
   * @param email
   * @param username
   * @param headerImg
   * @param photos
   * @return
   */
  @PostMapping("/upload")
  public String upload(@RequestParam("email") String email,
                       @RequestParam("username") String username,
                       @RequestPart("headerImg") MultipartFile headerImg,
                       @RequestPart("photos") MultipartFile[] photos) throws IOException {
  
      log.info("上传的信息：email={}，username={}，headerImg={}，photos={}",
              email,username,headerImg.getSize(),photos.length);
  
      if(!headerImg.isEmpty()){
          //保存到文件服务器，OSS服务器
          String originalFilename = headerImg.getOriginalFilename();
          headerImg.transferTo(new File("H:\\cache\\"+originalFilename));
      }
  
      if(photos.length > 0){
          for (MultipartFile photo : photos) {
              if(!photo.isEmpty()){
                  String originalFilename = photo.getOriginalFilename();
                  photo.transferTo(new File("H:\\cache\\"+originalFilename));
              }
          }
      }
          return "main";
  }
  ```

- 修改文件上传大小限制

  ```yml
  spring:
    servlet:
      multipart: 
        max-file-size: 10MB  最大单个文件上传大小，默认1MB
        max-request-size: 100MB  最大单次请求文件大小
  ```

### 5.2.4 异常处理

- SpringBoot默认的异常处理机制

  发生异常时SpringBoot会自动跳转到static/error或templates/error下对应错误码的页面，没有设定页面时底层有一个写死了的StaticView白页信息。

```jsp
<h1 th:text="${message}">显示异常信息</h1>
<h2 th:text="${trace}">显示错误追踪堆栈信息</h2>
```

- 自定义异常的处理规则

  默认的异常处理的功能主要是由DefaultErrorViewResolver来完成的，他会得到页面的状态码，通过此状态码到error文件夹中找错误页码找到就用，没找到就用自己的

- 自定义异常的处理方法

  - @controllerAdvice+@ExceptionHandler处理异常（此方法最常用）：底层由ExceptionHandlerExceptionResolver支持，主要的步骤为：

  ​	（1）创建一个普通类，作为全局异常处理类，在类上加上注解@ControllerAdvice

  ​	（2）定义一个异常处理方法，加上注解@ExceptionHandler

  ​		当Controller中发生异常时，会去带@ControllerAdvice注解异常处理类中通过@ExceptionHandler的value值找对应的异常处理方法，最后将数据展示到友好的页面上。

  ```java
  /**
   * @ControllerAdvice ：控制器增强（给控制器类增强功能），需要在SpringMVC配置文件声明组件扫描器，指定@ControllerAdvice的包名
   */
  @ControllerAdvice
  public class GlobalExceptionHandler {
      /**
       * 定义方法，处理发生的异常：只可以用ModelAndView
       *      方法的定义：处理异常的方法和控制器方法的定义一致
       *      形参：表示控制器抛出的异常对象
       *
       * @ExceptionHandler ：表示发生指定的异常时，由此方法执行
       *
       * 为什么处理异常？
       *      1.将异常记录到数据库或日志文件
       *      2.发送通知，将异常告知开发人员
       *      3.给用户友好的提示界面
       */
      @ExceptionHandler(value = NameException.class)
      public ModelAndView nameException(Exception e) {
          ModelAndView mv = new ModelAndView();
  
          mv.addObject("msg","姓名发生错误啦");
          mv.addObject("e",e);
          // 友好的提示界面
          mv.setViewName("nameError");
          return mv;
      }
  
      // 处理除了指定类型异常的其他未知异常
      @ExceptionHandler
      public ModelAndView doOtherException(Exception e) {
          ModelAndView mv = new ModelAndView();
  
          mv.addObject("msg","未知异常发生了");
          mv.addObject("e",e);
          System.out.println(e.getMessage());
          // 友好的提示界面
          mv.setViewName("defaultError");
          return mv;
      }
  }
  ```

  - @ResponseStatus注解定义自定义异常

    ```java
    @ResponseStatus(value = HttpStatus.FORBIDDEN,reason = "无法控制")
    public class OutOfControlException extends RuntimeException{
        public OutOfControlException() {
        }
    
        public OutOfControlException(String message) {
            super(message);
        }
    }
    ```

- 自定义异常处理器

  创建一个普通类实现HandlerExceptionResolver接口

  ```java
  public class CustomerHandlerExceptionResolver implements HandlerExceptionResolver {
  
      @Override
      public ModelAndView resolveException(HttpServletRequest request, 
                                           HttpServletResponse response, 
                                           Object handler, Exception ex) {
          try {
              response.sendError(511,"我喜欢的异常");
          } catch (IOException e) {
              e.printStackTrace();
          }
          return new ModelAndView();
      }
  }
  ```

# 6、Web原生组件注入(Servlet/Filter/Listener)

## 6.1 使用ServletAPI

- **@ServletComponentScan(basePackages = "com.atguigu.admin") **:指定原生Servlet组件都放在哪里

  位置：定义在**@SpringBootApplication**方法上

- **@WebServlet(urlPatterns = "/my")**：效果：直接响应，**没有经过Spring的拦截器？**

  位置：定义的Servlet普通类上

- **@WebFilter(urlPatterns={"/css/\*","/images/\*"})**

  位置：定义的过滤器普通类上

- **@WebListener**

  位置：定义的listener普通类上

## 6.2 使用RegistrationBean

```java
@Configuration
public class MyRegistConfig {

    @Bean
    public ServletRegistrationBean myServlet(){
        MyServlet myServlet = new MyServlet();

        return new ServletRegistrationBean(myServlet,"/my","/my02");
    }


    @Bean
    public FilterRegistrationBean myFilter(){

        MyFilter myFilter = new MyFilter();
		// return new FilterRegistrationBean(myFilter,myServlet());
        FilterRegistrationBean filterRegistrationBean = 
            								new FilterRegistrationBean(myFilter);
        filterRegistrationBean.setUrlPatterns(Arrays.asList("/my","/css/*"));//过滤多个请求
        return filterRegistrationBean;
    }

    @Bean
    public ServletListenerRegistrationBean myListener(){
        MySwervletContextListener mySwervletContextListener = 
            								new MySwervletContextListener();
        return new ServletListenerRegistrationBean(mySwervletContextListener);
    }
}
```

## 6.3 为什么自定义的Servlet不会被拦截器拦截

因为Spring底层有一个精确优选原则即多个Servlet都能处理同一路径时会选择匹配度高的；DispatcherServlet的映射路径为"/"，而自定义的路径为"/**"，根据精确优选匹配的原则就会先去找到自定义的Servlet

注意：当自定义的Servlet映射路径也为"/"时会去先调用Dispatcher，因为优先级Dispatcher高

![image.png](assets/image-1614864537352.png)

# 7.定制化原理

## 7.1 可供定制化的几个方面

- 如果需要自定义converters（addFormatters）自定义接收参数规则，拦截器（addInterceptors），定义静态资源行为（addResourceHandlers这个是默认由WebMvcAutoConfiguration配置好的，不需要用户定义）等都可以让配置类实现WebMvcConfiguration接口
- 如果想定义RequestMappingHandlerMapping（处理器映射器）和RequestMappingHandlerAdaptor（处理器适配器）相关的就可以在配置类中声明@Bean的WebMvcRegistrations方法
- 如果想完全控制SpringMVC利用@EnableWebMvc定义配置类（不要用）

## 7.2 实现功能定制和扩展原理

- 原理

- - 1、WebMvcAutoConfiguration  默认的SpringMVC的自动配置功能类。静态资源、欢迎页.....
  - 2、一旦使用 @EnableWebMvc 、。会 @Import(DelegatingWebMvcConfiguration.**class**)
  - 3、**DelegatingWebMvcConfiguration** 的 作用，只保证SpringMVC最基本的使用

- - - 把所有系统中的 WebMvcConfigurer 拿过来。所有功能的定制都是这些 WebMvcConfigurer  合起来一起生效（这也是整合其他功能如Kafka的原理）
    - 自动配置了一些非常底层的组件。**RequestMappingHandlerMapping**、这些组件依赖的组件都是从容器中获取
    - **public class** DelegatingWebMvcConfiguration **extends** **WebMvcConfigurationSupport**

- - 4、**WebMvcAutoConfiguration** 里面的配置要能生效 必须  @ConditionalOnMissingBean(**WebMvcConfigurationSupport**.**class**)
  - 5、@EnableWebMvc  导致了 **WebMvcAutoConfiguration  没有生效。**

- 整合其他组件原理分析

  **场景starter(如mybatis-starter)** **- xxxxAutoConfiguration - 导入xxx组件 - 绑定xxxProperties --** **绑定配置文件项** 

# 8.SpringBoot访问数据库

## 8.1 整合JDBC

1. 导入JDBC场景（starter）

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-jdbc</artifactId>
   </dependency>
   ```

2. 导入数据库驱动（自带版本仲裁）

   ```xml
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
   </dependency>
   ```

3. 自动配置类

   - DataSourceAutoConfiguration ： 数据源的自动配置

   - - 修改数据源相关的配置：**spring.datasource**
     - **数据库连接池的配置，是自己容器中没有DataSource才自动配置的(@Conditional)**
     - 底层配置好的连接池是：**HikariDataSource**

   ```java
       @Configuration(proxyBeanMethods = false)
       @Conditional(PooledDataSourceCondition.class)
       @ConditionalOnMissingBean({ DataSource.class, XADataSource.class })
       @Import({ DataSourceConfiguration.Hikari.class, DataSourceConfiguration.Tomcat.class,
               DataSourceConfiguration.Dbcp2.class, DataSourceConfiguration.OracleUcp.class,
               DataSourceConfiguration.Generic.class, DataSourceJmxConfiguration.class })
       protected static class PooledDataSourceConfiguration
   ```

   - DataSourceTransactionManagerAutoConfiguration： 事务管理器的自动配置
   - JdbcTemplateAutoConfiguration： **JdbcTemplate的自动配置，可以来对数据库进行crud**

   - - 可以修改这个配置项@ConfigurationProperties(prefix = **"spring.jdbc"**) 来修改JdbcTemplate
     - @Bean@Primary    JdbcTemplate；容器中有这个组件

   - JndiDataSourceAutoConfiguration： jndi的自动配置
   - XADataSourceAutoConfiguration： 分布式事务相关的

4. 修改配置项(url/username/password)

   ```yaml
   spring:
     datasource:
       url: jdbc:mysql://localhost:3306/db_account
       username: root
       password: 123456
       driver-class-name: com.mysql.jdbc.Driver
   ```

## 8.2 整合Druid数据库连接池

### 8.2.1 导入Druid依赖方式

参考网址：https://github.com/alibaba/druid

- 配置Druid连接池

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.17</version>
</dependency>

<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
    destroy-method="close">
    <property name="url" value="${jdbc.url}" />
    <property name="username" value="${jdbc.username}" />
    <property name="password" value="${jdbc.password}" />
    <property name="maxActive" value="20" />
    <property name="initialSize" value="1" />
    <property name="maxWait" value="60000" />
    <property name="minIdle" value="1" />
    <property name="timeBetweenEvictionRunsMillis" value="60000" />
    <property name="minEvictableIdleTimeMillis" value="300000" />
    <property name="testWhileIdle" value="true" />
    <property name="testOnBorrow" value="false" />
    <property name="testOnReturn" value="false" />
    <property name="poolPreparedStatements" value="true" />
    <property name="maxOpenPreparedStatements" value="20" />
</bean>
```

```java
@Configuration
public class MainConfig {
    @Bean
    @ConfigurationProperties(prefix = "spring.datasource")
    public DataSource dataSource(){
        return new DruidDataSource();
    }
}
```

- 配置StatViewServlet

> StatViewServlet的用途包括：
>
> - 提供监控操作数据库的信息展示的html页面
> - 提供监控信息的JSON API

```xml
<!--Web.xml中配置-->
<servlet>
    <servlet-name>DruidStatView</servlet-name>
    <servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>DruidStatView</servlet-name>
    <url-pattern>/druid/*</url-pattern>
</servlet-mapping>
```

```java
@Configuration
public class MainConfig {
    /**
     * 配置Druid监控页功能
     */
    @Bean
    @ConfigurationProperties(prefix = "spring.statviewservlet")
    public ServletRegistrationBean statViewServlet(){
        StatViewServlet statViewServlet = new StatViewServlet();
        ServletRegistrationBean servletRegistrationBean = 
            					new ServletRegistrationBean(statViewServlet, "/druid/*");
        // 用于配置初始化参数
        servletRegistrationBean.setInitParameters();
        return servletRegistrationBean;
    }
}
```

- 配置防火墙
- 配置防御SQL注入
- 配置Session监控
- 配置Spring监控

### 8.2.1 自动配置Druid

- 引入Druid-starter

  ```xml
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid-spring-boot-starter</artifactId>
      <version>1.1.17</version>
  </dependency>
  ```

- 分析自动配置类DruidDataSourceAutoConfigure.class

  ```java
  @Configuration
  @ConditionalOnClass({DruidDataSource.class})
  @AutoConfigureBefore({DataSourceAutoConfiguration.class})
  @EnableConfigurationProperties({DruidStatProperties.class, 
                                  						DataSourceProperties.class})
  @Import({DruidSpringAopConfiguration.class, DruidStatViewServletConfiguration.class, DruidWebStatFilterConfiguration.class, DruidFilterConfiguration.class})
  public class DruidDataSourceAutoConfigure {
      private static final Logger LOGGER = 
          				LoggerFactory.getLogger(DruidDataSourceAutoConfigure.class);
  
      public DruidDataSourceAutoConfigure() {
      }
  
      @Bean(initMethod = "init")
      @ConditionalOnMissingBean
      public DataSource dataSource() {
          LOGGER.info("Init DruidDataSource");
          return new DruidDataSourceWrapper();
      }
  }
  ```

  - 自动配置类绑定了DataSourceProperties，扩展配置项 **spring.datasource.druid**
  - DruidSpringAopConfiguration.**class**,   监控SpringBean的；配置项：**spring.datasource.druid.aop-patterns**
  - DruidStatViewServletConfiguration.**class**, 监控页的配置：**spring.datasource.druid.stat-view-servlet；默认开启**
  - DruidWebStatFilterConfiguration.**class**, web监控配置；**spring.datasource.druid.web-stat-filter；默认开启**
  - DruidFilterConfiguration.**class**}) 所有Druid自己filter的配置

- 修改配置项

  具体参照：https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter

  ```yml
  spring:
    servlet:
      multipart:
        max-file-size: 10MB
        max-request-size: 100MB
  
    datasource:
      url: jdbc:mysql://118.31.2.239:3306/test?useSSL=false&serverTimezone=UTC&characterEncoding=utf-8
      username: root
      password: root
      driver-class-name: com.mysql.cj.jdbc.Driver
  
      druid:
        aop-patterns: com.atguigu.admin.*  #监控SpringBean
        filters: stat,wall     # 底层开启功能，stat（sql监控），wall（防火墙）
  
        stat-view-servlet:   # 配置监控页功能
          enabled: true
          login-username: admin
          login-password: admin
          resetEnable: false
  
        web-stat-filter:  # 监控web
          enabled: true  # 开启
          urlPattern: /* # 拦截所有路径下请求
          exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*' # 排除
  
  
        filter:
          stat:    # 对上面filters里面的stat的详细配置
            slow-sql-millis: 1000
            logSlowSql: true
            enabled: true
          wall:
            enabled: true
            config:
              drop-table-allow: false
  ```

## 8.3 整合Mybatis

1. 利用全局配置文件整合Mybatis

   SqlSessionFactory: 自动配置好了

   SqlSession：自动配置了 **SqlSessionTemplate 组合了SqlSession**

   @Import(**AutoConfiguredMapperScannerRegistrar**.**class**）；

   @Mapper： 只要我们写的操作MyBatis的接口标注了 **@Mapper 就会被自动扫描进来**

- 引入starter

  ```xml
  <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>2.1.4</version>
  </dependency>
  ```

- 编写mybatis主配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!--mybatis的主配置文件-->
  
  <!--约束文件-->
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
  <configuration>
      <!--settings：控制mybatis全局行为-->
      <settings>
          <!--加入日志配置，可以在控制台输出执行的 sql语句和参数-->
          <setting name="logImpl" value="STDOUT_LOGGING"/>
          <!--开启全局缓存，默认是开启的-->
          <setting name="cacheEnabled" value="true"/>
          <!--开启驼峰命名规则-->
          <setting name="mapUnderscoreToCamelCase" value="true"/>
      </settings>
  
      <!--定义类的别名-->
      <typeAliases>
          <!--修改-->
          <package name="com.zhuwenchuan.admin.bean"/>
      </typeAliases>
  
  </configuration>
  ```

- 编写Mapper接口并标注@Mapper注解（推荐的方法），也可以在运行类上配置@MapperScan("com.zhuwenchuan.admin.mapper")

  ```java
  @Mapper
  public interface UserMapper {
      User selectUserByName(String name);
  
      User selectUserByNameAndPassword(@Param("name")String name, @Param("password")String password);
  
      int insertUser(User user);
  }
  
  @MapperScan("com.zhuwenchuan.admin.mapper")
  @SpringBootApplication
  public class Springboot03Application {
  
      public static void main(String[] args) {
          SpringApplication.run(Springboot03Application.class, args);
      }
  
  }
  ```

- 编写实体类

- 编写mapper映射文件

- 修改配置项

  ```yml
  mybatis:
    config-location: classpath:mybatis/mybatis-config.xml
    mapper-locations: classpath:mybatis/mapper/*.xml 
  ```

2.利用yaml配置文件整合mybatis（推荐）

```yml
mybatis:
  # config-location: classpath:mybatis/mybatis-config.xml 扫描全局配置文件
  mapper-locations: classpath:mybatis/mapper/*.xml # 扫描映射文件

  configuration: # 代替全局配置文件中的配置
    map-underscore-to-camel-case: true # 开启驼峰命名规则
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl # 开启日志
    cache-enabled: true # 开启mybatis一级缓存

  type-aliases-package: com.zhuwenchuan.admin.bean # 定义类的别名
```

3.纯注解（不推荐）

```java
@Select("select `id`,`username`,`password`,`email` from t_user where username = #{name}")
User selectUserByName(String name);

@Select("select `id`,`username`,`password`,`email` from t_user 
        							where username = #{name} and password = #{password}")
User selectUserByNameAndPassword(@Param("name")String name, @Param("password")String password);

@Insert("insert into t_user (`username`,`password`,`email`) 
        										values (#{name},#{password},#{email})")
@Options(useGeneratedKeys = true,keyProperty = "id")
int insertUser(User user);
```

## 8.4 整合Mybatis-PLUS

### 8.4.1 CRUD操作

- 引入starter

  ```xml
  <dependency>
      <groupId>com.baomidou</groupId>
      <artifactId>mybatis-plus-boot-starter</artifactId>
      <version>3.4.1</version>
  </dependency>
  ```

- 自动配置

  - MybatisPlusAutoConfiguration 配置类，MybatisPlusProperties 配置项绑定。**mybatis-plus：xxx 就是对****mybatis-plus的定制**
  - **SqlSessionFactory 自动配置好。底层是容器中没有配置数据源时为默认的数据源HikariDataSource**
  - **mapperLocations 自动配置好的。有默认值。classpath\*:/mapper/\**/\*.xml；任意包的类路径下的所有mapper文件夹下任意路径下的所有xml都是sql映射文件。  建议以后sql映射文件，放在 mapper下**
  - **容器中也自动配置好了** **SqlSessionTemplate**
  - **@Mapper 标注的接口也会被自动扫描；建议直接** @MapperScan(**"com.atguigu.admin.mapper"**) 批量扫描就行

- Mapper接口继承BaseMapper就可以继承BaseMapper中的方法并且可以直接用

  ```
  @Mapper
  // 加上@Repository可以在引用自动注入属性时避免出现红波浪线
  public interface UserMapper extends BaseMapper<User> {
  	
  }
  ```

- 在配置类上加上@MapperScan("com.zhuwenchuan.Mapper")注解，指明扫描的包

- 创建实体类，需要设置表名，字段与数据库不同的属性

  ```java
  @Data
  @NoArgsConstructor
  @AllArgsConstructor
  @TableName("t_user")
  public class User {
      @TableId(type = IdType.AUTO)  // 可以更改mp自带的主键生成策略
      private Integer id;
      @TableField("username")
      private String name;
      private String password;
      private String email;
  
      @TableField(exist = false) // 表示数据库中不存在的数据
      private String wife;
  
  }
  ```

- 创建Service接口，继承IService，ServiceImpl继承ServiceImpl类，其中封装了很多sql方法

  ```java
  public interface UserService extends IService<User> {
  	
  }
  ```

  ```java
  @Service
  public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements 
      																	UserService{
      
  }
  ```
```
  
  ```java
  @Controller
  public class IndexController {
  
      @Autowired
      private UserService userService;
  
      @GetMapping("/insert")
      @ResponseBody
      public User insertUser(){
          User user = new User(null,"caisongsong","123","caisongsong@163.com","wife");
          // 修改或直接添加
          userService.saveOrUpdate(user);
          return user;
          
          // 单个id查询
          User User = userService.selectById(1L);
          
          // 多个id批量查询
          List<User> Users = userService.selectBatchIds(Arrays.asList(1L,2L,3L));
      }
  }
```

### 8.4.2 分页

- 在配置类中向IOC容器注入MybatisPlusInterceptor分页拦截器

  ```java
  @Configuration
  public class MybatisConfig {
      @Bean
      public MybatisPlusInterceptor mybatisPlusInterceptor() {
          MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
          interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.H2));
          return interceptor;
      }
  }
  ```

- 书写处理器方法

  ```java
  // userService中的方法实际上都是Mybatis-Plus中的方法
  @Controller
  public class IndexController {
  
      @Autowired
      private UserService userService;
  
      @GetMapping("/select")
      public void selectUsers(Model model, 
                              // 当前页数
                             @RequestParam(value = "pn", defaultValue = "1") Integer pn,
                             // 重定向携带参数
                              RdirectAttributes ra){
          
          Page<User> userPage = new Page<>(pn,4);
          Page<User> page = userService.page(userPage);
          // 从数据库中查询来的数据放在了page.records中，前端需要从这里取出数据
          List<User> records = page.getRecords();
          model.addAttribute("page",page);
          ra.addAttribute("page",page);
          
          // 通过分页对象获取分页数据
          Page<User> page = new Page<>(1,5);
          userMapper.selectPage(page, null);
          page.getRecords().forEach(System.out::println); // 每页数据的List集合
          
          System.out.println(page.getCurrent()); // 当前页
          System.out.println(page.getPages()); // 总页数
          System.out.println(page.getSize()); // 每页显示数量
          System.out.println(page.getTotal()); // 总记录数
          System.out.println(page.hasNext()); // 是否有下一页
          System.out.println(page.hasPrevious()); // 是否有上一页
      }
  }
  ```





### 8.4.3 主键生成规则

- Mybatis-Plus默认策略，采用snowflake雪花算法
- 自增策略：分库分表时需要知道上一个表的最后一个id，不方便
- redis生成策略
- UUID策略：每次生成不同的值作为主键，排序不方便

### 8.4.4 自动填充

- 在需要自动填充的属性上加上@TableField(fill = FieldFill.INSERT)注解，这个功能也可以通过数据库实现，加上Default值，两个选一个不能同时选择。

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@TableName("t_user")
public class User {
    @TableId(type = IdType.AUTO)  // 可以更改mp自带的主键生成策略
    private Integer id;
    @TableField("username")
    private String name;
    private String password;
    private String email;
    
    @TableField(fill = FieldFill.INSERT) // 在添加操作时会自动填充
    private Date create_time;  //使用mp的方法实现数据的添加
	
    @@TableField(fill = FieldFill.INSERT_UPDATE) // 在修改和添加操作时会自动填充
    private Date updateTime;
    
    @TableField(exist = false) // 表示数据库中不存在的数据
    private String wife;

}
```

- 创建一个Handler普通类实现现元对象处理器接口

  ```java
  package com.atguigu.mybatisplus.handler;
  import com.baomidou.mybatisplus.core.handlers.MetaObjectHandler;
  import org.apache.ibatis.reflection.MetaObject;
  import org.slf4j.Logger;
  import org.slf4j.LoggerFactory;
  import org.springframework.stereotype.Component;
  import java.util.Date;
  
  @Component
  public class MyMetaObjectHandler implements MetaObjectHandler {
      private static final Logger LOGGER =
                                 LoggerFactory.getLogger(MyMetaObjectHandler.class);
  	
      // mp使用insert方法就会触发这个方法
      @Override
      public void insertFill(MetaObject metaObject) {
          LOGGER.info("start insert fill ....");
          // 需要判断这个字段是否为空，否则会mp会覆盖你自己设置的值
          this.setFieldValByName("createTime", new Date(), metaObject);
          this.setFieldValByName("updateTime", new Date(), metaObject);
       }
      
      
  	@Override
  	public void updateFill(MetaObject metaObject) {
          LOGGER.info("start update fill ....");
          this.setFieldValByName("updateTime", new Date(), metaObject);
       }
  }
  ```

### 8.4.5 乐观锁和悲观锁

- 乐观锁

  - 为了解决丢失更新问题（写问题）：丢失更新就是当两个人或多个人同时操作一个数据时，当一个人先提交了事务另一个人后提交了事务，那么先提交事务的那个人的结果就会被后提交的人所覆盖，导致丢失更新。
  - 实现方式：增加一个版本号，数据附属一个版本号，每个需要对该数据进行操作的人提交前都需要比对版本号，当某个人更改数据成功后版本号也会随之改变，那么之后想要将修改该数据的人会因为版本号不对应而无法操作数据。

- 悲观锁

  当一个人在操作时，其他人都不可以进行操作

- Mybatis-Plus实现乐观锁

  - 增加一个version字段

    ```
    ALTER TABLE `user` ADD COLUMN `version` INT
    ```

  - 实体类添加version字段，并且加上@Version

  - 在配置类中添加乐观锁插件

    ```java
    @EnableTransactionManagement
    @Configuration
    @MapperScan("com.atguigu.mybatis_plus.mapper")
    public class MybatisPlusConfig {
        /**
        * 乐观锁插件
        */
        @Bean
        public OptimisticLockerInterceptor optimisticLockerInterceptor() {
            return new OptimisticLockerInterceptor();
        }
    }
    ```

- 可以加上一个自动填充，给version一个默认值

- 测试乐观锁，每次修改版本号都会加1

### 8.4.6 逻辑删除和物理删除

- 物理删除：真实的删除，将对应数据从数据库中删除，之后查询不到该数据

  ```java
  int result = userMapper.deleteById(1);
  int result = userMapper.deleteBatchIds(Arrays.asList(1,2,3));
  ```

- 逻辑删除：假性删除，将对应数据中代表是否被删除字段状态修改为被删除状态，即修改删除标志字段数据，在数据库中任然能看到这条数据

  - 在数据库中添加deleted字段作为标志位字段

    ```
    ALTER TABLE `user` ADD COLUMN `deleted` boolean
    ```

  - 在实体类上加上@TableLogic注解

    ```
    @TableLogic
    @TableField(fill = FieldFill.INSERT)
    private Integer deleted;
    ```

  - 在application.properties中加入配置，配置逻辑删除的值，默认就是删除标1，未删除标0

    ```properties
    mybatis-plus.global-config.db-config.logic-delete-value=1
    mybatis-plus.global-config.db-config.logic-not-delete-value=0
    ```

  - 在配置类中加入Bean

    ```java
    @Bean
    public ISqlInjector sqlInjector() {
        return new LogicSqlInjector();
    }
    ```

  - 进行逻辑删除操作

    ```java
    // 其实底层执行的是改操作，将deleted标志位从0改为1
    int result = userMapper.deleteById(1);
    // 一旦开启了deleted标志位，下次进行查操作，只会查询出未删除的了
    select * from user where deleted=0;
    ```

### 8.4.7 性能分析插件

用于性能分析的拦截器，用于输出每条sql语句机器执行时间，超过指定时间停止运行，有助于发现问题。最好在开发环境中使用，线上环境不要使用。

- 在配置类中加入性能分析的插件

  ```java
  /**
  * SQL 执行性能分析插件
  * 开发环境使用，线上不推荐。 maxTime 指的是 sql 最大执行时长
  */
  @Bean
  @Profile({"dev","test"})// 设置 dev test 环境在配置文档中开启spring.profiles.active=dev
  public PerformanceInterceptor performanceInterceptor() {
      PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
      performanceInterceptor.setMaxTime(100);//ms，超过此处设置的ms则sql不执行
      performanceInterceptor.setFormat(true);
      return performanceInterceptor;
  }
  ```

### 8.4.8 复杂条件查询（条件构造器）

- Mybatis-Plus中的条件构造类的继承关系图

  ![1616153278653](assets/1616153278653.png)

  - Wapper：顶级父类，条件构造抽象类
  - AbstractWrapper ： 用于查询条件封装，生成 sql 的 where 条件
  - **QueryWrapper（查询条件用这个类）** ： 构造条件类，Entity 对象封装操作类，不是用lambda语法
  - UpdateWrapper ： Update 条件封装，用于Entity对象更新操作

- QueryWrapper

  - ge（大于等于）、gt、le、lt、isNull、isNotNull、eq（等于）、nq（不等于）

    ```java
    @Test
    public void testDelete() {
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper
         .isNull("name")  // 
         .ge("age", 12)  // age大于等于12
         .isNotNull("email"); // email不为空
        int result = userMapper.delete(queryWrapper);
        System.out.println("delete return count = " + result);
    }
    
    SQL：UPDATE user SET deleted=1 WHERE deleted=0 AND name IS NULL AND age >= ? AND email IS NOT NULL
    ```

  - allEq：传入一个map，查询map键值对的条件全部符合的数据

    ```java
    @Test
    public void testSelectList() {
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        Map<String, Object> map = new HashMap<>();
        map.put("id", 2);
        map.put("name", "Jack");
        map.put("age", 20);
        queryWrapper.allEq(map);
        List<User> users = userMapper.selectList(queryWrapper);
        users.forEach(System.out::println);
    }
    
    SELECT id,name,age,email,create_time,update_time,deleted,version FROM user WHERE deleted=0 AND name = ? AND id = ? AND age = ?
    ```

  - between：查询满足...到...的数据

    ```java
    QueryWrapper<User> wrapper = new QueryWeapper<>();
    wrapper.between("age",20,50); // 查询where age between 20 and 50 
    List<User> users = userMapper.selectList(wrapper); 
    ```

  - like、likeLeft、likeRight：模糊查询

    ```java
    wrapper.like("name","zhu");//传入数据%zhu%,select * from user where name like %zhu%
    wrapper.likeLeft("name","zhu"); // 传入数据，%name
    List<User> users = userMapper.selectList(wrapper); 
    ```

  - orderBy，orderByDesc，orderByAsc：排序

    ```java
    wrapper.orderBy("id");
    ```

  - last：拼接SQL语句

    ```java
    wrapper.last("limit 1");
    ```

  - select：指定要查询的列

    ```java
    wrapper.select("name","age");
    ```

# 9.SpringBoot整合Redis

- 引入Redis-starter

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
  </dependency>
  ```

- 自动配置

  - RedisAutoConfiguration 自动配置类。RedisProperties 属性类 --> **spring.redis.xxx是对redis的配置**
  - 连接工厂是准备好的。**Lettuce**ConnectionConfiguration、**Jedis**ConnectionConfiguration（采用的是直连，多个线程操作是不安全的，若想避免不安全可以使用jedis pool连接池）
  - **自动注入了RedisTemplate**<**Object**, **Object**> ： xxxTemplate都是用来实际操作xxx的；
  - **自动注入了StringRedisTemplate；k：v都是String**
  - **底层只要我们使用 StringRedisTemplate、RedisTemplate就可以操作redis**

  ```java
  @Configuration(proxyBeanMethods = false)
  @ConditionalOnClass(RedisOperations.class)
  // 配置类
  @EnableConfigurationProperties(RedisProperties.class)
  @Import({ LettuceConnectionConfiguration.class, JedisConnectionConfiguration.class })
  public class RedisAutoConfiguration {
  	
      // 供操作的两个对象
  	@Bean
  	@ConditionalOnMissingBean(name = "redisTemplate")
  	@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
  	public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
  		RedisTemplate<Object, Object> template = new RedisTemplate<>();
  		template.setConnectionFactory(redisConnectionFactory);
  		return template;
  	}
  
  	@Bean
  	@ConditionalOnMissingBean
  	@ConditionalOnSingleCandidate(RedisConnectionFactory.class)
  	public StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
  		StringRedisTemplate template = new StringRedisTemplate();
  		template.setConnectionFactory(redisConnectionFactory);
  		return template;
  	}
  }
  ```
  
- 改写配置文档

  ```yml
  spring:  
    redis:
      host: 192.168.31.128
  ```
  
- 执行Redis(RedisTemplate与Lettuce)

  ```java
  @Autowired
  private RedisTemplate redisTemplate;
  
  @Test
  void testRedis(){
      // 操作字符串的
      ValueOperations<String, String> operations = redisTemplate.opsForValue();
      // 操作hash
      HashOperations hashOperations = redisTemplate.opsForHash();
  	
      // 直接存入会有乱码，需要对key字符串进行序列化，编写自己的redisTemplate
      operations.set("hello","world");
      // 传递对象需要用Json，底层采用的是jdk自带的序列化方式，需要对对象进行序列化，可以自己编写redisTemplate直接替换为json序列化方式
      operations.set("hello",new User);
  
      String hello = operations.get("hello");
      System.out.println(hello);
  }
  ```
  
- 改写RedisTemplate类，加上json序列化，防止key乱码，以及用json格式穿对象

  ```java
  @Configuration
  public class RedisConfig {
  
      // 编写自己的redisTemplate覆盖SpringBoot中自带的
      @Bean
      public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory redisConnectionFactory) {
          RedisTemplate<String, Object> template = new RedisTemplate<>();
          template.setConnectionFactory(redisConnectionFactory);
  
          // 序列化配置
          Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
          ObjectMapper objectMapper = new ObjectMapper();
          objectMapper.activateDefaultTyping(LaissezFaireSubTypeValidator.instance,
                  ObjectMapper.DefaultTyping.NON_FINAL, JsonTypeInfo.As.PROPERTY);
          jackson2JsonRedisSerializer.setObjectMapper(objectMapper);
  
          // String的序列化
          StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
          // key采用String的序列化方式
          template.setKeySerializer(stringRedisSerializer);
          // hash的key也采用String的序列化方式
          template.setHashKeySerializer(stringRedisSerializer);
          // value的序列化方式采用jackson
          template.setValueSerializer(jackson2JsonRedisSerializer);
          // hash的value系列化采用jackson
          template.setHashValueSerializer(jackson2JsonRedisSerializer);
  
          template.afterPropertiesSet();
  
          return template;
  
      }
  }
  ```

  ```java
  @Autowired
  private RedisTemplate redisTemplate;
  
  @Test
  void contextLoads() throws JsonProcessingException {
      // 自主配置了RedisTemplate之后的操作，不会再乱码了
      ValueOperations<String, Object> operations = redisTemplate.opsForValue();
  
      User user = new User(1, "zhuwenchuan", "123456", "88", "jj");
      operations.set("user",user);
      
      System.out.println(operations.get("user"));
  }
  ```

- 编写RedisUtil

- redisTemplate常用指令

  - 设置生存时间

  ```java
  存入redis的同时设置key的生存时间，timeout为生存时间，timeunit为时间单位
  redisTemplate.redisTemplate.opsForValue().set(key, value, timeout, timeUnit);
  
  设置redis数据库中键的生命时间,TimeUnit是java.util下的类
  redisTemplate.expire(Object key, long timeout, TimeUnit timeUnit);
  ```

- Jedis和Lettuce

  - Lettuce：SpingBoot2.x以后采用的版本，Lettuce的连接是基于Netty的，Netty 是一个多线程、事件驱动的 I/O 框架。连接实例可以在多个线程间共享，当多线程使用同一连接实例时，是线程安全的。
  - Jedis：Jedis在实现上是直接连接的Redis Server，如果在多线程环境下是非线程安全的。每个线程都去拿自己的 Jedis 实例，当连接数量增多时，资源消耗阶梯式增大，连接成本就较高了。

# 10.JUnit5

## 10.1 JUnit5常用注解

JUnit5的注解与JUnit4的注解有所变化

<https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations>

- **@Test :**表示方法是测试方法。但是与JUnit4的@Test不同，他的职责非常单一不能声明任何属性，拓展的测试将会由Jupiter提供额外测试
- **@ParameterizedTest :**表示方法是参数化测试，下方会有详细介绍
- **@RepeatedTest :**表示方法可重复执行，下方会有详细介绍
- **@DisplayName :**为测试类或者测试方法设置展示名称
- **@BeforeEach :**表示在每个单元测试之前执行
- **@AfterEach :**表示在每个单元测试之后执行
- **@BeforeAll :**表示在所有单元测试之前执行
- **@AfterAll :**表示在所有单元测试之后执行
- **@Tag :**表示单元测试类别，类似于JUnit4中的@Categories
- **@Disabled :**表示测试类或测试方法不执行，类似于JUnit4中的@Ignore
- **@Timeout :**表示测试方法运行如果超过了指定时间将会返回错误
- **@ExtendWith :**为测试类或测试方法提供扩展类引用

```java
@SpringBootTest // 使用SpringBoot环境进行测试
@DisplayName("JUnit5类测试")
class UserServiceImplTest {

/*    @Autowired
    UserService userService;*/

    // 规定超时时间单位为ms，超时抛异常
    @Timeout(value = 500,unit = TimeUnit.MILLISECONDS)
    // 给测试方法取个名字
    @DisplayName("测试方法1")
    // 重复测试
    @RepeatedTest(5)
    @Test
    void testDisplayName(){
        System.out.println("测试方法1");
    }

    @DisplayName("测试方法2")
    @Test
    void test2(){
        System.out.println("测试方法2");
    }

    @BeforeEach
    void testBeforeEach(){
        System.out.println("在每个测试方法运行前运行");
    }

    @AfterEach
    void testAfterEach(){
        System.out.println("在每个测试方法运行后运行");
    }

    @AfterAll
    static void testAfterAll(){
        System.out.println("在所有测试方法运行后运行");
    }

    @BeforeAll
    static void testBeforeAll(){
        System.out.println("在所有测试方法运行前运行");
    }
}
```

## 10.2 断言机制

作用：每次项目开启前会运行所有的测试方法，用了断言方法就能快速发现错误所在，并产生一个详细的错误报告

### 10.2.1 单个值断言

用来对单个值进行简单的验证。如：

| 方法            | 说明                                 |
| --------------- | ------------------------------------ |
| assertEquals    | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals | 判断两个对象或两个原始类型是否不相等 |
| assertSame      | 判断两个对象引用是否指向同一个对象   |
| assertNotSame   | 判断两个对象引用是否指向不同的对象   |
| assertTrue      | 判断给定的布尔值是否为 true          |
| assertFalse     | 判断给定的布尔值是否为 false         |
| assertNull      | 判断给定的对象引用是否为 null        |
| assertNotNull   | 判断给定的对象引用是否不为 null      |

```java
@Test
@DisplayName("simple assertion")
public void simple() {
     assertEquals(3, 1 + 2, "simple math");
     assertNotEquals(3, 1 + 1);

     assertNotSame(new Object(), new Object()); // 断言失败后面的代码都不会执行
     Object obj = new Object();
     assertSame(obj, obj);

     assertFalse(1 > 2);
     assertTrue(1 < 2);

     assertNull(null);
     assertNotNull(new Object(),"对象不存在"); // 每个断言方法都可以自定义错误信息
}
```

### 10.2.2 数组断言

通过 assertArrayEquals 方法来判断两个对象或原始类型的数组是否相等

```java
@Test
@DisplayName("array assertion")
public void array() {
 assertArrayEquals(new int[]{1, 2}, new int[] {1, 2});
}
```

### 10.2.3 组合断言

assertAll 方法接受多个 org.junit.jupiter.api.Executable 函数式接口的实例作为要验证的断言，可以通过 lambda 表达式很容易的提供这些断言、

```java
@Test
@DisplayName("assert all")
public void all() {
 assertAll("Math",
    () -> assertEquals(2, 1 + 1),
    () -> assertTrue(1 > 0)
 );
}
```

### 10.2.4 异常断言

JUnit5提供了一种新的断言方式**Assertions.assertThrows()** ,配合函数式编程就可以进行使用。

```java
@Test
@DisplayName("异常测试")
public void exceptionTest() {
    ArithmeticException exception = Assertions.assertThrows(
           //扔出断言异常
            ArithmeticException.class, () -> System.out.println(1 % 0),"业务逻辑正常");

}
```

### 10.2.5 超时断言

Junit5还提供了**Assertions.assertTimeout()** 为测试方法设置了超时时间

```java
@Test
@DisplayName("超时测试")
public void timeoutTest() {
    //如果测试方法时间超过1s将会异常
    Assertions.assertTimeout(Duration.ofMillis(1000), () -> Thread.sleep(500));
}
```

### 10.2.6 快速失败

通过 fail 方法直接使得测试失败

```java
@Test
@DisplayName("fail")
public void shouldFail() {
 fail("This should fail");
}
```

## 10.3 前置条件（assumptions）

JUnit 5 中的前置条件（**assumptions【假设】**）类似于断言，不同之处在于**不满足的断言会使得测试方法失败**，而不满足的**前置条件只会使得测试方法的执行终止**。前置条件可以看成是测试方法执行的前提，当该前提不满足时，就没有继续执行的必要，会跳过此测试方法

```
@DisplayName("前置条件")
public class AssumptionsTest {
 private final String environment = "DEV";
 
 @Test
 @DisplayName("simple")
 public void simpleAssume() {
    assumeTrue(Objects.equals(this.environment, "DEV"));
    assumeFalse(() -> Objects.equals(this.environment, "PROD"));
 }
 
 @Test
 @DisplayName("assume then do")
 public void assumeThenDo() {
    assumingThat(
       Objects.equals(this.environment, "DEV"),
       () -> System.out.println("In DEV")
    );
 }
}
```

assumeTrue 和 assumFalse 确保给定的条件为 true 或 false，不满足条件会使得测试执行终止。assumingThat 的参数是表示条件的布尔值和对应的 Executable 接口的实现对象。只有条件满足时，Executable 对象才会被执行；当条件不满足时，测试执行并不会终止。

## 10.4 嵌套测试

JUnit 5 可以通过 Java 中的内部类和@Nested 注解实现嵌套测试，从而可以更好的把相关的测试方法组织在一起。在内部类中可以使用@BeforeEach 和@AfterEach 注解，而且嵌套的层次没有限制。

```java
@DisplayName("A stack")
class TestingAStackDemo {

    Stack<Object> stack;

    @Test
    @DisplayName("is instantiated with new Stack()")
    void isInstantiatedWithNew() {
        // 嵌套外部的类无法驱动嵌套内部的类，前套内的beforeEach和afterEach不会执行
        // 但是内层的类可以驱动外层的
        new Stack<>();
    }

    @Nested
    @DisplayName("when new")
    class WhenNew {

        @BeforeEach
        void createNewStack() {
            stack = new Stack<>();
        }

        @Test
        @DisplayName("is empty")
        void isEmpty() {
            assertTrue(stack.isEmpty());
        }

        @Test
        @DisplayName("throws EmptyStackException when popped")
        void throwsExceptionWhenPopped() {
            assertThrows(EmptyStackException.class, stack::pop);
        }

        @Test
        @DisplayName("throws EmptyStackException when peeked")
        void throwsExceptionWhenPeeked() {
            assertThrows(EmptyStackException.class, stack::peek);
        }

        @Nested
        @DisplayName("after pushing an element")
        class AfterPushing {

            String anElement = "an element";

            @BeforeEach
            void pushAnElement() {
                stack.push(anElement);
            }

            @Test
            @DisplayName("it is no longer empty")
            void isNotEmpty() {
                assertFalse(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when popped and is empty")
            void returnElementWhenPopped() {
                assertEquals(anElement, stack.pop());
                assertTrue(stack.isEmpty());
            }

            @Test
            @DisplayName("returns the element when peeked but remains not empty")
            void returnElementWhenPeeked() {
                assertEquals(anElement, stack.peek());
                assertFalse(stack.isEmpty());
            }
        }
    }
}
```

## 10.5 参数化测试

**@ParameterizedTest**：定义在方法上，表明此方法进行参数化测试

**@valueSource**：为参数化测试指定入参来源，支持八大基础类以及String类型,Class类型

**@NullSource**: 表示为参数化测试提供一个null的入参

**@EnumSource**: 表示为参数化测试提供一个枚举入参

**@CsvFileSource**：表示读取指定CSV文件内容作为参数化测试入参

**@MethodSource**：表示读取指定方法的返回值作为参数化测试入参(注意方法返回需要是一个流，且是静态方法)

```java
@ParameterizedTest
// 逐个对传入的参数进行测试
@ValueSource(strings = {"one", "two", "three"})
@DisplayName("参数化测试1")
public void parameterizedTest1(String string) {
    System.out.println(string);
    Assertions.assertTrue(StringUtils.isNotBlank(string));
}

@ParameterizedTest
@MethodSource("method")    //指定方法名
@DisplayName("方法来源参数")
public void testWithExplicitLocalMethodSource(String name) {
    System.out.println(name);
    Assertions.assertNotNull(name);
}

// 方法返回的是一个流且是静态的
static Stream<String> method() {
    return Stream.of("aple", "banana");
}
```

# 11.SpringBoot高级特性

## 11.1 profile功能

为了方便多环境适配，springboot简化了profile功能。

### 11.1.1 application-profile的功能

​	适配不同环境的配置文件

- 默认配置文件  application.yaml；任何时候都会加载，与环境配置同时生效

- 更改运行环境并且指定环境配置文件为 application-test.yaml

  ```properties
  spring.profiles.active=test
  ```

- 同名配置项，profile配置优先即指定环境的配置文件

- 激活指定环境

- - 配置文件激活，但是每次更换环境都需要更改配置文件
  - cmd命令行激活：java -jar xxx.jar --**spring.profiles.active=prod  --person.name=haha**

- - - **修改配置文件的任意值，命令行修改的最优先使用**

### 11.1.2 @Profile(根据环境启用类和方法)

​		可以根据不同的环境（如开发环境、测试环境、生产环境等）所对应的不同数据源（c3p0/druid等）动态激活和切换一系列组件的功能。

1.引入数据源，并标注@Profile("xxx")，表示在什么环境下运行此组件

```java
import javax.sql.DataSource;

@PropertySource("classpath:com/jdbc.properties")
// 实现EmbeddedValueResolverAware调用Spring底层组件StringValueResolver
public class MainConfig implements EmbeddedValueResolverAware{
    
    private StringValueResolver stringValueResolver;

    private String driverClass;
    
    // 建立三个不同环境下的数据源
    @Profile("prod")
	@Bean("dataSourceProd")
	public DataSource dataSourceProd() {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setUsername("${jdbc.username}");
        dataSource.setPassword("${jdbc.password}");
        dataSource.setUrl("${jdbc.url}");
        dataSource.setDriverClassName("${jdbc.driver}");
        return dataSource;
	}
    
    // 默认情况下
    @Profile("default")
	@Bean("dataSourceTest")
    public DataSource dataSourceTest() {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setUsername("${jdbc.username}");
        dataSource.setPassword("${jdbc.password}");
        dataSource.setUrl("${jdbc.url}");
        dataSource.setDriverClassName("${jdbc.driver}");
        return dataSource;
    }
	
    @Profile("dev")
    @Bean("dataSourceDev")
    public DataSource dataSourceDev() {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setUsername("${jdbc.username}");
        dataSource.setPassword("${jdbc.password}");
        dataSource.setUrl("${jdbc.url}");
        dataSource.setDriverClassName("${jdbc.driver}");
        return dataSource;
    }
    
    public void setEmbeddedValueResolver(StringValueResolver stringValueResolver) {
        this.stringValueResolver = stringValueResolver;
        driverClass = stringValueResolver.resolveStringValue("${jdbc.driver}");
    }
}
```

### 11.1.3 Profile分组(同时启用两个环境)

```properties
spring.profiles.active=production 

下面两个环境的配置文件和环境都会启用
spring.profiles.group.production[0]=proddb 
spring.profiles.group.production[1]=prodmq

命令行中使用：--spring.profiles.active=production  激活
```

## 11.2 外部配置源

常用：**Java属性文件(.properties)**、**YAML文件**、**环境变量(高级设置-环境变量)**、**命令行参数(cmd)**；

### 11.2.1 配置文件查找的路径

(1) classpath 根路径

(2) classpath 根路径下config目录（resources-config-application.ymal）

(3) jar包当前目录(打包后的jara包，和配置文件放在同一个目录下运行)

(4) jar包当前目录的config目录

(5) /config子目录的直接子目录

注意：下一个的配置生效优先级高于上一个的，下面的配置会覆盖上面的

### 11.2.2 配置文件加载顺序

1. 当前jar包内部的application.properties和application.yml
2. 当前jar包内部的application-{profile}.properties 和 application-{profile}.yml
3. 引用的外部jar包的application.properties和application.yml
4. 引用的外部jar包的application-{profile}.properties 和 application-{profile}.yml

注意：同样下面的配置会覆盖上面的配置

## 11.3 扩展点：自定义starter

- 创建starter包

- 创建自动配置AutoConfiguration包
- 在starter包中引入AutoConfiguration包依赖
- 在AutoConfiguration包中写入

## 11.4 扩展点：SpringBoot监听器

实现SpringApplicationRunListener并创建有参构造方法可以在不同时间点调用监听器方法时对SpringApplication进行操作或其它操作。

```java
// 这个类不需要@Component，因为SpringBoot已经存在类一个实现类实现了SpringApplicationRunListener方法，因此会自动导入其所有实现类，但是要有有参构造方法
public class MySpringApplicationRunListener implements SpringApplicationRunListener {
    
    private SpringApplication application;
    
    public MySpringApplicationRunListener (SpringApplication application, String args[]) {
        this.application = application;   
    }

    @Override
    public void starting(ConfigurableBootstrapContext bootstrapContext) {
        System.out.println("遍历SpringApplicationRunListener调用 starting 方法；");
        application
    }

    @Override
    public void environmentPrepared(ConfigurableBootstrapContext bootstrapContext, ConfigurableEnvironment environment) {
		System.out.println("监听器调用 listener.environmentPrepared()；通知所有的监听器当前环境准备完成");
    }

    @Override
    public void contextPrepared(ConfigurableApplicationContext context) {
		System.out.println("遍历所有的 listener 调用 contextPrepared通知所有的监听器contextPrepared");
    }

    @Override
    public void contextLoaded(ConfigurableApplicationContext context) {
		
    }

    @Override
    public void started(ConfigurableApplicationContext context) {
		所有监听 器 调用 listeners.started(context); 通知所有的监听器 started
    }

    @Override
    public void running(ConfigurableApplicationContext context) {
		在run方法之后调用
    }

    @Override
    public void failed(ConfigurableApplicationContext context, Throwable exception) {
		以上任意异常会调用
    }
}
```

## 11.5 扩展点：CommandLinerRunner和**ApplicationRunner** 

在监听器started方法之后running方法调用之前，调用CommandLinerRunner和ApplicationRunner的run方法在应用启动时做一个一次性的事情。

```
// 为自定义的run方法设定优先级
@Order(1)
@Component
public class MyCommandLineRunner implements CommandLineRunner{

    @Override
    public void run(String... args) throws Exception {
        
    }
}
```

# 12.SpringBoot整合SpringSecurity

## 12.1 SpingSecurity简介

SpringSecurity中重要的类：

- WebSecurityConfigurerAdaptor：自定义Security策略
- AuthenticationManagerBuilder：自定义认证策略
- @EnableWebSecurity：开启WebSecurity模式

SpringSecurity的两个主要目标：

- 认证：Authentication
- 授权：Authorization

SpringSecurity的实现主要思想：运用的AOP切面编程的思想，不需要改变源代码直接进行功能增强

## 12.2 SpingSecurity的实现步骤

1. 引入依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-security</artifactId>
   </dependency>
   ```

2. 创建Security配置类重写授权方法和认证方法

   ```java
   @EnableWebSecurity
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
       // 授权
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           // 首页所有人都能访问，功能页只有对应权限的人员能够进入
           http.authorizeRequests()
                   .antMatchers("/").permitAll()
                   .antMatchers("/1/**").hasRole("vip1")
                   .antMatchers("/main.html").hasRole("vip2");
   
           // 没有权限会跳转到登录页面
           http.formLogin();
       }
   
       // 认证
   
       @Override
       protected void configure(AuthenticationManagerBuilder auth) throws Exception {
           // 从内存中获取数据进行认证，auth.jdbcAuthentication()可以从数据库库中获取
           auth.inMemoryAuthentication()
                   .withUser("zhuwenchuan").password("123456").roles("vip1","vip2")
                   // and进行拼接更多的认证信息
                   .and()
                   .withUser("xijingping").password("123456").roles("vip1");
           
           // 从数据库中获取权限认证信息
           
       }
   }
   ```

# 13.Shiro

## 13.1 获取当前用户

```java
Subject currentUser = SecurityUtils.getSubject();
```

原理：SecurityUtils.getSubject()是每个请求创建一个Subject, 并保存到ThreadContext的resources（ThreadLocal<Map<Object, Object>>）变量中，也就是一个http请求一个subject,并绑定到当前线程。

## 13.2 判断登录是否成功

```java
currentUser.login(new UsernamePasswordToken(username, password));
```

不成功（账号或密码不对）会抛出异常.

## 13.3判断下一次请求所属用户

**问题**：currentUser.login()登陆认证成功后，下一次请求如何知道是那个用户的请求呢？

**答案**：每次请求都会重新设置Session和Principals,看到这里大概就能猜到：如果是web工程，直接从web容器获取httpSession，然后再从httpSession获取Principals，本质就是从cookie获取用户信息，然后每次都设置Principal，这样就知道是哪个用户的请求。（本质：依赖于浏览器的cookie来维护session的）

## 13.4参考

https://blog.csdn.net/yangfengjueqi/article/details/86517647





