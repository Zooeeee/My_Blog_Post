---
title: 毕业设计中springboot的使用
comments: true
tags:
  - Java
  - Springboot
  - 毕业设计
date: 2020-04-12 15:06:43
categories: Java
---
[项目地址](https://github.com/Zooeeee/Online_exam)
#### 基本的项目结构(限于我当前的理解)
1111111

![基本的项目结构](https://upload-images.jianshu.io/upload_images/11628766-0acc059ee71b8c30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

333333333333333

<img src="https://upload-images.jianshu.io/upload_images/11628766-0acc059ee71b8c30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"/>

* config 用于放置其他框架的配置java文件,例如我此处放置了jackson的配置,使得前台返回的null值会自动转换成空字符串
* controller 用于放置控制类,MVC结构中C的功能,用于分发路由,接受参数,返回数据
* Dao 用于放置数据访问对象,直接访问数据层
* jsonBean 由于我这里使用的是springboot默认的jackson框架,当前端发送一个json消息时,要通过对应的jsonBean对象才能在controller中接受消息
* model 实体类,MVC中的M,此处我用的是springdata jpa,底层是用hibernate实现,所以此处其实是数据库中表在java中的映射
* service 服务类,dao类实现基础的crud , service是dao与controller之间的一个类,用于对dao进行更符合要求的封装
* utils 工具类,这里我放置了token生成的工具类
* Vo 视图层,将数据库表中的字段转化成文字,类似于数据库中的视图
* RunApp 启动类,放置在最外层,能够扫描到同级目录以及他们的子目录
#### 静态图片资源的非空才能生成target
Spring Boot 对静态资源映射提供了默认配置
Spring Boot 默认将 /** 所有访问映射到以下目录
```
classpath:/static
classpath:/public
classpath:/resources
classpath:/META-INF/resources
```
![resource](https://upload-images.jianshu.io/upload_images/11628766-fb635f16a3af9f5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
<img src="https://upload-images.jianshu.io/upload_images/11628766-0acc059ee71b8c30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240"/>
要注意的是其实图片是存放在target中的,如果只是在src中新建一个空目录,是不会在target中有对应的目录的,需要的要么是在空目录中放置一个文件,要么是判断有无目录,然后生成
![target](https://upload-images.jianshu.io/upload_images/11628766-cbe209297668486a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 一些基本的注释
* @SpringBootApplication 启动类
* @Service 服务类
* @Autowrised 注入类,在要使用Dao与Service的类中使用
* @RestController  标明该类是控制类,且能直接返回List,String,Map等数据类型
* @RequestMapping  要配置 Web 请求的映射
* @RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest  测试类注解
#### contoller类接受复杂json数据需要相应的jsonBean，并且加上@RequestBody注释参数
这样才能接受Json类型的数据
#### 一些lombok的常用注释
* Data：作用于类上，是以下注解的集合：@ToString @EqualsAndHashCode @Getter @Setter @RequiredArgsConstructor
* @Cleanup：自动关闭资源，针对实现了java.io.Closeable接口的对象有效，如：典型的IO流对象
* @AllArgsConstructor：生成全参构造器
* @NoArgsConstructor：生成无参构造器；