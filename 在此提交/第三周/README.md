# Mybatis
## Mybatis开发步骤
MyBatis开发步骤:
1. 添加MyBatis的坐标
2. 创建user数据表
3. 编写User实体类
4. 编写映射文件UserMapper.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="userMapper">
    <select id="findAll" resultType="com.example.MybatisTest.domain.User">
        select * from users
    </select>
</mapper>
```
5. 编写核心文件SqlMapConfig.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 这里写配置内容 -->
<!--    数据源环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://127.0.0.1:3306/user?serverTimezone=UTC&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="59192584"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="com.example.MybatisTest.mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```
6. 编写测试类
```
//        获得核心配置文件
        InputStream resourceAsStream = Resources.getResourceAsStream("mybatis.xml");
//        获得session工厂对象
        SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsStream);
//        获得Session回话对象
        SqlSession sqlSession = build.openSession();
//        执行操作   参数：命名空间+id
        List<User> users = sqlSession.selectList("userMapper.findAll");
        System.out.println(users);
        sqlSession.close();
```

## Mybatis映射文件

```
<?xml version="1.0" encoding="UTF-8"?>

<mapper namespace="userMapper">
    <select id="findAll" resultType="com.example.MybatisTest.domain.User">
        select * from users
    </select>
</mapper>
```
1. 映射文件DTD约束头
```
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
```
2. 命名空间
```
namespace="userMapper"
```
3. <seletc></select>操作可选
4. select * from users查询语句
5. resultType="com.example.MybatisTest.domain.User"查询结果对应的实体类型
## Mybatis插入操作
1. 插入语句使用insert标签
```
<insert id="save" parameterType="com.example.MybatisTest.domain.User">
    insert into users value (#{id},#{username},#{password})
</insert>
```
2. 在映射文件中使用parameterType属性指定要插入的数据类型
3. Sq|语句中使用#{实体属性名}方式引用实体中的属性值
4. 插入操作使用的API是sqlSession.insert(“ 命名空间.id"，实体对象);
5. 插入操作涉及数据库数据变化，所以要使用sqISession对象 显示的提交事务,即sqlSession.commit()
## Mybatis修改数据操作
1. 添加数据
```
<update id="update" parameterType="com.example.MybatisTest.domain.User">
        update users set username = #{username} ,password =#{password},id =#{id} where id =#{id}
    </update>
```
2. 删除
```
<delete id="delete" parameterType="java.lang.Integer">
        delete from users where id = #{id}
    </delete>
```

## Mybatis核心配置文件
* configuration配置
    * properties 属性
    * settings 设置
    * typeAliases类型别名
    * typeHandlers 类型处理器
    * objectF actory对象工厂
    * plugins 插件
    * environments环境：default，默认环境的名称
        * environment环境变量：id，当前环境的名称
        * transactionManager事务管理器：type，事务管理类型
        * dataSource数据源：type,数据类型
    * databaseldProvider 数据库厂商标识
    * mappers映射器
1. environments标签
其中，务管理器(transactionManager) 类型有两种:
* JDBC:这个配置就是直接使用了JDBC的提交和回滚设置，它依赖于从数据源得到的连接来管理事务作用域。
* MANAGED:这个配置几乎没做什么。它从来不提交或回滚一个连接, 而是让容器来管理事务的整个生命周期(比如JEE
应用服务器的上下文)。默认情况下它会关闭连接，然而一些容器并不希望这样， 因此需要将closeConnection属性设置
为false来阻止它默认的关闭行为。
其中，数据源(dataSource) 类型有三种:
* UNPOOLED:这个数据源的实现只是每次被请求时打开和关闭连接。
* POOLED:这种数据源的实现利用“池”的概念将JDBC连接对象组织起来。
* JNDI:这个数据源的实现是为了能在如EJB或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置
一个JNDI上下文的引用。
2. mapper标签
该标签的作用是加载映射的，加载方式有如下几种: 
* 使用相对于类路径的资源引用， 例如:``` <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>```
* 使用完全限定资源定位符(URL) ，例如: ```<mapper url= "file://var/mappers/AuthorMapper.xml"/>```
* 使用映射器接口实现类的完全限定类名，例如:``` <mapper class= " org.mybatis.builder.AuthorMapper"/>```
* 将包内的映射器接口实现全部注册为映射器， 例如: ```<package name= "org.mybatis.builder"/>```
3. 数据源配置可以抽取到properties文件中
4. 可以给Java类定义别名
```
<typeAliases>
    <typeAlias type="类名" alias="别名"/>
</typeAliases>
```

## Mybatis相应api
1. SqlSession工厂构建器SqlSessionFactoryBuilder
常用API: SqISessionFactory build(InputStream inputStream)
通过加载mybatis的核心文件的输入流的形式构建一个SqlSessionFactory对象
其中，Resources 工具类,这个类在org.apache.ibatis.io包中。Resources 类帮助你从类路径下、文件系统或
一个web URL中加载资源文件。

## Mybatis的Dao层实现
* 代理开发方式
采用Mybatis的代理开发方式实现DAO层的开发，这种方式是我们后面进入企业的主流。
Mapper接口开发方法只需要程序员编写Mapper接口(相当于Dao接口)，由Mybatis 框架根据接口定义创建接
口的动态代理对象，代理对象的方法体同上边Dao接口实现类方法。
Mapper接口开发需要遵循以下规范:
1. Mapper.xm|文件中的namespace与mapper接口的全限定名相同
2. Mapper接口方法名和Mapper.xm|中定 义的每个statement的id相同
3. Mapper接口方法的输入参 数类型和mapper.xmI中定义的每个sql的parameterType的类型相同
4. Mapper接口方法的输出参数类型和mapper .xm|中定义的每个sql的resultType的类型相同

* 业务层代码
```
InputStream resourceAsStream = Resources.getResourceAsStream("mybatis.xml");
SqlSessionFactory build = new SqlSessionFactoryBuilder().build(resourceAsStream);
SqlSession sqlSession = build.openSession();
UserDao mapper = sqlSession.getMapper(UserDao.class);
List<User> all = mapper.findAll();
User byId = mapper.findById(1);
System.out.println(byId);
System.out.println(all);
```
* Mapper接口代码
```
public List<User> findAll() throws IOException;
public User findById(int id);
```

## mybatis映射文件深入
1. 动态sq|语句概述
Mybatis的映射文件中，前面我们的SQL都是比较简单的,有些时候业务逻辑复杂时,我们的SQL是动态变化的,
此时在前面的学习中我们的SQL就不能满足要求了。
* if标签
```
<select id="findByCondition" parameterType="com.example.MybatisTest.domain.User" resultType="com.example.MybatisTest.domain.User">
    select * from users
    <where>
        <if test="id!=0">
            id = #{id}
        </if>
        <if test="username!=null">
            and username = #{username}
        </if>
        <if test="password!=null">
            and password = #{password}
        </if>
    </where>
</select>
```
* foreach标签
```
<select id="findByIds" parameterType="list" resultType="com.example.MybatisTest.domain.User">

--         select * form users where id in(x,x,x)
    select * from users
    <where>
        <foreach collection="list" open="id in(" close=")" item="id" separator=",">
            #{id}
        </foreach>
    </where>
</select>
```
2. sql语句的抽取
```
<sql id="selectUser" >select * from users</sql>
```
引用
```
<include refid="selectUser"></include>
```
3. typeHandlers标签
无论是MyBatis在预处理语句(PreparedStatement) 中设置-个参数时,还是从结果集中取出一个值时，都会用
类型处理器将获取的值以合适的方式转换成Java类型。
你可以重写类型处理器或创建你自己的类型处理器来处理不支持的或非标准的类型。具体做法为:实现
org.apache.ibatis.type.TypeHandler接口，或继承一个很便利的类 org.apache.ibatis.type.BaseTypeHandler,然
后可以选择性地将它映射到一个JDBC类型。例如需求:一个Java中的Date数据类型,我想将之存到数据库的时候存成一
个1970年至今的毫秒数，取出来时转换成java的Date,即java的Date与数据库的varchar毫秒值之间转换。
开发步骤:
* 定义转换类继承类BaseTypeHandler<T>
* 覆盖4个未实现的方法，其中setNonNullParameter为java程序设置数据到数据库的回调方法，
getNullableResult为查询时mysql的字符串类型转换成java的Type类型的方法
* 在MyBatis核心配置文件中进行注册
* 测试转换是否正确
4. plugins标签
MyBatis可以使用第三方的插件来对功能进行扩展,分页助手PageHelper是将分页的复杂操作进行封装，
使用简单的方式即可获得分页的相关数据
开发步骤:
* 导入通用PageHelper的坐标
```
<!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.2.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.github.jsqlparser/jsqlparser -->
<dependency>
    <groupId>com.github.jsqlparser</groupId>
    <artifactId>jsqlparser</artifactId>
    <version>3.2</version>
</dependency>

```
* 在mybatis核心配置文件中配置PageHelper插件
```
<plugins>
    <plugin interceptor="dialect" >
        <property name="dialect" value="mysql"/>
    </plugin>
</plugins>
```
* 测试分页数据获取


## Mybatis多表查询
1. 一对一查询
* 一对一查询的模型
用户表和订单表的关系为，一个用户有多个订单, 一个订单只从属于个用户
一对一查询的需求:查询一个订单，与此同时查询出该订单所属的用户

# SSM框架整合
## 配置文件
* Spring配置文件: applicationContext.xml
* SprngMVC配置文件: spring-mvc.xml
* MyBatis映射文件: AccountMapper.xml
* MyBatis核心文件: sqlMapConfig.xml
* 数据库连接信息文件: jdbc.properties
* Web.xml文件: web.xml
* 日志文件: log4j.xml

## 环境配置步骤
### web.xml
1. Spring的监听器
```
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```
2. SpringMVC的前端控制器
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
    <url-pattern>/*</url-pattern>
</servlet-mapping>
```
3. 乱码过滤器
```
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```


### applicationContext.xml
1. 组件扫描，记得排除SpringMVC的Controller标签

### SpringMVC
1. 组件扫描
2. 配置MVC注解驱动
3. 内部资源视图解析器
4. 开放静态资源访问
```
<context:component-scan base-package="com.example.SSM.controller"/>
<bean id="resourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/pages"/>
    <property name="suffix" value=".jsp"/>
</bean>
<mvc:default-servlet-handler/>
```
# Springboot
## 概述
SpringBoot对上述Spring的缺点进行的改善和优化，基于约定优于配置的思想，可以让开发人员不必在配置与逻辑业务之间进行思维的切换，全身心的投入到逻辑业务的代码编写中，从而大大提高了开发的效率，一定程度上缩短了项目周期。
## 入门
1. 案例:需求
搭建SpringBoot工程,定义HelloController.hello0方法， 返回”Hello SpringBoot!"。
2. 案例:实现步骤
* 创建Maven项目
* 导入SpringBoot起步依赖
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>2.1.9.RELEASE</version>
</dependency>
```
* 定义Controller
* 编写引导类：SpringBoot的引导类,是项目入口,运行main方法就可以启动项目。注解：@SpringBootApplication
* 启动测试
## 起步依赖原理分析
1. ing-boot-starter-parent：定义了一套很好的版本搭配
2. spring-boot-starter-web：集成了需要的依赖，版本信息从父工程得到

## SpringBoot配置
### 配置文件分类
SpringBoot是基于约定的，所以很多配置都有默认值，但如果想使用自己的配置替换默认配置的话，就可以使用application.properties或者application.yml (application.yaml) 进行配置。
* SpringBoot提供了2种配置文件类型: properteis和yml/yaml 
* 默认配置文件名称: application
* 在同一级目录下优先级为: properties > yml > yaml

### yaml
YAML是一种直观的能够被电脑识别的的数据数据序列化格式， 并且容易被人类阅
读，容易和脚本语言交互的，可以被支持YAML库的不同的编程语言程序导入，比如: C/C+ +, Ruby, Python, Java, Perl, C#, PHP等。YML文件是以数据为核心的，比传统的xml方式更加简洁。

#### 基础语法
* 大小写敏感
* 数据值前边必须有空格,作为分隔符
* 使用缩进表示层级关系
* 缩进时不允许使用Tab键，只允许使用空格(各个系统Tab对应的空格数目可能不同，导致层次混乱)。
* 缩进的空格数目不重要，只要相同层级的元素左侧对齐即呵
* #表示注释，从这个字符一直到行尾，都会被解析器忽略。

#### 数据类型
* 对象map：键值对的集合
```
person:
  name: zhangsan
  age: 18
```
* 数组:一组按次序排列的值
```
adress:
  - xxx
  - xxx
adress:[xxx,xxx]
```
* 纯量：单个，不可再分的值
```
msg:'hello \n world' #单引号忽略转义字符
msg:"hello \n world" #双引号识别转义字符
```

### 读取文件配置内容

* @Value
```
@Value("${name}")
private String name;

@Value("${person.name}")
private String name1;

@Value("${person.age}")
private int age;

```
* Environment
需要注入对象
```
 @Autowired
private Environment environment;

System.out.println(environment.getProperty("person.age")

```
* @ConfigurationProperties
要添加前缀，不然可能会读取到相同属性名的内容
```
@Component
@ConfigurationProperties(prefix = "person")
```
* 可以添加spirng-boot-configuration-processor依赖，这样编写配置文件就会有提示信息
```
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-configuration-processor -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <version>2.5.3</version>
</dependency>
```

### profile
我们在开发Spring Boot应用时，通常同一套程序会被安装到不同环境,比如:开发、测试产等。中数据库地址、服务器端口等等配置都不同，如果每次打包时，都要修改配置文件,那么非常麻烦。profile功能就是来进行动态配置切换的。

1. profile配置方式
* 多profile文件方式
创建application-dev.properties,application-test.properties,application-pro.properties

spring:
  profiles:
    active: dev,这样配置在application.properties文件中就是激活dev
* yml多文档方式
2. profile激活方式
* 配置文件
* 虚拟机参数
* 命令行参数：windous powershell上运行
java -jar.\demo-0.0.1-SNAPSHOT.jar --spring.profiles.active=test
以后运行是通过jar包来运行程序的


### 内部配置加载顺序
Springboot程序启动时，会从以下位置加载配置文件:
1. file:..config/: 当前项目下的/config目录下
2. file:../ : 当前项目的根目录
3. classpath:/config/: classpath的/config目录
4. classpath:/ : classpath的根目录
加载顺序为上文的排列顺序，高优先级配置的属性会生效

### Springboot整合Junit
步骤：
1. 搭建SpringBoot工程
2. 引入starter-test起步依赖
3. 编写测试类
4. 添加测试相关注解
* @RunWith(SpringRunner.class)
* @SpringBootlsest(classes =启动类class)
5. 编写测试方法

### Springboot整合Mybatis
1. 搭建SpringBoot工程
2. 引入mybatis起步依赖，添加mysql驱动
3. 编写DataSource和MyBatis相关配置
4. 定义表和实体类
5. 编写dao和mapper文件/纯注解开发
6. 测试


## Springboot原理分析
### Springboot自动配置

### Condition
Condition是在Spring 4.0增加的条件判断功能，通过这个可以功能可以实现选择性的创建Bean操作。
```
@SpringBootApplication

public class DemoApplication {

    public static void main(String[] args) {
        //获取Spring的IOC容器
        ConfigurableApplicationContext context = SpringApplication.run(DemoApplication.class,args);
    }

}


@Bean("user")
    @Conditional(ClassCondition.class)
    public User user()
    {

        return new User();
    }



//返回true就能创建，flase则不能
public class ClassCondition implements Condition {
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        return true;
    }
}
```

### 切换内置服务器
SpringBoot的web环境中默认使用tomcat作为内置服务器，实SpringBoot提供了4中内置服务器供我们选择,我们可以很方便的进行切换。


### @Enable*注解
SpringBoot中提供了很多Enable开头的注解,这些注解都是用于动态启用某些功能的。其底层原理是使用@Import注解导入一些配置类，实现Bean的动态加载。


### @Import注解
@Enable*底层依赖于@Import注解导入-类,使用@lmport导入的类会被Spring加载到IOC容器中。而@Import提供4中用
法:
1. 导入Bean
2. 导入配置类
3. 导入ImportSelector实现类。一般用于加载配置文件中的类
4. 导入ImportBeanDefinitionRegistrar实现类。

## Springboot事件监听
### Java监听机制
SpringBoot的监听机制，实是对Java提供的事件监听机制的封装。
Java中的事件监听机制定义了以吓几个角色: .
1. 事件: Event, 继承java.util.EyentObject类的对象
2. 事件源: Source, 任意对象Object
3. 监听器: Listener, 实现java.util.EventListener接口的对象

### SpringBoot监听机制
SpringBoot在项目启动时，会对几个监听器进行回调，我们可以实现这些监听器接口，项目启动时完成
一些操作。
ApplicationContextlnitializer、SpringApplicationRunListener. CommandLineRunner、 ApplicationRunner
可以利用这个来在启动项目时读取数据库内容，缓存预热

### Springboot监控
SpringBoot自带监控功能Actuator下可以帮助实现对程序内部运行情况监控,比如监控状况、Bean加载情况、 配置属性、日志信息等。




