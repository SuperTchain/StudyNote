MyBatis-Plus（简称 MP）是一个 MyBatis 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415092210303.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0x6eTQxMDk5Mg==,size_16,color_FFFFFF,t_70)

## 基本特性

- **无侵入：** 只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小：** 启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作：** 内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用：** 通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成：** 支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 ActiveRecord 模式：** 支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作：** 支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器：** 采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
- **内置分页插件：** 基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库：** 支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件：** 可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件：** 提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作

## 快速入门

**1、创建数据库 mybatis\_plus**

现有一张 User 表，其表结构如下：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415093038886.png)

其对应的数据库 Schema 脚本，数据库 Data 脚本如下：

```sql
DROP TABLE IF EXISTS user;

CREATE TABLE user
(
	id BIGINT(20) NOT NULL COMMENT '主键ID',
	name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
	age INT(11) NULL DEFAULT NULL COMMENT '年龄',
	email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (id)
);
-- 真实开发中，version（乐观锁），deleted（逻辑删除）、gmt_create、gem_mo


DELETE FROM user;

INSERT INTO user (id, name, age, email) VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');

```

**2、新建Maven工程**  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415093819123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0x6eTQxMDk5Mg==,size_16,color_FFFFFF,t_70)

**2、导入相关依赖**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.4.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>com.ly</groupId>
    <artifactId>mybatis-plus</artifactId>
    <version>1.0-SNAPSHOT</version>
    <name>mybatis-plus</name>
    <description>Demo project for Spring Boot</description>
    
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>

        <!--mybatis-plus 是自己开发的，非官方的！-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.3.1.tmp</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

说明：我们使用mybatis-plus可以节省我们大量的代码，尽量不要同时导入mybatis和mybatis-plus！ 版本的差异！

**3、配置文件application.yml**

```yml
spring:
  #配置数据源
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatis_plus?useSSL=false&serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
    username: root
    password: 123456

#配置日志，我们所用的sql现在是不可见的，我们希望知道他是怎么执行的，所以我们必须要查看日志！
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

**4、编写代码**

**mybatis-plus的使用步骤：** 引入依赖 \-> 创建pojo \-> 实现dao接口（不用写mapper.xml文件，只需要在启动器上配置 mapper 扫描路径即可）-> 基本使用

**编写实体类 User.java，这里使用了[Lombok插件](https://blog.csdn.net/Lzy410992/article/details/110725037)**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

**编写Mapper类 UserMapper.java**

```java
//在对应的Mapper上继承基本的类baseMapper
public interface UserMapper extends BaseMapper<User> {
    //所有的CRUD已经编写完成
    //不需要像以前的配置一些xml

}
```

**新建 Spring Boot 启动类中添加 \@MapperScan 注解，扫描 Mapper 文件夹：**

```java
@SpringBootApplication
@MapperScan("com.ly.mapper")//扫描mapper文件夹
public class MybatisPlusApplication {

    public static void main(String[] args) {
        SpringApplication.run(MybatisPlusApplication.class, args);
    }

}
```

**工程目录：**  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415100932124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0x6eTQxMDk5Mg==,size_16,color_FFFFFF,t_70)

**添加测试类，进行功能测试：**

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class MybatisPlusTest {

    //继承了BaseMapper，所有方法都来自父类
    //我们可以编写自己的拓展方法
    @Autowired
    private UserMapper userMapper;

    @Test
    public void testSelect() {
        System.out.println(("----- selectAll method test ------"));
        //参数是一个Wrapper，条件结构器，这里先不用 填null
        //查询所有的用户 
        List<User> userList = userMapper.selectList(null);
        userList.forEach(System.out::println);
    }

}
```

UserMapper 中的 selectList\(\) 方法的参数为 MP 内置的条件封装器 Wrapper，所以不填写就是无任何条件

**控制台输出：**  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415104540909.png)

## 实现CRUD

#### 插入操作：

> **插入一条记录:** **int insert\(T entity\);**  
> **插入类型T：实体对象**

**举例测试：**

```java
@Test
public void testInsert() {

    User user = new User();
    user.setName("LY");
    user.setAge(100);
    user.setEmail("123@qq.com");

    int result = userMapper.insert(user);  //帮我们生成id
    System.out.println(result);			   //受影响的行数
    System.out.println(user); 			   //发现id会自动回填
}
```

运行结果：mybatis-plus实现了主键自动生成  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021041511094877.png)  
**主键生成策略：**

数据库插入的id为全局默认的id（ID\_WORKER\)，我们需要配置主键自增，在实体类字段上添加注解： `@TableId(type =IdType.AUTO)`，数据库字段一定要是自增的。

```java
public enum IdType {
    AUTO(0),   //数据可id自增
    NONE(1),   //未设置主键
    INPUT(2),   //手动输入
    ID_WORKER(3), //默认的全局唯一id
    UUID(4),  //全局唯一id uuid
    ID_WORKER_STR(5); // ID_WORKEK 字符串表示法

    private int key;

    private IdType(int key) {
        this.key = key;
    }

    public int getKey() {
        return this.key;
    }
}
```

添加过注解，并将数据库id字段设置为自增后，测试插入数据：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415112725816.png)

> **雪花算法：**  
> SnowFlake 算法，是 Twitter 开源的分布式 id 生成算法。其核心思想就是：使用一个 64 bit 的 long 型的数字作为全局唯一 id。在分布式系统中的应用十分广泛，且ID 引入了时间戳，基本上保持自增的。  
> 这 64 个 bit 中，其中 1 个 bit 是不用的，然后用其中的 41 bit 作为毫秒数，用 10 bit 作为工作机器 id，12 bit 作为序列号。

#### 更新操作

```java
// 根据 whereEntity 条件，更新记录
int update(@Param(Constants.ENTITY) T entity, @Param(Constants.WRAPPER) Wrapper<T> updateWrapper);
// 根据 ID 修改
int updateById(@Param(Constants.ENTITY) T entity);


#参数说明
类型	    参数名	        描述
T	    entity	        实体对象 (set 条件值,可为 null)
Wrapper	updateWrapper	实体对象封装操作类（可以为 null,里面的 entity 用于生成 where 语句）
```

举例测试：

```java
@Test
public void testUpdate() {
    //sql自动动态配置
    User user = new User();
    user.setName=("ZZ");
    user.setId(3L);
    user.setAge("50");
    
    //注意：updateById的参数是一个对象
    userMapper.updateById(user);
}
```

运行结果：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415113902311.png)

## 自动填充

创建时间、修改时间！这些个操作一般都是自动化完成，我们不希望手动更新！

阿里巴巴开发手册：所有的数据库表：gmt\_create\\gmt\_modified几乎所有的表都要配置上！而且需要自动化

**方式一：数据库级别** \(工作中不允许\)

在表中新增字段 create\_time 、update\_time，设为默认CURRENT\_TIMESIAMP

**方式二：代码级别**

在表中新增字段 create\_time 、update\_time：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415115310598.png)

 实体类上的属性需要增加注解 \@TableField

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    @TableId(type = IdType.AUTO)
    private Long id;
    private String name;
    private Integer age;
    private String email;
    
	//创建时间,插入数据时操作
	@TableField(fill = FieldFill.INSERT)
	private Date createTime;	
	//更新时间，插入和更新是操作
	@TableField(fill = FieldFill.INSERT_UPDATE)
	private Date updateTime;
}
```

 编写处理器来处理这个注释：

```java
@Slf4j
@Component //不要忘记吧处理器加到IOC容器中
public class MyMetaObjectHandler implements MetaObjectHandler {

    //插入时候的填充策略
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("start insert fill ....");  //日志
        //设置字段的值（String fieldName字段名,Object fieldVal要传递的值,MetaObject metaObject)
        this.setFieldValByName("createTime",new Date(),metaObject);
        this.setFieldValByName("updateTime",new Date(),metaObject);
        
    }

    //更新时间的填充策略
    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("start update fill ....");
        this.setFieldValByName("updateTime",new Date(),metaObject);
        
    }
}
```

测试插入一条数据：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415120720520.png)  
数据库中的数据：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415120638867.png)

## 乐观锁

乐观锁：顾名思义十分乐观，它总是被认为不会出现问题，无论干什么都不去上锁！如果出现了问题，再次更新测试

悲观锁：顾名思义十分悲观，它总是出现问题，无论干什么都会上锁！再去操作！

**乐观锁实现方式：**

 -    取出记录时，获取当前version
 -    更新时，带上这个version
 -    执行更新事，set version=newVersion where version =oldVersion
 -    如果version不对，就更新失败

```sql
乐观锁： 1、先查询，获得版本号 version=1
--A
update user set name ="shuishui" ,version =version+1
where id =2 and version=1

--B 如果线程抢先完成，这个时候version=2，会导致A修改失败
update user set name ="shuishui" ,version =version+1
where id =2 and version=1
```

**测试乐观锁：**

**1、表中创建乐观锁字段version 默认值为1**  
**2、同步实体类**

```java
@Version //乐观锁Version注解
private Integer version;
```

**3、注册组件 （config包下）**

```java
@EnableTransactionManagement
@MapperScan("com.ly.mapper")
@Configuration//配置类
public class MyBatisPlusConfig{
    //注册乐观锁插件
    @Bean
	public OptimisticLockerInterceptor optimisticLockerInterceptor() {
    	return new OptimisticLockerInterceptor();
	}
}
```

测试乐观锁成功：

```java
//测试乐观锁成功
@Test
public void testOptimisticLocker(){
    //1、查询用户信息
    User user = userMapper.selectById(1L);
    //2、修改用户信息
    user.setName("LZY");
    user.setEmail("222@qq.com");
    //执行更新操作
    userMapper.updateById(user);
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415150019965.png)

模拟乐观锁失败：

```java
//测试乐观锁失败
@Test
public void testOptimisticLocker2(){
    //1、查询用户信息
    User user = userMapper.selectById(1L);
    //2、修改用户信息
    user.setName("LZY1");
    user.setEmail("111@qq.com");

    //1、模拟另一个线程执行了插队操作
    User user2 = userMapper.selectById(1L);
    //2、修改用户信息
    user2.setName("LZY2");
    user2.setEmail("222@qq.com");

    //执行更新操作
    userMapper.updateById(user2);

    //执行更新操作
    userMapper.updateById(user); //如果没有乐观锁就会覆盖插队线程的值
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415150810854.png)

## 查询操作

**根据 ID 查询：** **T selectById\(Serializable id\);**

**根据 entity 条件，查询一条记录：** **T selectOne\(\@Param\(Constants.WRAPPER\) Wrapper queryWrapper\);**

**查询（根据ID 批量查询）：** **List selectBatchIds\(\@Param\(Constants.COLLECTION\) Collection\<\? extends Serializable> idList\);**

**根据 entity 条件，查询全部记录：** **List selectList\(\@Param\(Constants.WRAPPER\) Wrapper queryWrapper\);**

**查询（根据 columnMap 条件）：** **List selectByMap\(\@Param\(Constants.COLUMN\_MAP\) Map\<String, Object> columnMap\);**

**根据 Wrapper 条件，查询全部记录：** **List\<Map\<String, Object>> selectMaps\(\@Param\(Constants.WRAPPER\) Wrapper queryWrapper\);**

**根据 Wrapper 条件，查询全部记录。注意： 只返回第一个字段的值：** **List selectObjs\(\@Param\(Constants.WRAPPER\) Wrapper queryWrapper\);**

**根据 entity 条件，查询全部记录（并翻页）：** **IPage selectPage\(IPage page, \@Param\(Constants.WRAPPER\) Wrapper queryWrapper\);**

**根据 Wrapper 条件，查询全部记录（并翻页）：** **IPage\<Map\<String, Object>> selectMapsPage\(IPage page, \@Param\(Constants.WRAPPER\) Wrapper queryWrapper\);**

**根据 Wrapper 条件，查询总记录数：** **Integer selectCount\(\@Param\(Constants.WRAPPER\) Wrapper queryWrapper\);**

**参数类型说明：**

- **Serializable： 主键ID**
- **Wrapper ：实体对象封装操作类（可以为 null）**
- **Collection\<\? extends Serializable>：主键ID列表\(不能为 null 以及 empty\)**
- **Map\<String, Object>： 表字段 map 对象**
- **IPage：分页查询条件（可以为 RowBounds.DEFAULT）**

**实例测试：**

```java
@Test
public void testSelectById(){
    User user =userMapper.selectById(1);
    System.out.println(user);
}

//测试批量查询
@Test
public void testSelectByBatchId(){
    List<User> users =userMapper.selectBatchIds(Arrays.asList(1,2,3));
    users.forEach(System.out::println);
}

//条件查询
public void testSelectByBatchIds(){
    HashMap<String,Object> map=new HashMap<>();
    //自定义查询
    map.put("name","LZY");
    map.put("age",18);

    List<User> users = userMapper.selectByMap(map);
    users.forEach(System.out::println);
}
```

## 分页查询

Mybatis-Plus中内置了分页插件，配置拦截器组件即可：

```java
@EnableTransactionManagement
@Configuration
@MapperScan("com.ly.mapper")
public class MybatisPlusConfig {

    @Bean
    public PaginationInterceptor paginationInterceptor() {
        PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
        // 设置请求的页面大于最大页后操作， true调回到首页，false 继续请求  默认false
        // paginationInterceptor.setOverflow(false);
        // 设置最大单页限制数量，默认 500 条，-1 不受限制
        // paginationInterceptor.setLimit(500);
        // 开启 count 的 join 优化,只针对部分 left join
        paginationInterceptor.setCountSqlParser(new JsqlParserCountOptimize(true));
        return paginationInterceptor;
    }
}
```

测试分页：

```java
//测试分页查询
@Test
public void testPage(){
    // 参数一：当前页
    // 参数二：页面大小
    // 使用了分页插件之后，所有的分页操作也变得简单了
    Page<User> page =new Page<>(2,5);
    userMapper.selectPage(page,null);

    page.getRecords().forEach(System.out::println);
    //获取总数
    page.getTotal();
}
```

## 删除操作

**根据 entity 条件，删除记录：** **int delete\(\@Param\(Constants.WRAPPER\) Wrapper wrapper\);**

**删除（根据ID 批量删除）：** **int deleteBatchIds\(\@Param\(Constants.COLLECTION\) Collection\<\? extends Serializable> idList\);**

**根据 ID 删除：** **int deleteById\(Serializable id\);**

**根据 columnMap 条件，删除记录：** **int deleteByMap\(\@Param\(Constants.COLUMN\_MAP\) Map\<String, Object> columnMap\);**

**参数类型说明：**

- **Wrapper：实体对象封装操作类（可以为 null）**
- **Collection\<\? extends Serializable>： 主键ID列表\(不能为 null 以及 empty\)**
- **Serializable：主键ID**
- **Map\<String, Object>： 表字段 map 对象**

测试删除：

```java
@Test
public void testDeleteById(){
    userMapper.deleteById(1);
}

//批量删除
@Test
public void testDeleteBatchId(){
    userMapper.deleteBatchIds(Arrays.asList(1,2));
}

//条件删除
@Test
public void testDeleteMap(){
    HashMap<String,Object> map = new HashMap<>();
    map.put("name","LZY");
    userMapper.deleteByMap(map);
}
```

## 逻辑删除

物理删除 ：从数据库中直接移出

逻辑删除：方便数据恢复和保护数据本身价值的一种方案，在数据库中没有被移出，而是通过一个变量来让他失效！deleted=0 ==>deleted =1\(失效\)

**在数据库中增加deleted字段：**  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210415154624268.png)  
**实体类中增加属性，并添加\@TableLogic注解：**

```java
@TableLogic  //3.1.1开始可以不用这一步，但如果实体类上有 @TableLogic 则以实体上的为准，忽略全局
private Integer deleted;
```

**application.yml 加入配置:**

```yml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: flag  #全局逻辑删除字段值 3.3.0开始支持，详情看下面。
      logic-delete-value: 1     # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
```

效果: 使用mp自带方法删除和查找都会附带逻辑删除功能 \(自己写的xml不会\)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021041516101015.png)

## 条件构造器Wrapper

**1.查询name不为null的用户，并且邮箱不为null的永不，年龄大于等于35的用户**

```java
@Test
public void findByAgeLessThan(){
    // 查询name不为null的用户，并且邮箱不为null的永不，年龄大于等于35的用户
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    wrapper.isNotNull("name");
    wrapper.isNotNull("email");
    wrapper.ge("age",35);
    userMapper.selectList(wrapper).forEach(System.out::println);
}
```

执行结果：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210416101238197.png)

**2.查询name为LY的用户**

```java
@Test
public void findByName(){
    // 查询name为LZY的用户
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    wrapper.eq("name","LY");
    User user=userMapper.selectOne(wrapper);
    System.out.println(user);
}
```

执行结果：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021041610143877.png)  
**3.查询年龄在10\~30岁之间的用户**

```java
@Test
public void findByAgeBetween(){
    // 查询年龄在10~30岁之间的用户
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    wrapper.between("age",10,30);
    Integer count =userMapper.selectCount(wrapper);//查询结果数
    System.out.println(count);
}
```

执行结果：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210416101639799.png)

**4.测试模糊查询**

```java
//模糊查询
@Test
public void findByLink(){
    QueryWrapper<User> wrapper =new QueryWrapper<>();

    wrapper.notLike("name","Z");//相当于NOT LIKE '%Z%'
    wrapper.likeLeft("email","@qq.com");//相当于LIKE '%@qq.com'
    List<Map<String,Object>> maps =userMapper.selectMaps(wrapper);//查询结果数
    maps.forEach(System.out::println);
}
```

测试结果：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021041610214227.png)

**5.测试子查询**

```java
@Test
public void findById() {
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    //子查询
    wrapper.inSql("id", "select id from user where id<5");
    List<Object> objects = userMapper.selectObjs(wrapper);
    objects.forEach(System.out::println);
}
```

测试结果：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210416102252151.png)

**6.通过id进行排序**

```java
@Test
public void findByIdOrderByIdAsc(){
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    //通过id进行排序
    wrapper.orderByAsc("id");
    List<User> users =userMapper.selectList(wrapper);
    users.forEach(System.out::println);
}
```

执行结果：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210416102346114.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0x6eTQxMDk5Mg==,size_16,color_FFFFFF,t_70)

## 代码自动生成器

AutoGenerator 是 MyBatis-Plus 的代码生成器，通过 AutoGenerator 可以快速生成 Entity、 Mapper、Mapper XML、Service、Controller 等各个模块的代码，极大的提升了开发效率。

**基本使用：**

新建一个springboot工程：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210416094438486.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0x6eTQxMDk5Mg==,size_16,color_FFFFFF,t_70)  
在pom.xml中添加相关依赖：

```xml
<!--在之前pom.xml文件的基础上添加-->
<!--代码生成器依赖.3.0.3以后需要添加该依赖-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.3.1</version>
</dependency>
<!--默认的模板引擎velocity-->
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.3</version>
</dependency>
```

编写yml文件，直接使用之前的即可：

```yml
spring:
  #配置数据源
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mybatis_plus?useSSL=false&serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
    username: root
    password: 123456
  #设置开发环境
  profiles:
    active: dev
#配置日志，我们所用的sql现在是不可见的，我们希望知道他是怎么执行的，所以我们必须要查看日志！
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      logic-delete-field: flag  # 全局逻辑删除的实体字段名(since 3.3.0,配置后可以忽略不配置步骤2)
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
server:
  port: 8010
```

编写代码生成器：

```java
/**
 * @Author: Ly
 * @Date: 2021-04-16 09:06
 */
import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.IdType;

import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.po.TableFill;
import com.baomidou.mybatisplus.generator.config.rules.DateType;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import java.util.ArrayList;

// 代码自动生成器
public class MyBatisPlusGenerator {
    public static void main(String[] args) {
        // 需要构建一个 代码自动生成器 对象
        AutoGenerator mpg = new AutoGenerator();
        // 配置策略
        // 1、全局配置
        GlobalConfig gc = new GlobalConfig();
        //获取当前项目路径
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath+"/src/main/java");
        gc.setAuthor("龙源lll");
        gc.setOpen(false);
        gc.setFileOverride(false);  // 是否覆盖
        gc.setServiceName("%sService"); // 去Service的I前缀
        gc.setIdType(IdType.AUTO);
        gc.setDateType(DateType.ONLY_DATE);
        gc.setSwagger2(true);
        mpg.setGlobalConfig(gc);

        //2、设置数据源
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/mybatis_plus?useSSL=false&serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("520992");
        dsc.setDbType(DbType.MYSQL);
        mpg.setDataSource(dsc);

        //3、包的配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName("blog");
        pc.setParent("com.ly");
        pc.setEntity("entity");
        pc.setMapper("mapper");
        pc.setService("service");
        pc.setController("controller");
        mpg.setPackageInfo(pc);

        //4、策略配置
        StrategyConfig strategy = new StrategyConfig();
        //strategy.setInclude("table1","table2",...); // 设置要映射的表名
        strategy.setInclude("user"); // 设置要映射的表名
        strategy.setNaming(NamingStrategy.underline_to_camel);
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        // 自动lombok
        strategy.setEntityLombokModel(true);
        strategy.setLogicDeleteFieldName("deleted");
        // 自动填充配置
        TableFill gmtCreate = new TableFill("gmt_create", FieldFill.INSERT);
        TableFill gmtModified = new TableFill("gmt_modified", FieldFill.INSERT_UPDATE);
        ArrayList<TableFill> tableFills = new ArrayList<>();
        tableFills.add(gmtCreate);
        tableFills.add(gmtModified);
        strategy.setTableFillList(tableFills);
        // 乐观锁
        strategy.setVersionFieldName("version");
        strategy.setRestControllerStyle(true);
        strategy.setControllerMappingHyphenStyle(true);
        // localhost:8080/hello_id_2
        mpg.setStrategy(strategy);
        mpg.execute();
        //执行
    }
}
```

运行后的效果图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210416094410270.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0x6eTQxMDk5Mg==,size_16,color_FFFFFF,t_70)