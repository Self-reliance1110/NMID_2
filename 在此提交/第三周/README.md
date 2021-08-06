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
* 使用相对于类路径的资源引用， 例如: <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
* 使用完全限定资源定位符(URL) ，例如: <mapper url= "file://var/mappers/AuthorMapper.xml"/>
* 使用映射器接口实现类的完全限定类名，例如: <mapper class= " org.mybatis.builder.AuthorMapper"/>
* 将包内的映射器接口实现全部注册为映射器， 例如: <package name= "org.mybatis.builder"/>

