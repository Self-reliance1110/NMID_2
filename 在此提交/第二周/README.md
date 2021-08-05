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

## SpringAOP
AOP为Aspect Oriented Programming的缩写,意思为面向切面编程,是通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。
### AOP的底层实现
实际上，AOP的底层是通过Spring提供的的动态代理技术实现的。在运行期间，Spring通过动态代理技术动态的生成代理对象，代理对象方法执行时进行增强功能的介入，在去调用目标对象的方法,从而完成功能的增强。
### AOP的动态代理技术
常用的动态代理技术
* JDK代理:基于接口的动态代理技术
* cglib代理:基于父类的动态代理技术

### JDK的动态代理
底层代码：
```
public static void main(String[] args) {


        final Target target = new Target();
        Advice advice = new Advice();
        TargetInterface proxy = (TargetInterface) Proxy.newProxyInstance(target.getClass().getClassLoader(),
                target.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        advice.before();//前置增强
                        method.invoke(target,args);//执行目标方法
                        advice.after();//后置增强
                        return null;
                    }
                });
        //调用代理对象的方法
        proxy.save();
    }
```
### cglib动态代理
底层代码
```
public static void main(String[] args) {

        final Target target = new Target();//目标对象
        final Advice advice = new Advice();//增强对象


        //1.创建增强器
        Enhancer enhancer = new Enhancer();
        //2.设置父类
        enhancer.setSuperclass(Target.class);
        //3.设置回调
        enhancer.setCallback(new MethodInterceptor() {
            @Override
            public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
                //执行前置
                advice.before();
                //执行目标
                Object invoke = method.invoke(target, args);
                //执行后置
                advice.after();
                return invoke;
            }
        });
        //4. 创建代理对象
        Target proxy = (Target) enhancer.create();
        proxy.save();
    }
```
### AOP相关概念
Spring的AOP实现底层就是对上面的动态代理的代码进行了封装，封装后我们只需要对需要关注的部分进行代码编写,并通过配置的方式完成指定目标的方法增强。
常用相关术语
Target (目标对象) :代理的目标对象
Proxy (代理) :一个类被AOP织入增强后，就产生-个结果代理类
Joinpoint (连接点) :所谓连接点是指那些被拦截到的点。在spring中,这些点指的是方法，因为spring只支持方法类型的连接点，即可以被增强的方法
Pointcut (切入点) :所谓切入点是指我们要对哪些Joinpoint进行拦截的定义，即真的被增强的方法
Advice (通知/增强) :所谓通知是指拦截到Joinpoint之后所要做的事情就是通知，即增强方法
Aspect (切面) :是切入点和通知(引介)的结合
Weaving (织入) : 是指把增强应用到目标对象来创建新的代理对象的过程。spring采用动态代理织入,而Aspect采用编译期织入和类装载期织入，即切入点和通知(引介)的结合的过程

### AOP开发明确的事项
1.需要编写的内容
* 编写核心业务代码( 目标类的目标方法)
* 编写切面类， 切面类中有通知(增强功能方法)
* 在配置文件中，配置织入关系,即将哪些通知与哪些连接点进行结合
* AOP底层使用哪种代理方式：在spring中,框架会根据目标类是否实现了接口来决定采用哪种动态代理的方式。

### AOP开发步骤
* 导入AOP相关坐标:spring-context,aspectjweaver
* 创建目标接口和目标类(内部有切点)
* 创建切面类(内部有增强方法)
* 将目标类和切面类的对象创建权交给spring
```
<bean id="target" class="com.example.AnnotaionContext.AOP.Target"/>
<bean id="aspect" class="com.example.AnnotaionContext.AOP.Advice"/>
```
* 在applicationContext.xml中配置织入关系
```
<bean id="target" class="com.example.AnnotaionContext.AOP.Target"/>

<bean id="Aspect" class="com.example.AnnotaionContext.AOP.Advice"/>

<aop:config>
    <aop:aspect ref="Aspect">
        <aop:before method="before" pointcut="execution(public void com.example.AnnotaionContext.AOP.Target.save())"/>
    </aop:aspect>
</aop:config>
```
### XML配置AOP详解
1.切点表达式的写法
* 表达式语法:execution([修饰符]返回值类型包名.类名.方法名(参数))
* 访问修饰符可以省略
* 返回值类型、包名、类名、方法名可以使用星号*代表任意
* 包名与类名之间一一个点.代表当前包下的类,两个点..示当前包及其子包下的类
* 参数列表可以使用两个点..表示任意个数,任意类型的参数列表
2.通知的类型
通知的配置语法:
* <aop:通知类型 method = "切面类中方法名" pointcut= "切点表达式"> </aop:通知类型>
* 前置通知<aop:before>用于配置前置通知。指定增强的方法在切入点方法之前执行
* 后置通知<aop:after-returning>用于配置后置通知。指定增强的方法在切入点方法之后执行
* 环绕通知<aop:around>用于配置环绕通知。指定增强的方法在切入点方法之前和之后都
执行
* 异常抛出通知<aop:throwing>用于配置异常抛出通知。指定增强的方法在出现异常时执行
* 最终通知<aop:after>用于配置最终通知。无论增强方式执行是否有异常都会执行
3.切点表达式可以抽取，百度搜

### 基于注解的AOP开发
1.创建目标接口和目标类(内部有切点)
* @Aspect
2.创建切面类(内部有增强方法)
3.将目标类和切面类的对象创建权交给spring
4.在切面类中使用注解配置织入关系
5.在配置文件中开启组件扫描和AOP的自动代理



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

## SpringMVC的组件
### SpringMVC注解解析
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
        <form action="${pageContext.request.contextPath}/quick11" method="post">
            <input type="text" name="userList[0].username"><br>
            <input type="text" name="userList[0].age"><br>
            <input type="text" name="userList[1].username"><br>
            <input type="text" name="userList[1].age"><br>
            <input type="submit" value="提交">
        </form>

        ```
        * ${pageContext.request.contextPath}是JSP取得绝对路径的方法，等价于<%=request.getContextPath()%> 。也就是取出部署的应用程序名或者是当前的项目名称。比如我的项目名称是demo1在浏览器中输入为http://localhost:8080/demo1/a.jsp ${pageContext.request.contextPath}或<%=request.getContextPath()%>取出来的就是/demo1,而"/"代表的含义就是http://localhost:8080。故有时候项目中这样写${pageContext.request.contextPath}/a.jsp
    
    * Controller代码
    ```
    @RequestMapping("/quick11")
    @ResponseBody
    public void test11(VO vo)
    {
        System.out.println(Arrays.asList(vo.getUserList()));
    }
    ```
    * VO类代码
    ```
    public class VO {
        private List<User> userList;
        //....set，get，toString方法省略
    }

    ```
* 当使用ajax提交时，可以指定contentType为json形式， 那么在方法参数位置使用  @RequestBody可以直接接收集合数据而无需使用POJO进行包装。

没学jquery，先跳过....

### 静态资源的访问开启

```

        <mvc:resources mapping="/img/**" location="/img/"/>
        mapping="/img/**" 是访问地址中含有这个
        location="/img/" 是资源的真实目录

```
或者
```
        <mvc:default-servlet-handler/>
        代表是如果找不到文件就交给tomcat处理
```

### 请求数据的乱码问题
get请求服务器会自己解决
当post请求时，数据会出现乱码,我们可以设置一个过滤器来进行编码的过滤。
在web.xml文件配置一个全局过滤器
```
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```
### 参数绑定注解@requestParam
当请求的参数名称与Controller的业务方法参数名称不一致时,就需要通过@RequestParam注解显示的绑定。
```
@RequestMapping("/quick12")
    @ResponseBody
    public void test12(@RequestParam("name") String username)
    {
        System.out.println(username);
    }
```
注解@RequestParam还有如下参数可以使用:
* value:与请求参数名称
* required:此在指定的请求参数是否必须包括，默认是true,提交时如果没有此参数则报错
* defaultValue:当没有指定请求参数时，则使用指定的默认值赋值

### 获得Restful风格的参数
Restful是一种软件架构风格、 设计风格，而不是标准，只是提供了-组设计原则和约束条件。主要用于客户端和服务器交互类的软件，基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存机制等。
Restful风格的请求是使用“url+请求方式”示次请求目的的，HTTP协议里面四个表示操作方式的动词如下:
* GET:用于获取资源
* POST:用于新建资源
* PUT:用于更新资源
* DELETE:用于删除资源

例如:
* /user/1 GET : 得到id=1的user
* /user/1 DELETE: 删除id= 1的user
* /user/1 PUT: 更新id=1的user
* /userPOST: 新增user

上述ur|地址/user/1中的1 就是要获得的请求参数，在SprpgMVC中可以使用 占位符进行参数绑定。地址/user/1可以写成/user/id},占位符{id}对应的就是1的值。在业务方法中我们可以使用@PathVariable注解进行 占位符的匹配获取工作。

```http://localhost/quick13/zhangsan```
```
    @RequestMapping("/quick13/{username}")
    @ResponseBody
    public void test13(@PathVariable(value = "username") String username)
    {
        System.out.println(username);
    }
```

### 自定义类型转换器
* SpringMVC 默认已经提供了一些常用的类型转换器， 例如客户端提交的字符串转换成int型进行参数设置。
* 但是不是所有的数据类型都提供了转换器,没有提供的就需要自定义转换器，例如:日期类型的数据就需要自定义转换器。

自定义类型转换器的开发步骤:
* 定义转换器类实现Converter接口
* 在配置文件中声明转换器
* 在<annotation-driven> 中引用转换器

### 获得Servlet相关API
SpringMVC支持使用原始ServletAPI对象作为控制器方法的参数进行注入，常用的对象如下:
* HttpServletRequest
* HttpServletResponse
* HttpSession
```
@RequestMapping("/quick15")
    @ResponseBody
    public void test15(HttpServletRequest request,HttpServletResponse response)
    {
        System.out.println(request);
        System.out.println(response);
    }
```

### 获取请求头
* 使用@RequestHeader可以获得请求头信息，相当于web阶段学习的request.getHeader(name)
    @RequestHeader注解的属性如下:
    * value:请求头的名称
    * required: 是否必须携带此请求头
    ```

    @RequestMapping("/quick16")
    @ResponseBody
    public void test16(@RequestHeader(value = "User-Agent")String agent)
    {
        System.out.println(agent);

    }
    ```
* 使用@CookieValue可以获得指定Cookie的值
    @CookieValue注解的属性如下: 
    * value:指定cookie的名称
    * required:是否必须携带此cookie

### 文件上传
1.文件上传客户端三要素
* 表单项type= "file"
* 表单的提交方式是post
* 表单的enctype属性是多部分表单形式，即enctype = "multipart/form-data”

2.文件上传原理
* 当form表单修改为多部分表单时，request.getParameter()将失效。
* enctype= "application/x-www-form-urlencoded" 时，form表单的正文内容格式是:
key=value&key=value&key=value
* 当form表单的enctype取值为Mutilpart/form-data时，请求正文内容就变成多部分形式

### 单文件上传步骤
* 导入fileupload和io坐标
```
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.4</version>
        </dependency>

        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.2</version>
        </dependency>
```
* 配置文件上传解析器，spring-mvc.xml
```
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
                <property name="defaultEncoding" value="UTF-8"></property>
<!--                上传单个文件的大小-->
                <property name="maxInMemorySize" value="500000"></property>
</bean>
```

* 编写文件上传代码
```
@RequestMapping("/quick17")
    @ResponseBody
    public void test17(String username, MultipartFile upload) throws IOException {
        String originalFilename = upload.getOriginalFilename();
        upload.transferTo(new File("D:\\upload\\"+originalFilename));

    }
```
### 多文件上传
多文件上传，只需要将页面修改为多个文件上传项，将方法参数MultipartFile类型修改为MultipartFile[]即可


## SpringMVC拦截器
### 拦截器(interceptor) 的作用
Spring MVC的拦截器类似于Servlet开发中的过滤器Filter,用于对处理器进行预处理和后处理。
将拦截器按一定的顺序联结成一条链, 这条链称为拦截器链(Interceptor Chain)。在访问被拦截的方法或字段时，拦截器链中的拦截器就会按其之前定义的顺序被调用。拦截器也是AOP思想的具体实现。

### 步骤
1.创建拦截器类实现HandlerInterceptor接口
```
public class MyInterceptor implements HandlerInterceptor {
    //在目标方法执行前执行,返回true放行，返回false后边的都不执行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        return true;
    }

    //在目标方法执行后执行，在视图对象返回之前执行
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

        System.out.println("postHandle");
    }

    //在整个流程执行完毕后执行
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

        System.out.println("afterCompletion");
    }
}
```
2.配置拦截器
```
<mvc:interceptors>
        <mvc:interceptor>
                <mvc:mapping path="/**"/>
                <bean class="com.example.AnnotaionContext.interceptor.MyInterceptor"/>
        </mvc:interceptor>
</mvc:interceptors>
```

## SpringMVC的异常处理
系统中异常包括两类:预期异常和运行时异常RuntimeException,前者通过捕获异常从而获取异常信息，后者主要通过规范代码开发、测试等手段减少运行时异常的发生。
系统的Dao、Service、 Controller出现都通过throws Exception向.上抛出,最后由SpringMVC前端控制器交由异常处理器(HandlerExceptionResolver)进行异常处理

### 异常处理两种方式
* 使用Spring MVC提供的简单异常处理器SimpleMappingExceptionResolver
    * SpringMVC已经定义好了该类型转换器，在使用时可以根据项目情况进行相应异常与视图的映射配置
    ```
    <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
<!--                默认错误页面，因为配置了视图解析器，就不用加前后缀-->
                <property name="defaultErrorView" value="defaultError"/>
                <property name="exceptionMappings">
                        <map>
                                <entry key="异常类" value="错误页面"></entry>
                        </map>
                </property>
        </bean>
    ```

* 实现Spring的异常处理接口HandlerExceptionResolver自定义自己的异常处理器
    * 创建异常处理器类实现HandlerExceptionResolver
    * 配置异常处理器
    ```
    <bean class="自定义异常处理类"/>
    ```
    * 编写异常页面






# JdbcTemplate基本使用
## JdbcTemplate开发步骤
1. 导入spring-jdbc和spring-tx坐标
2. 创建数据库表和实体
3. 创建JdbcTemplate对象
4. 执行数据库操作

## JdbcTemplate常规操作
* 更新操作:jdbcTemplate.update (sql, params)
* 查询操作:
jdbcTemplate.query(sq1,Mapper,params)
jdbcTemplate.queryForobject(sq1,Mapper,params)


# Spring的事务控制
## 事务传播行为
* REQUIRED:如果当前没有事务,就新建一个事务, 如果已经存在一个事务中， 加入到这个事务中。一般的选择(默认值)
* SUPPORTS:支持当前事务,如果当前没有事务,就以非事务方式执行(没有事务)
* MANDATORY:使用当前的事務,如果当前没有事务,就抛出异常
* REQUERS_NEV:新建事务,如果当前在事务中，把当前事务挂起。
* NOT_SUPPORTED:以非事务方式执行操作,如果当前存在事务，就把当前事务挂起
* NEVER:以非事务方式运行，如果当前存在事務，抛出异常
* NESTED:如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行REQUIRED类似的操作
* 超时时间:默认值是-1,没有超时限制。如果有，以秒为单位进行设置
* 是否只读:建议查询时设置为只读

### 基于XML的声明式事务控制
1. 什么是声明式事务控制
Spring的声明式事务顾名思义就是采用声明的方式来处理事务。这里所说的声明，就是指在配置文件中声明用在Spring配置文件中声明式的处理事务来代替代码式的处理事务。
2. 声明式事务处理的作用
* 事务管理不侵入开发的组件。 具体来说，业务逻辑对象就不会意识到正在事务管理之中，事实上也应该如此，因为事务管理是属于系统层面的服务,而不是业务逻辑的一部分,如果想要改变事务管理策划的话,也只需要在定义文件中重新配置即可
* 在不需要事务管理的时候，只要在设定文件上修改一下, 即可移去事务管理服务,无需改变代码重新编译，这样维护起来极其方便


### 步骤
1. 需要tx命名空间
2. ```<!--    jdbc my什么东东都用这个类-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
<!--    事务的增强-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="事务方法" 事务属性.../>
        </tx:attributes>
    </tx:advice>
<!--    配置事务的织入-->
    <aop:config>
        <aop:advisor advice-ref="txAdvice" pointcut=""></aop:advisor>
    </aop:config>```
    其中，<tx:method> 代表切点方法的事务参数的配置,例如:
    <tx :method name= =" transfer" isolation="REPEATABLE_ READ ”propagation="REQUIRED" timeout= "-1”
    read-only="false"/>
    ●name:切点方法名称
    ●isolation:事务的隔离级别
    ●propogation: 事务的传播行为
    ●timeout; 超时时间
    ●read-only:是否只读
### 基于注解的声明式事务控制
@Transactional(属性...)