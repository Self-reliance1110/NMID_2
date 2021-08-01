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