# Spring
## 概述
Spring的核心是IOC(反转控制)和AOP(面向切向编程)，最核心的是IOC

## Spring开发步骤
* 导入Spring依赖
```
<!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.0.5.RELEASE</version>
</dependency>

```
* 编写Dao接口和实现类
* 创建Spring配置文件
    * 在src目录下的resources目录下创建applicationContext.xml文件，文件名不强制要求
    配置文件如下
    ```
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean   bean id ="userDao" class="Impl.UserDaoImpl"></bean>
    </beans>
    ```
    
* 在配置文件中配置dao实现类
* 使用Spring的API获得Bean的实例
```
ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
UserDao ud=(UserDao)ac.getBean("userDao");
 ud.save();
```
## Spring的配置文件
### Bean标签的基本配置
* id
* class
* 默认情况下调用无参构造
* scope
    * singleton:单例的，默认的，容器中只有唯一一个，调用多次都是同一个对象
    * prototype：多例的
### Bean的生命周期配置
* init-method:指定类中初始化的方法名称
* destory-method:指定类中销毁对象的方法名称
### Bean实例化的方法
* 无参构造
* 工程静态方法
```
public static UserDaoImpl getUserDao()
{
    System.out.println("static factory");

    return new UserDaoImpl();
}

<bean id ="userDao" class="demo.UserStaticFactory" factory-method="getUserDao"></bean>

```
* 工程实例方法
```
    <bean id="factory" class="demo.UserStaticFactory"></bean>
    <bean id ="userDao" factory-bean="factory" factory-method="getUserDao"></bean>
```
## Spring的依赖注入方式
### 构造方法

```
    <bean id="userService" class="Impl.UserServiceImpl">
        <constructor-arg name="userDao" ref="userDao"></constructor-arg>
    </bean>


    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }
```
### set方法
```
    <bean id ="userDao" class="Impl.UserDaoImpl"></bean>

    <bean id="userService" class="Impl.UserServiceImpl">
        <property name="userDao" ref="userDao"></property>
    </bean>
    //name参数是UserService里面的属性名，首字母小写
    //ref后面的参数是id标识

    或有p命名空间
    xmlns:p="http://www.springframework.org/schema/p"
    //上面这行写在<beans>标签里面
    <bean id="userService" class="Impl.UserServiceImpl" p:userDao-ref="userDao"/>


    public class UserServiceImpl implements UserService{
    private UserDao userDao;
    @Override
    public void save() {
        userDao.save();
    }

    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

}
```
## Spring的依赖注入的数据类型
### 普通数据类型

```

private String name;
    private int age;

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }


    <bean id ="userDao" class="Impl.UserDaoImpl">
        <property name="name" value="张三"></property>
        <property name="age" value="18"></property>
    </bean>
```
### 引用数据类型

### 集合数据类型

## 分模块开发
实际开发中，Spring的配置内容非常多，这就导致Spring配置很繁杂体积很大，所以，可以将部分配置拆解到其他配置文件中，而在Spring主配置文件中通过import标签进行加载
```
<import resource="applicationContext-xxx.xml"/>
```

## Spring的相应API

### ApplicationContext的继承体系
* ClassPathXmlApplicationContext
    他是从类的根路径加载，推荐
* FileSystemXmlApplicationContext
    从磁盘路径加载
*  AnnotationConfigApplicationContext
    当使用注解配置容器时，需要用此类来创建Spring容器，它用来读取注解

## Spring配置数据源
### 数据源开发步骤
* 导入数据源坐标和数据库驱动坐标
* 创建数据源对象
* 设置数据源的基本连接数据
* 使用数据源获取连接资源与归还资源
* 加载properties文件的又一种方法
```
        ResourceBundle rb = ResourceBundle.getBundle("jdbc");
        //方法参数是在类的根目录的文件名，不需要后缀，用于加载properties文件

        String driver = rb.getString("jdbc.driver");
        String url = rb.getString("jdbc.url");
        String username = rb.getString("jdbc.username");
        String password = rb.getString("jdbc.password");
        //这是properties的内容
        jdbc.driver=com.mysql.cj.jdbc.Driver
        jdbc.url=jdbc:mysql://127.0.0.1:3306/user?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8&useSSL=false
        jdbc.username=root
        jdbc.password=59192584

```
* 通过Spring创建c3p0对象
```
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://127.0.0.1:3306/user?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false"></property>
        <property name="user" value="root"></property>
        <property name="password" value="59192584"></property>
    </bean>

    @org.junit.Test
    public void test4() throws SQLException {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("ApplicationContext.xml");
        ComboPooledDataSource dataSource = applicationContext.getBean(ComboPooledDataSource.class);
        Connection connection = dataSource.getConnection();
        System.out.println(connection);
    }
```
### Spring配置文件加载properties文件
关键代码
```
xmlns:context="http://www.springframework.org/schema/context"
xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"


<context:property-placeholder location="xxx.properties"></context:property-placeholder>


//通过这种方式读取值
${key}
```

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"></property>
        <property name="jdbcUrl" value="${jdbc.url}"></property>
        <property name="user" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>
</beans>
```
## Spring的注解开发
### Spring的原始注解
* Spring是轻代码而重配置的框架，配置比较繁琐，影响开发效率，所以注解开发是一种趋势，注解代替xml配置文件可以简化配置，提高开发效率
* Spring原始注解主要是替代<bean>标签配置
    * @Component	使用在类上用于实例化Bean
        * @Component("id标识")
        ```
        <bean id="userService" class="com.example.AnnotaionContext.Service.UserServiceImpl">
        //就相当于
        @Component("userService")

        <property name="userDao" ref="userDao"></property>
        //就相当于
        @Autowired
        @Qualifier("userDao")
        使用注解方式注入可以不写set方法
        ```
    * @Controller	使用在web层类上用于实例化Bean
    * @Service	使用在service层类上用于实例化Bean
    * @Repository	使用在dao层类上用于实例化Bean
    * Autowired	使用在字段上用于根据类型依赖注入
    * @Qualifier	结合@Autowired一起使用用于根据名称进行依赖注入
    * @Resource	相当于@Autowired+@Qualifier，按照名称进行注入
    * @Value	注入普通属性
    * @Scope	标注Bean的作用范围
    * @PostConstruct	使用在方法上标注该方法是Bean的初始化方法
    * @PreDestroy	使用在方法上标注该方法是Bean的销毁方法
* 组件扫描
```
<context:component-scan base-package="com.example.AnnotaionContext"></context:component-scan>
//base-package 是一个包名，它可以扫描这个包下所有的注解
```
### Spring新注解
有一些操作是原始注解没法做到的：
* 非自定义的bean的配置<bean>
* 加载properties文件的配置<cotext:property-placeholder>
* 组件扫描<cotext:component-scan>
* 引入其他文件<import>
新注解：
* @Configuration用于指定当前类是一个Spring配置类，当创建容器时会从该类上加载注解
* @ComponentScan用于指定Spring在初始化时要扫描的包。作用和Spring的xml配置文件中的```<context:component-scan beas-package=“com.szl”/>```一样
* @Bean使用在方法上，标注将该方法的返回值存储到Spring容器中
* @PropertySource用于加载.properties文件中中的配置
* @Import用于导入其他配置类

示例代码
```
@Configuration//主配置标识
@ComponentScan("com.example.AnnotaionContext")//组件扫描
//<context:component-scan base-package="com.example.AnnotaionContext"></context:component-scan>

@Import(DataSourceConfiguration.class)//引入分配置
public class SpringConfiguration {

}


//DAO类
@PropertySource("classpath:jdbc.properties")//加载properties文件
//<context:property-placeholder location="classpath:jdbc.properties"></context:property-placeholder>
public class DataSourceConfiguration {
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;
    /*
    * <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.driver}"></property>
        <property name="jdbcUrl" value="${jdbc.url}"></property>
        <property name="user" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
    </bean>*/
    @Bean("dataSource")
    public DataSource getDataSource() throws PropertyVetoException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass(driver);
        dataSource.setJdbcUrl(url);
        dataSource.setUser(username);
        dataSource.setPassword(password);
        return dataSource;
    }
}


//调用代码
public static void main(String[] args) throws SQLException {
        //ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(SpringConfiguration.class);
        UserService bean = applicationContext.getBean(UserService.class);
        DataSource dataSource = (DataSource)applicationContext.getBean("dataSource");
        Connection connection = dataSource.getConnection();
        System.out.println(connection);
        bean.save();
    }
```
* 对于非自定义类可以使用@Bean对方法进行注解，方法返回值为非自定义类实例化对象

### Spring集成JUtil
测试类中总是要写获取容器的代码，比较繁琐
步骤：
* 导入Spring集成JUnit和JUnit的坐标
* 使用@Runwith注解替换原来的运行期
* 使用@ContextConfiguration指定配置文件或配置类
* 使用@Autowired注入需要测试的对象
* 创建测试方法进行测试
```
@RunWith(SpringJUnit4ClassRunner.class)
//@ContextConfiguration("classpath:applicationContext.xml")
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJUnitTest {


    @Autowired
    private UserService userService;
    @Test
    public void test1()
    {
        userService.save();
    }
}
```
以后测试就只需要在该类中添加测试方法和注入测试对象即可

## Spring与web环境的集成
在Web项目中，可以使用ServletContextListener监听Web应用的启动，我们可以在Web应用启动时，就加载Spring的配置文件，创建应用上下文对象Application，再将其存储在最大的域ServletContext中，这样就可以在任意位置从域中应用上下文的ApplicationContext对象了

* 创建监听器类
```
public class ContextListenerLoader implements ServletContextListener {
    public void contextInitialized(ServletContextEvent sce) {
        ApplicationContext app = new AnnotationConfigApplicationContext(SpringConfiguration.class);
        ServletContext servletContext = sce.getServletContext();
        servletContext.setAttribute("app",app);
        System.out.println("监听器初始化成功");
    }

    public void contextDestroyed(ServletContextEvent sce) {
    }

}
```
* 在web.xml文件中配置监听器
```
    <listener>
        <listener-class>com.example.AnnotaionContext.listener.ContextListenerLoader</listener-class>
    </listener>
```

* 在Servlet中调用
```
ApplicationContext app = (ApplicationContext)this.getServletContext().getAttribute("app");
        UserDao userDao = (UserDao)app.getBean("userDao");
        userDao.save();
```


* 可以利用SevletContext的getInitParameter方法获取web.xml文件中的全局初始化参数降低耦合度

```
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>配置文件地址</param-value>
    </context-param>


ServletContext servletContext = sce.getServletContext();
ApplicationContext app = new ContextPathXmlApplicationContext(servletContext.getInitParameter(contextConfigLocation));
        

```
这样就可以在配置文件中规定spring配置文件的文件名
### Spring-web工具类
上述功能Spring已经封装好了，在spring-web jar包下
所以我们利用Spring提供的工具，步骤为
* 添加spring-web坐标，在web.xml文件中配置ContextLoderListener监听器
* 使用WebApplicationContextUtils获得上下文对象ApplicationContext
* web.xml配置
```
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
```
* 该工具类大致实现方法,不需要自己实现
```
ServletContext servletContext = sce.getServletContext();
        String contextConfiguration =  servletContext.getInitParameter("contextConfiguration");
        ApplicationContext app = new ClassPathXmlApplicationContext(contextConfiguration);
        servletContext.setAttribute("app",app);
```
* 调用方法
```
ServletContext servletContext = this.getServletContext();
        ApplicationContext app = WebApplicationContextUtils.getWebApplicationContext(servletContext);
        UserDao userDao = (UserDao)app.getBean("userDao");
        userDao.save();
```

# SpringMVC
开发步骤：
* 导入SpringMVC相关坐标
* 配置SpringMVC核心控制器DispacherServlet
    在web.xml文件中进行配置
    ```
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    ```
* 创建Controller和和视图页面
```
@Controller
public class UserController {
    @RequestMapping("/quick")
    public String save()
    {
        System.out.println("Controller save running...");

        return "success.jsp";
    }
}
```
视图页面就是方法返回的jsp文件名对应的jsp文件
* 使用注解配置Controller类中业务方法的映射地址
```
@RequestMapping("/quick")
```
* 配置SpringMVC核心文件spring-mvc.xml,进行Controller的组件扫描
```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
    http://www.springframework.org/schema/mvc
    http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-4.2.xsd">

<!--    Controller组件扫描-->
        <context:component-scan base-package="com.example.AnnotaionContext.controller"/>

</beans>
```

* 客户端发起请求测试

## Spring的组件
### Spring注解解析
* @RequestMapping
    * 作用:用于建立请求URL和处理请求方法之间的对应关系
    * 位置: 
        * 类上, 请求URL的第一级访问目录。 此处不写的话，就相当于应用的根目录
        * 方法上,请求URL的第二级访问目录, 与类上的使用@ReqquestMapping标注的一级目录起组成访问虚拟路径
    * 属性:
        * value:用于指定请求的URL。它和path属性的作用是一样的
       * method:用于指定请求的方式
       * params:用于指定限制请求参数的条件。它支持简单的表达式。要求请求参数的key和value必须和配置的一模-样
```
@RequestMapping(value="/quick" method = RequestMethod.GET params = {"username"})


params = {"username"}意思是请求参数必须要有username
params = {"username!xxx"}意思是username值不等于xxx
```
* 组件扫描,还能过滤
```
<context:component-scan base-package="com.example.AnnotaionContext.controller">
                <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        </context:component-scan>
```
* SpringMVC配置xml解析
    * 配置视图解析器
    ```
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
                <property name="prefix" value="/jsp/"></property>
                <property name="suffix" value=".jsp"></property>
        </bean>
    ```
    这样Controller里的方法只需要返回一个文件名就行了，会自动拼接成/jsp/xxx.jsp

## SpringMVC的数据响应
1.页面跳转
* 直接返回字符串
    * 此种方法会将返回的字符串与视图解析器的前后缀拼接后跳转
    ```
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
                <property name="prefix" value="/jsp/"></property>
                <property name="suffix" value=".jsp"></property>
    </bean>


    @RequestMapping("/quick")
    public String save()
    {
        System.out.println("Controller save running...");

        return "success";
    }
    ```
* 返回ModelAndView对象
```
@RequestMapping("/quick2")
    public ModelAndView test2()
    {

        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("hello","Hello");
        modelAndView.setViewName("success");
        return modelAndView;
    }


@RequestMapping("/quick3")
    public ModelAndView test3(ModelAndView modelAndView)
    {
        
        modelAndView.addObject("hello","Hello");
        modelAndView.setViewName("success");
        return modelAndView;
    }
```
2.回写数据
* 直接返回字符串
    * 通过SpringMVC框架注入的response对象，使用response.getWriter0.print( "helloworld" )回写数据，此时不需要视图跳转,业务方法返回值为void。
    ```
    @RequestMapping("/quick4")
    public void test4(HttpServletResponse response) throws IOException {
        PrintWriter writer = response.getWriter();
        writer.write("hello");
    }
    ```

    * 将需要回写的字符串直接返回，但此时需要通过@ResponseBody注解告知SpringMVC框架，方法。返回的字符串不是跳转是直接在http响应体中返回。
    ```
    @RequestMapping("/quick5")
    @ResponseBody
    public String test5()  {
        return "hello";
    }
    ```

* 返回对象或集合
    * 在spring-mvc.xml文件配置
    ```
    <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
                <property name="messageConverters">
                        <list>
                                <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter"/>
                        </list>
                </property>
        </bean>
    ```
    使得SpringMVC框架可以将对象或集合直接转换成json格式的字符串然后返回
    ```
    @RequestMapping("/quick6")
    @ResponseBody
    public User test6() throws JsonProcessingException {
        User user = new User("张三",18);
        return user;
    }
    ```
    页面上出现内容如下
    ```
    {"username":"张三","age":18}
    ```
    * 在方法.上添加@ResponseBody就可以返回jison格式的字符串，但是这样配置比较麻烦，配置的代码比较多，因此，我们可以使用mvc的注解驱动代替上述配置。

    在SpringMVC的各个组件中,处理器映射器、处理器适配器、视图解析器称为SpringMVC的三大组件。使用```<mvc:annotation-driven/>```自动加载RequestMappingHandlerMapping (处理映射器)和RequestMappingHandlerAdapter (处理适配器)，可用在Spring-xml.xmI配置文件中使用```<mvc:annotation-driven/>```替代注解处理器和适配器的配置。同时使用```<mvc:annotation-driven/>```默认底层就会集成jackson进行对象或集合的json格式字符串的转换。

## SpringMVC获得请求数据

客户端请求参数的格式是: name=value&name=value...
服务器端要获得请求的参数,有时还需要进行数据的封装，SpringMVC 可以接收如下类型的参数:
* 基本类型参数
* POJO类型参数，简单JavaBean
* 数组类型参数
* 集合类型参数

### 获得基本类型参数
Controller中的业务方法的参数名称要与请求参数的name-致,参数值会自动映射匹配。


http://localhost/quick7?username=zhangsan&age=18，这样就可以自动匹配了
```
    @RequestMapping("/quick7")
    @ResponseBody
    public void test7(String username,int age)
    {
        System.out.println(username);
        System.out.println(age);
    }
    
```

### 获得POJO类型参数
访问http://localhost/quick8?username=zhangsan&age=18，可以自动封装User对象
```
@RequestMapping("/quick8")
    @ResponseBody
    public void test8(User user)
    {
        System.out.println(user);

    }
```

### 获得数组类型参数
Controller中的业务方法数组名称与请求参数的name-致, 参数值会自动映射匹配。
访问http://localhost/quick9?strs=111&strs=222&strs=333
```
 @RequestMapping("/quick9")
    @ResponseBody
    public void test9(String[] strs)
    {
        System.out.println(Arrays.asList(strs));

    }
```

### 获得集合类型封装
* 获得集合参数时，要将集合参数包装到一个POJO中才可以。

    * 写个页面
        ```
        

        ```
        * ${pageContext.request.contextPath}是JSP取得绝对路径的方法，等价于<%=request.getContextPath()%> 。也就是取出部署的应用程序名或者是当前的项目名称。比如我的项目名称是demo1在浏览器中输入为http://localhost:8080/demo1/a.jsp ${pageContext.request.contextPath}或<%=request.getContextPath()%>取出来的就是/demo1,而"/"代表的含义就是http://localhost:8080。故有时候项目中这样写${pageContext.request.contextPath}/a.jsp