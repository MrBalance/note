# Spring 学习

## Spring特征

1. 轻量级

   * 完整spring框架大小只有1M多、且Spring处理所需的开销也是微不足道的
   * 非侵入式，Spring应用中的对象不依赖于Spring的特定类

2. 控制反转

   * 通过控制反转IOC（Spring 容器控制对象生命周期和对象之间的关系），降低耦合
   * 应用IOC一个对象依赖的其他对象会通过被动的方式传递进来，而不是对象自己创建和查找依赖

3. 面向切面

   支持面向切面编程，将业务逻辑和系统服务分开

4. 容器

   容器管理对象的配置和生命周期

5. 框架

   * 简单的组件配置、组合成复杂的应用
   * 应用对象被声明式的组合
   * 提供基础功能、应用逻辑开发留给开发者

   ==ps：==

   控制反转IOC（目的）     依赖注入（手段）

   谁控制谁？IOC容器控制bean

   控制什么？控制bean的生命周期

   那些方面反转？对象实例化的权力

   为什么要反转？

   ### Spring 常用注解

   @Controller

   @ResController

   @Component

   @Repository

   @Service

   @ResponseBody

   @RequestMapping

   @Autowired

   @PathVarlabe

   @RequestParam

   @RequestHeader

## Spring容器高层视图

Spring启动读取bean配置信息，并将bean信息初始化生成bean注册表，并根据注册表初始化Bean实例，装配好bean的依赖关系，为上层提供准备就绪的运行环境。其中bean缓存池使用HashMap实现

## SpringBean的作用域（scope配置）

1. Singleton（单例）：容器中只存在一个共享的bean实例，线程不安全，默认值
2. prototype（原型）：每次获取都会创建一个新的bean实例，每个bean都有自己的属性和状态（对有状态的bean使用prototype，对无状态的bean使用singleton）
3. request：一次request请求中，同一个bean，不同request请求使用不同的bean，当前http request有效，request结束销毁
4. session：一次http session中，同一个bean，不同http session中不同bean，当前session中有效，结束销毁
5. globle session：全局
6. 的http session 中，同一个bean，仅在使用portlet context时有效

## Spring bean生命周期

大体上分为四个阶段：实例化、属性赋值、初始化、销毁

1. 实例化：实例化一个bean对象（new）

2. 属性赋值：设置对象相关属性和依赖，IOC注入

3. 初始化：

   1. 初始化前：

      * 检查BeanNameAware接口，通过setBeanName设置Bean的id
      * 检查BeanFactoryAware接口，通过setBeanFactory传递Spring工厂自身（最简单容器功能，实例化对象和取对象）

   2. 初始化：

      * 检查ApplicationContextAware接口，通过setApplicationContext传入Spring上下文（比BeanFactory有更多实现方法，国际化、访问资源url和文件、载入多个上下文、发送消息和响应机制、AOP）
      * 检查BeanPostProcessor接口，调用postProcessBeforeInitialization方法，Bean内容修改，发生在初始化结束的时候，应用于内存或缓存技术

   3. 初始化后：

      检查init-method属性，调用配置的初始化方法

4. 销毁：

   1. 使用前：
      检查BeanPostProcessor，调用postProcessorAfterInitialization方法，注册相关回调接口
   2. 使用后：
      * 如果Bean不再需要，调用DisposableBean接口，调用destroy方法
      * 检查destroy-method属性，调用配置的销毁方法

   ## Spring依赖注入的四种方法

   1. 构造器注入
   2. setter方法注入
   3. 静态工厂注入（new 工厂）
   4. 实例工厂（工厂类中获得实例）

   ## Spring五种自动装配

   1. 默认，显示的ref属性装配
   2. byName
   3. byType（多个bean符合条件，抛出错误）
   4. constructor（需要提供构造器参数，没有确定参数类型会抛异常）
   5. autodetect：首先尝试constructor，无法工作则用byType



## SpringAOP

面向切面编程

主要应用场景：

权限

缓存

内容传递

错误处理

懒加载

调试

记录跟踪，优化，校准

性能优化

持久化

资源池

同步

事务

### AOP两种代理方式

1. jdk动态代理：反射、接口
2. GCLib动态代理：继承

## SpringMVC

### 理解：

Spring MVC是一个基于MVC架构的用来简化web应用程序开发的应用开发框架，它是Spring的一个模块,无需中间整合层来整合，通过把Model，View，Controller分离，把较为复杂的web应用分成逻辑清晰的几部分，简化开发，减少出错，方便组内开发人员之间的配合

### 原理：

1. 用户发送请求到DispatcherServlet
2. DispatcherServlet通过查询一个或多个HandleMapping找到对应的Handle(Controller)（HttpRequestHandler、Controller接口）
3. 解析到Handle后交给HandleAdapter适配器处理
4. HandleAdapter找到真正的处理逻辑去处理
5. handlerAdapter处理完后返回一个ModelAndView，Model是数据对象，View是逻辑上的视图
6. ViewResolver根据逻辑View找到真正的view
7. DispatcherServlet将Model传给View，然后返回浏览器一个View

### 优点：

1. 基于组件，无论对象、控制器、视图、还是业务对象都是基于java组件，和Spring紧密结合
2. 不依赖ServletAPI（实现中依赖，应用层面不依赖）
3. 可以使用任意视图技术，你仅限于jsp
4. 支持各种请求资源的映射策略
5. 易于扩展

## 六大主要组件

1. DispatcherServlet（前端控制器）接受、响应请求，相当于转发器，减少其他组件的耦合度
2. HandlerMapping（处理器映射器）根据URL查找Handler
3. HandlerAdapter（处理器适配器）
4. Handler（处理器，需要程序员开发，也就是常常说的controller）
5. ViewResolver（视图解析器）根据逻辑名解析成真正的视图
6. View（视图，需要程序员开发，如jsp）View是一个接口，它的实现类支持不同类型（jsp、freemarker、pdf等）

==ps：==

转发（foward）和重定向（redirect）区别

| 区别               | 转发（foward）   | 重定向（redirect） |
| ------------------ | ---------------- | ------------------ |
| **根目录**         | 包含项目访问地址 | 没有项目访问地址   |
| **地址栏**         | 不会发生变化     | 会发生变化         |
| **哪里跳转**       | 服务器端进行跳转 | 浏览器端进行跳转   |
| **请求域中的数据** | 不会丢失         | 会丢失             |

## JPA原理

### 事务

原子性、一致性、隔离性、持久性

### 分布式事务

事务管理器TansactionManager和资源管理器ResourceManager

两阶段提交，第一阶段：准备阶段、第二阶段：提交阶段

## MyBatis

### 缓存

一级缓存，SqlSession级别，不能关闭，同一个SqlSession查询相同语句时调用，最多存储1024条sql

二级缓存，Mapping级别，可以跨SqlSession共享，需要配置

### 标签

#### 一、定义SQL语句

属性介绍：
	id ：唯一的标识符
	parameterType：传给此语句的参数的全路径名或别名 例:com.test.poso.User

```xml
<!--  select 标签  -->
<select id="userList" parameterType="user" resultType="User">
	select * from user where name =#{name}
</select>
<!--  insert 标签   -->
<!--  delete 标签   -->
<delete id="deleteUser" parameterType="int"> 
	delete from user where id = #{id} 
</delete>
<!--  update 标签   -->
```

#### 二、配置对象属性与查询结果集

标签说明：
主标签
id：该resultMap的标志
type：返回值的类名，此例中返回EStudnet类
子标签：

id：用于设置主键字段与领域模型属性的映射关系，此处主键为ID，对应id。
result：用于设置普通字段与领域模型属性的映射关系

```xml
<!--  resultMap 标签的使用   -->
<resultMap id="getStudentRM" type="EStudnet">
  <id property="id" column="ID"/>
  <result property="studentName" column="Name"/>
  <result property="studentAge" column="Age"/>
</resultMap>
<select id="getStudent" resultMap="getStudentRM">
  SELECT ID, Name, Age
    FROM TStudent
</select>
```

#### 三、动态拼接SQL

(1) if 标签

```xml
<select id=" getStudentListLikeName " parameterType="StudentEntity" resultMap="studentResultMap">     
    SELECT * from STUDENT_TBL ST      
    <if test="studentName!=null and studentName!='' ">     
        WHERE ST.STUDENT_NAME LIKE CONCAT(CONCAT('%', #{studentName}),'%')      
    </if>     
</select>
```

（2）foreach 标签

说明：

　　　　collection ：collection属性的值有三个分别是list、array、map三种，分别对应的参数类型为：List、数组、map集合，我在上面传的参数为数组，所以值为array

　　　　item ： 表示在迭代过程中每一个元素的别名

　　　　index ：表示在迭代过程中每次迭代到的位置（下标）

　　　　open ：前缀

　　　　close ：后缀

　　　　separator ：分隔符，表示迭代时每个元素之间以什么分隔

```xml
<delete id="deleteBatch"> 
　　　　delete from user where id in
　　　　<foreach collection="array" item="id" index="index" open="(" close=")" separator=",">
　　　　　　#{id}
　　　　</foreach>
　　</delete>
```

（3）choose标签

```xml
<select id="getStudentListChooseEntity" parameterType="StudentEntity" resultMap="studentResultMap">     
    SELECT * from STUDENT_TBL ST      
    <where>     
        <choose>     
            <when test="studentName!=null and studentName!='' ">     
                    ST.STUDENT_NAME LIKE CONCAT(CONCAT('%', #{studentName}),'%')      
            </when>     
            <when test="studentSex!= null and studentSex!= '' ">     
                    AND ST.STUDENT_SEX = #{studentSex}      
            </when>     
            <when test="studentBirthday!=null">     
                AND ST.STUDENT_BIRTHDAY = #{studentBirthday}      
            </when>     
            <when test="classEntity!=null and classEntity.classID !=null and classEntity.classID!='' ">     
                AND ST.CLASS_ID = #{classEntity.classID}      
            </when>     
            <otherwise>     
                      
            </otherwise>     
        </choose>     
    </where>     
</select>
```

#### 四、格式化输出

（1）where

如果标签返回的内容是以AND 或OR 开头的，则它会剔除掉

```xml
<!-- 查询学生list，like姓名，=性别 -->     
<select id="getStudentListWhere" parameterType="StudentEntity" resultMap="studentResultMap">     
    SELECT * from STUDENT_TBL ST      
    <where>     
        <if test="studentName!=null and studentName!='' ">     
            ST.STUDENT_NAME LIKE CONCAT(CONCAT('%', #{studentName}),'%')      
        </if>     
        <if test="studentSex!= null and studentSex!= '' ">     
            AND ST.STUDENT_SEX = #{studentSex}      
        </if>     
    </where>     
</select>
```

（2）set

使用set+if标签修改后，如果某项为null则不进行更新，而是保持数据库原值

```xml
<!-- 更新学生信息 -->     
<update id="updateStudent" parameterType="StudentEntity">     
    UPDATE STUDENT_TBL      
    <set>     
        <if test="studentName!=null and studentName!='' ">     
            STUDENT_TBL.STUDENT_NAME = #{studentName},      
        </if>     
        <if test="studentSex!=null and studentSex!='' ">     
            STUDENT_TBL.STUDENT_SEX = #{studentSex},      
        </if>     
        <if test="studentBirthday!=null ">     
            STUDENT_TBL.STUDENT_BIRTHDAY = #{studentBirthday},      
        </if>     
        <if test="classEntity!=null and classEntity.classID!=null and classEntity.classID!='' ">     
            STUDENT_TBL.CLASS_ID = #{classEntity.classID}      
        </if>     
    </set>     
    WHERE STUDENT_TBL.STUDENT_ID = #{studentID};      
</update>
```

（3）trim

 trim是更灵活的去处多余关键字的标签，他可以实践where和set的效果。

```xml
<!-- 查询学生list，like姓名，=性别 -->     
<select id="getStudentListWhere" parameterType="StudentEntity" resultMap="studentResultMap">     
    SELECT * from STUDENT_TBL ST      
    <trim prefix="WHERE" prefixOverrides="AND|OR">     
        <if test="studentName!=null and studentName!='' ">     
            ST.STUDENT_NAME LIKE CONCAT(CONCAT('%', #{studentName}),'%')      
        </if>     
        <if test="studentSex!= null and studentSex!= '' ">     
            AND ST.STUDENT_SEX = #{studentSex}      
        </if>     
    </trim>     
</select>
```

```xml
<!-- 更新学生信息 -->     
<update id="updateStudent" parameterType="StudentEntity">     
    UPDATE STUDENT_TBL      
    <trim prefix="SET" suffixOverrides=",">     
        <if test="studentName!=null and studentName!='' ">     
            STUDENT_TBL.STUDENT_NAME = #{studentName},      
        </if>     
        <if test="studentSex!=null and studentSex!='' ">     
            STUDENT_TBL.STUDENT_SEX = #{studentSex},      
        </if>     
        <if test="studentBirthday!=null ">     
            STUDENT_TBL.STUDENT_BIRTHDAY = #{studentBirthday},      
        </if>     
        <if test="classEntity!=null and classEntity.classID!=null and classEntity.classID!='' ">     
            STUDENT_TBL.CLASS_ID = #{classEntity.classID}      
        </if>     
    </trim>     
    WHERE STUDENT_TBL.STUDENT_ID = #{studentID};      
</update>
```

#### 五、配置关系

（1）association

一对一

```xml
<resultMap id="userResultMap" type="test.mybatis.entity.User">
  <id column="id" property="id" jdbcType="VARCHAR" javaType="java.lang.String"/>
  <result column="userName" property="userName" jdbcType="VARCHAR" javaType="java.lang.String"/>
<!-- 这里把user的id传过去 --> 
   <association property="article" column="id"                       
            select="test.mybatis.dao.articleMapper.selectArticleByUserId" />
    <!-- test.mybatis.dao.articleMapper为命名空间 -->
 </resultMap>
```

(2)collection

一对多

```xml
<resultMap id="userResultMap" type="test.mybatis.entity.User">
  <id column="id" property="id" jdbcType="VARCHAR" javaType="java.lang.String"/>
  <result column="userName" property="userName" jdbcType="VARCHAR" javaType="java.lang.String"/>
<!-- 这里把user的id传过去 -->  
   <collection property="articleList" column="id"                       
            select="test.mybatis.dao.articleMapper.selectArticleListByUserId" />
 </resultMap>
```

#### 六、SQL标签

更多用于写sql语句的一部分，写在配置文件中的常量

#### 七、include标签

用于引用常量

### #{}和${}区别

#相当于对数据 加上 双引号，$相当于直接显示数据。

$方式一般用于传入数据库对象，例如传入表名

MyBatis排序时使用order by 动态参数时需要注意，用$而不是#！ 