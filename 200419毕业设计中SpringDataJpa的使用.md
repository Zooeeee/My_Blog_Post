---
title: 毕业设计中SpringDataJpa的使用
comments: true
tags:
  - Java
  - SpringDataJpa
  - 毕业设计
date: 2020-04-12 16:19:11
categories: Java
---
[项目地址](https://github.com/Zooeeee/Online_exam)
#### 使用原因
因为是毕业设计项目,属于小型项目,没有必要上mybatis,所以选择了对于单表crud操作更加简单的Spring Data Jpa
#### 简介
JPA意即Java持久化API，是Sun官方在JDK5.0后提出的Java持久化规范，这些接口所在包为javax.persistence.
JPA的出现主要是为了简化持久层开发以及整合ORM技术，结束Hibernate、TopLink、JDO等ORM框架各自为营的局面。JPA是在吸收现有ORM框架的基础上发展而来，易于使用，伸缩性强。
主要注重于对操作的映射,而不是mybatis对于结果集的映射
#### 在Springboot中引入
```xml
<!--spring-data-jpa-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<!-- 添加hibernate 依赖  此处用的实现层是hibernate-->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-entitymanager</artifactId>
    <version>5.0.3.Final</version>
</dependency>
```
```yml
spring:
  jpa:
    properties:
      hibernate:
        jdbc:
          time_zone: Hongkong
        hbm2ddl:
          auto: update
        dialect: org.hibernate.dialect.MySQL5InnoDBDialect
        format_sql: true
    show-sql: true
    database: mysql
```
hibernate 指的是实现层框架
show-sql 指的是运行dao时会在控制台打印sql语句
dialect 指的是不同数据库的 语法糖
auto: update 指的是更新表,create就是删除表之后重新创建
time_zone: Hongkong 解决mysql时区与中国不同的问题
#### 基本流程
1. 在model中新建数据库中表的对应类,用@Entity注释,用@Table(name = "t_problem")来映射对应表
2. 主键的注释  --model包
   ```java
  @Id  //标注主键
  @GeneratedValue(strategy = GenerationType.IDENTITY) //标注主键自增方式
  @Column(name = "id")
  private Integer id;
   ```
3. Dao层
  ```java
  public interface ProblemDao extends JpaRepository<Problem,Integer>{}
  ```
  JpaRepository是一个基本的jpa仓库来,还有simple,crud,specific等仓库
4. Service
   直接注入Dao
#### Dao中自带的方法
get,find,delete,save,count
#### Dao中自己写的接口
在Dao中可以写自定义的接口,然后jpa会自动生成相应的方法
```java
// 方法的名称要遵循 findBy + 属性名（首字母大写） + 查询条件(首字母大写 Is Equals)
// findByNameLike(String name)
// findByName(String name)
// findByNameAndAge(String name, Integer age)
// findByNameOrAddress(String name)


public User findByUsernameAndPassword(String userName,String password);
public User findByNickName(String nickname);
```
#### Dao中自定义的jpql
```java
// select 新建一个视图层,返回的泛型就是该VO类
// left join 实现多表连接
// Page 实现分页
@Query("select new com.zy.vo.problem.ProblemVo(p.id,t.type,s.subject,p.name,p.answer,p.content,p.analysis,d.difficulty,u.nickName) " +
    "from Problem as p " +
    "left join ProblemToType as t on p.type = t.id " +
    "left join ProblemToDifficulty as d on p.difficulty = d.id " +
    "left join ProblemToSubject as s on p.subject = s.id " +
    "left join User as u on p.creator = u.id " +
    "order by p.id ")
Page<ProblemVo> getProblemVo(Pageable pageable);

// 直接返回List的泛型
// 获取所有题型
@Query("select distinct t.type from Problem as p LEFT JOIN ProblemToType as t on p.type = t.id ")
List<String> getProblemType();


@Query("select new com.zy.vo.result.ResultVo(r.resultId ,r.examId,r.totalMark,r.createTime,u.nickName,e.name,e.des,u.avatar) from Result as r left join User as u on r.stuId = u.id left join Exam as e on e.id = r.examId where r.stuId = ?1")
List<ResultVo> getMyAllResultVo(Integer stuId);
```
也可使用sql语句
```java 
// 使用nativeQuery = true来使用sql语句,同时用?idnex来对应传入的参数
@Query(nativeQuery = true,value = "SELECT p.id FROM t_problem as p left join t_problem_subject as s on s.id = p.subject left join t_problem_type as t on t.id = p.type left join t_problem_difficulty as d on d.id = p.difficulty where p.is_used = 1 and s.subject = ?1 and t.type = ?2 and d.difficulty = ?3 ORDER BY RAND() LIMIT ?4")
List<Integer> getConditionByRandom(String subject,String type,String difficulty,Integer rand);

```
#### JpaSpecificationExecutor
```java
@Test
@Transactional
@Rollback(value = false)
Specification<User> specification1 = new Specification<User>() {
@Override
public Predicate toPredicate(Root<User> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
     List<Predicate> list = new ArrayList<Predicate>();
      if(StringUtils.isNotBlank(username)){
         list.add(cb.like(root.get("username").as(String.class), "%"+username+"%"));
      }
      if(StringUtils.isNotBlank(password)){
          list.add(cb.equal(root.get("password").as(String.class), password));
      }
//新建一个数组，这个对象代表一个查询条件
      Predicate[] p = new Predicate[list.size()];
     // 将list对象转化为p类型数组，加载cb中
      return cb.and(list.toArray(p));
     }
};
```
用来定义复杂或者是自定义的查询
#### 一些踩过的坑
1. 场景:将题目选项字段设为option,但是在dao层报错
   解决:option是mysql的关键字,在sql语句中会报错
2. 返回vo类时要注意vo类有没有相应的构造函数
3. 关于mysql数据库中字段是json的存取值方法
```xml
<dependency>
   <groupId>com.vladmihalcea</groupId>
   <artifactId>hibernate-types-52</artifactId>
   <version>2.4.3</version>
</dependency>
```
实体类中的写法
```java
@Data
@Entity
@TypeDef(name = "json", typeClass = JsonStringType.class)
public class ExpressOrder{
    /**主键自增 */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
 
    /**商品相关信息 */
    @Type(type = "json")
    @Column( columnDefinition = "json" )
    private List<CargoModel> cargoModelList;
 
    /**增值服务信息 */
    @Type(type = "json")
    @Column( columnDefinition = "json" )
    private List<AddedServiceModel> addedServiceModelList;
}
 
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CargoModel implements Serializable {
    private String name;// 货物名称
    private String count;// 货物数量
}
 
@Data
@NoArgsConstructor
@AllArgsConstructor
public class AddedServiceModel implements Serializable {
    private String name;//增值服务名，如COD等。
    private String value;
    private String value1;
}

```
4. List返回entity类报错
在实体类中添加
```java
@JsonIgnoreProperties(value = { "hibernateLazyInitializer", "handler" })
```
在此标记不生成json对象的属性

因为jsonplugin用的是java的内审机制.hibernate会给被管理的pojo加入一个hibernateLazyInitializer属性,jsonplugin会把hibernateLazyInitializer也拿出来操作,并读取里面一个不能被反射操作的属性就产生了这个异常.
5. 关于自动添加创建时间和最后修改时间
```java
@Data
@Table(name = "sys_service")
@Entity
@EntityListeners(AuditingEntityListener.class)
public class SysService extends ImprovedNamingStrategy {
    @GeneratedValue(strategy= GenerationType.IDENTITY)
    @Id
    private Integer id;
    // 创建时间
    @CreatedDate
    @Column(name = "createTime",updatable = false,nullable = false)
    private Date createTime;
    //最后修改时间
    @LastModifiedDate
    @Column(name = "updateTime",nullable = false)
    private Date updateTime;
}

// 在启动类中
@EnableJpaAuditing
// 在Model中用Date类型接受
```
注:时区问题
我们这里的时间是 UTC+8 即东八区时间,观察到数据入库之后,时间都为 UTC 时间.
解决方法:

数据库连接添加时区指定: &serverTimezone=Hongkong
上面的配置是解决问题的根本方法,以下是一些设置时区的配置

```yml
spring:
  jackson:
    time-zone: Hongkong #jackson时区配置
    date-format: yyyy-MM-dd HH:mm:ss  # 格式化返回时间
spring:
  jpa:
    properties:
      hibernate:
        jdbc:
          time_zone: Hongkong #Hibernate时区配置
```
