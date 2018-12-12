# 目录大纲
[TOC]
# 前言
&emsp;&emsp;JPA 诞生的缘由是为了整合第三方 ORM 框架，建立一种标准，[百度百科](https://baike.baidu.com/item/JPA)说 JDK 为了实现 ORM 的天下归一才制定了 JPA 这一套标准，目前也是在按照这个方向发展，但是还没能完全实现。在 ORM 框架中，Hibernate 是一支很大的部队，很方便，能力也很强，同时，Hibernate 也和 JPA 整合的比较良好，我们可以认为 JPA 是标准，事实上也是，JPA 几乎全是接口，实现都是 Hibernate 在做，宏观上看，在 JPA 的统一之下，Hibernate 目前很良好的运行着。
<br>
&emsp;&emsp;前面阐述了 JPA 和 Hibernate 的关系，那么 Spring Data JPA 又是什么东西呢？这地方需要稍微解释一下，我们做 Java 开发的都知道 Spring 的强大，到目前为止，企业级应用 Spring 几乎是无所不能，无所不在，已经是事实上的标准了，企业级应用不适用 Spring 的几乎没有，这样说没错吧，而 Spring 整合第三方框架的能力又很强，它要做的不仅仅是个最早的 IOC 容器那么简单的一回事，现在 Spring 涉及的面很广，主要体现在和第三方工具的整合上。而在与第三方整合这方面，Spring 做了持久化这一块的工作，个人感觉 Spring 希望把持久化这一块内容也拿下，于是就有了 Spring Data 系列，包括 spring-data-jpa，spring-data-template，spring-data-mongodb，spring-data-redis，还有个民间产品 mybatis-spring，和前面类似，这是和 MyBatis 整合的第三方包，这些都是持久化工具干的事儿。
<br>
&emsp;&emsp;在学习 Spring Data JPA 之前，先对 ORM 思想 和 JPA 标准进行一个简单的了解，因为 Spring Data JPA 是建立在 ORM 思想和 JPA 标准之上的一个持久层框架。

# 1. ORM 概述
&emsp;&emsp;对象关系映射（Object Relational Mapping）简称 ORM，ORM 是一种为了解决面向对象与关系数据库存在互不匹配的现象的技术。简单的说，ORM 是通过描述对象和数据之间映射的元数据，将程序中的对象自动持久化到关系数据库中。

# 2. JPA 概述
&emsp;&emsp;JPA 是 **J**ava **P**ersistence **A**PI 的简称，中文翻译：Java 持久层 API，是用 JDK5.0 注解或 XML 来描述对象和数据库表的映射关系，并将运行期的实体对象持久化到数据库中。
<br>
Sun 引入新的 JPA ORM 规范出于两个原因：
- 简化现有 JavaEE 和 JavaSE 应用开发工作
- Sun 希望整合 ORM 技术，实现天下归一
<br>
下图展示了 JPA 规范与 ORM 框架之间的关系


JPA 包含以下3个方面的技术：
- ORM 映射元数据
JPA 支持 JDK5.0 注解和 XML 两种元数据的形式，元数据描述对象和表之间的映射关系，框架据此将实体对象持久化到数据库表中。
<br>
- API
用来操作实体对象，执行增删改查操作，框架在后台替代我们完成所有的事情，使开发者得以从繁琐的 JDBC 和 SQL 代码中解脱出来。
<br>
- 查询语言
这是持久化操作中很重要的一部分，通过面向对象而非面向数据库SQL查询数据，避免程序与SQL紧密耦合。

## 2-1. JPA 的优势
- 标准化
JPA 是 JCP 组织发布的 JavaEE 标准之一，因此任何声称符合 JPA 标准的框架都遵循同样的架构，提供相同的访问 API，这保证了基于 JPA 开发的企业应用能够经过少量的修改就能够在不同的 JPA 框架下运行。
<br>
- 简单方便
JPA 的主要目标之一就是提供简单的编程模型，在 JPA 框架下创建实体和创建 Java 类一样简单，没有任何的约束和限制，只需要使用 javax.persistence.Entity 进行声明，JPA 的框架和接口也非常简单，没有太多特别的规则和设计模式的要求，开发者可以很容易的掌握。JPA 基于非侵入式设计原则，因此可以很容易的和其他框架或者容器集成。
<br>
- 查询能力
JPA 的查询语言是面向对象而非面向数据库的，它以面向对象的自然语法构造查询语句，可以看成是 Hibernate HQL 的等价物。JPA 定义了独特的 JPQL（Java Persistence Query Language），JPQL 是 EJBQL 的一种扩展，它是针对实体的一种查询语言，操作的是实体，而不是关系数据库的表，而且能够支持批量更新和修改、join、group by、having 等通常只有在 SQL 才能够提供的高级查询特性，甚至还能够支持子查询。
<br>
- 高级特性
JPA 能够支持面向对象的高级特性，例如类之间的继承、多态等复杂关系，这样的支持能够让开发者最大限度的使用面向对象的模型设计企业应用，而不需要自行处理这些特性在关系数据库的持久化。

# 3. JPA 初体验
## 3-1. 搭建项目环境
首先需要创建一个工程，因不涉及页面交互，所以创建一个简单的 Maven 工程即可。
### 3-1-1. 添加依赖
```xml
<properties>
    <project.hibernate.version>5.0.7.Final</project.hibernate.version>
</properties>

<dependencies>
    <!--junit-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
    
    <!--hibernate对jpa的支持-->
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-entitymanager</artifactId>
        <version>${project.hibernate.version}</version>
    </dependency>
    
    <!--c3p0-->
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-c3p0</artifactId>
        <version>${project.hibernate.version}</version>
    </dependency>
    
    <!--log4j-->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
    
    <!--mysql-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.45</version>
    </dependency>
</dependencies>
```

### 3-1-2. 编写 JPA 配置
JPA 的配置文件必须放在项目 resource 目录下的 META-INF 文件夹下，且名称必须为 persistence.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence xmlns="http://java.sun.com/xml/ns/persistence" version="2.0">
    <!--
        持久化单元
            name: 单元名称
            transaction-type: 事务管理方式
                JTA: 分布式事务管理
                RESOURCE_LOCAL: 本地事务管理
    -->
    <persistence-unit name="myJpa" transaction-type="RESOURCE_LOCAL">
        <!--JPA的实现-->
        <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>

        <!--可选配置（JPA的实现配置，例如Hibernate的配置）-->
        <properties>
            <!--数据库配置-->
            <property name="javax.persistence.jdbc.user" value="root"/>
            <property name="javax.persistence.jdbc.password" value="root"/>
            <property name="javax.persistence.jdbc.driver" value="com.mysql.jdbc.Driver"/>
            <property name="javax.persistence.jdbc.url" value="jdbc:mysql://localhost:3306/test"/>
            
            <!--Hibernate配置-->
            <property name="hibernate.show_sql" value="true"/> <!--显示SQL（true/false）-->
            <property name="hibernate.hbm2ddl.auto" value="update"/><!--自动创建表（create/update/none）-->
        </properties>
    </persistence-unit>
</persistence>
```

### 3-1-3. 编写实体类
使用 JDK5.0 注解来描述实体类和数据库表的映射关系
```java
/**
 * 客户的实体类
 * @author qinghua
 * @date 2018/9/7 21:16
 */
@Entity //声明实体类
@Table(name = "cst_customer") //实体类和表的映射关系
public class Customer {

    /**
     * 主键生成策略
     * IDENTITY: 需要数据库支持自动增长 (MySQL,SqlServer)
     * SEQUENCE: 需要数据库支持序列 (Oracle)
     * TABLE : 由JPA维护一张独立的主键表
     * AUTO : 根据数据库类型自动选择生成策略
     */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cust_id")
    private Long custId; //客户编号（主键）
    @Column(name = "cust_name")
    private String custName; //客户名称（公司名称）
    @Column(name = "cust_source")
    private String custSource; //客户信息来源
    @Column(name = "cust_industry")
    private String custIndustry;//客户所属行业
    @Column(name = "cust_level")
    private String custLevel; //客户级别
    @Column(name = "cust_address")
    private String custAddress; //客户联系地址
    @Column(name = "cust_phone")
    private String custPhone; //客户联系电话
    
    //省略 getter & setter

    //省略 toString()
}
```

## 3-2. 第一个 JPA 程序
- JPA 的基本增删改查一共6个步骤
    - 加载配置文件，创建实体管理器工厂
    - 从实体管理器工厂中获得实体管理器
    - 从实体管理器中获得事务对象
    - 使用实体管理器完成 CRUD 操作
    - 提交或回滚事务
    - 释放资源
<br>
- 下面的代码是一个简单的保存操作
    ```java
    @Test
    public void testSave() {
        //1、加载配置文件，创建实体管理器工厂
        EntityManagerFactory factory = Persistence.createEntityManagerFactory("myJpa");
    
        //2、从实体管理器工厂中获得实体管理器
        EntityManager manager = factory.createEntityManager();
    
        //3、从实体管理器中获得事务对象
        EntityTransaction transaction = manager.getTransaction();
        transaction.begin();
    
        //4、使用实体管理器完成 CRUD 操作
        Customer customer = new Customer();
        customer.setCustName("Google");
        customer.setCustIndustry("互联网");

        manager.persist(customer); //保存对象

        //5、提交或回滚事务
        transaction.commit();
    
        //6、释放资源
        manager.close();
        factory.close();
    }
    ```
<br>
- 实体管理器工厂工具类
从上面的代码中可以看出，实体管理器工厂在每个测试方法都需要重复的创建，从工厂中获得的实体管理器在使用完毕后需要释放，这造成了不必要的时间开销。可以编写一个实体管理器工厂的工具类，在工具类中维护一个实体管理器工厂，整个应用只有这一个工厂，提供一个方法获得实体管理器，使用完毕后也无需释放资源。
    ```java
    /**
     * 实体管理器工具类
     *
     * @author qinghua
     * @date 2018/9/12 12:26
     */
    public class JpaUtils {
    
        /**
         * 维护一个公共的实体类管理器工厂
         */
        private static EntityManagerFactory factory;
    
        /**
         * 在静态代码块中加载配置文件来初始化管理器工厂
         */
        static {
            factory = Persistence.createEntityManagerFactory("myJpa");
        }
    
        /**
         * 从工厂中获取一个实体管理器
         * @return
         */
        public static EntityManager getEntityManager() {
            return factory.createEntityManager();
        }
    }
    ```

## 3-3. 增删改查
JPA 的实体管理器提供了基本的增删改查 API
| 操作 | 方法 |
| --- |
| 保存 | `persist`(T entity) |
| 查询（立即加载） | `find`(Class entityClass, Long primaryKey) |
| 查询（延迟加载） | `getReference`(Class entityClass, Long primaryKey) |
| 更新 | `merge`(T entity) |
| 删除 | `remove`(T entity) |
<br>
程序示例：
```java
package com.example.test;

import com.example.domain.Customer;
import com.example.entity.JpaUtils;
import org.junit.Test;

import javax.persistence.EntityManager;
import javax.persistence.EntityTransaction;

/**
 * @author qinghua
 * @date 2018/9/7 21:27
 */
public class JpaTest {

    /**
     * 保存, persist
     */
    @Test
    public void testSave() {

        //从工具类中获得实体管理器
        EntityManager manager = JpaUtils.getEntityManager();

        //获取事务对象，开启事务
        EntityTransaction transaction = manager.getTransaction();
        transaction.begin();

        //完成增删改查操作
        Customer customer = new Customer();
        customer.setCustName("Google");
        customer.setCustIndustry("互联网");

        //提交或回滚事务
        manager.persist(customer); //保存对象
        transaction.commit();
    }

    /**
     * 根据主键查询（及时加载）,find
     */
    @Test
    public void testFind() {
        //通过工具类获得实体管理器
        EntityManager entityManager = JpaUtils.getEntityManager();

        /**
         * 执行查询
         *
         * 参数一：Class（字节码）对象，指查询出的数据封装到该类的实体对象中
         * 参数二：主键
         */
        Customer customer = entityManager.find(Customer.class, 1L);
        System.out.println(customer);
    }

    /**
     * 根据主键查询（延迟加载）,getReference
     */
    @Test
    public void testGetReference() {
        //通过工具类获得实体管理器
        EntityManager entityManager = JpaUtils.getEntityManager();

        /**
         * 执行查询
         *
         * 参数一：Class（字节码）对象，指查询出的数据封装到该类的实体对象中
         * 参数二：主键
         */
        Customer customer = entityManager.getReference(Customer.class, 1L);
        System.out.println(customer);
    }

    /**
     * 删除记录, remove
     */
    @Test
    public void testRemove() {
        //通过工具类获得实体管理器
        EntityManager entityManager = JpaUtils.getEntityManager();

        //开启事务
        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();

        //先查询出要删除的记录
        Customer customer = entityManager.find(Customer.class, 1L);

        //执行删除
        entityManager.remove(customer);

        //提交事务
        transaction.commit();
    }

    /**
     * 更新记录, merge
     */
    @Test
    public void testUpdate() {
        //通过工具类获得实体管理器
        EntityManager entityManager = JpaUtils.getEntityManager();

        //开启事务
        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();

        //先查询出要删除的记录
        Customer customer = entityManager.find(Customer.class, 1L);
        customer.setCustPhone("13800138000");

        //执行更新
        entityManager.merge(customer);

        //提交事务
        transaction.commit();
    }
}
```

## 3-4. JPQL 查询
&emsp;&emsp;JPQL（Java Persistence Query Language）是 JPA 制定的面向对象的查询语言，其目的是为了避免程序与SQL 紧密耦合，语法和 SQL 非常相似，不同是是 SQL 面向的是数据库表和字段，而 JPQL 面向的是实体类和属性。
<br>
SQL 和 JPQL 的语法差异：
| 语言 | 语法示例 |
| --- |
| SQL | select * from 表名 where 字段名 = ? and 字段名 = ? |
| JPQL | from 类名 where 属性名 = ? and 属性名 = ? |
<br>
&emsp;&emsp;JPQL 查询需要通过实体管理器（EntityManager）的 createQuery(String jpql) 方法传递一个 JPQL 语句来创建一个 Query 对象，再通过 Query 对象的方法进行参数赋值和执行查询操作，下面是一些简单的示例程序。
```java
package com.example.test;

import com.example.domain.Customer;
import com.example.entity.JpaUtils;
import org.junit.Test;

import javax.persistence.EntityManager;
import javax.persistence.Query;
import java.util.List;

/**
 * @author qinghua
 * @date 2018/9/14 16:50
 */
public class JpqlTest {

    /**
     * 查询所有
     */
    @Test
    public void testFindAll() {
        //获得实体管理器
        EntityManager entityManager = JpaUtils.getEntityManager();

        //创建Query对象
        String jpql = "from Customer";
        Query query = entityManager.createQuery(jpql);

        //执行查询
        List<Customer> list = query.getResultList();
        
        //迭代
        for (Customer customer : list) {
            System.out.println(customer);
        }
    }

    /**
     * 排序
     */
    @Test
    public void testOrderBy() {
        //获得实体管理器
        EntityManager entityManager = JpaUtils.getEntityManager();

        //创建Query对象
        String jpql = "from Customer order by custId desc";
        Query query = entityManager.createQuery(jpql);

        //执行查询
        List<Customer> list = query.getResultList();
        
        //迭代
        for (Customer customer : list) {
            System.out.println(customer);
        }
    }

    /**
     * 统计查询
     */
    @Test
    public void testCount() {
        //获得实体管理器
        EntityManager entityManager = JpaUtils.getEntityManager();

        //创建Query对象
        String jpql = "select count(custId) from Customer";
        Query query = entityManager.createQuery(jpql);
        
        //执行查询
        Object count = query.getSingleResult();

        System.out.println(count);
    }

    /**
     * 分页
     */
    @Test
    public void testFindPage() {
        //获得实体管理器
        EntityManager entityManager = JpaUtils.getEntityManager();

        //创建Query对象
        String jpql = "from Customer";
        Query query = entityManager.createQuery(jpql);

        //设置分页参数
        query.setFirstResult(1);
        query.setMaxResults(2);

        //执行查询
        List<Customer> list = query.getResultList();
        
        //迭代
        for (Customer customer : list) {
            System.out.println(customer);
        }
    }

    /**
     * 条件查询
     */
    @Test
    public void testLike() {
        //获得实体管理器
        EntityManager entityManager = JpaUtils.getEntityManager();

        //创建Query对象
        String jpql = "from Customer where custName like ?1";
        Query query = entityManager.createQuery(jpql);

        //设置参数
        query.setParameter(1, "G%");

        //执行查询
        List<Customer> list = query.getResultList();
        
        //迭代
        for (Customer customer : list) {
            System.out.println(customer);
        }
    }
}
```

# 4. Spring Data JPA
&emsp;&emsp; Spring Data JPA 的基本概述在本文的前言部分已经做了介绍，这里不再重复。
## 4-1. 搭建项目环境
### 4-1-1. 创建工程
创建一个简单 Maven 项目。

### 4-1-2. 添加依赖
```xml
<properties>
    <spring.version>5.0.2.RELEASE</spring.version>
    <hibernate.version>5.0.7.Final</hibernate.version>
    <slf4j.version>1.6.6</slf4j.version>
    <log4j.version>1.2.12</log4j.version>
    <c3p0.version>0.9.1.2</c3p0.version>
    <mysql.version>5.1.6</mysql.version>
</properties>

<dependencies>
    <!--junit-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>

    <!--spring-->
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.8.13</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aop</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context-support</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-orm</artifactId>
        <version>${spring.version}</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>${spring.version}</version>
    </dependency>

    <!--spring data jpa-->
    <dependency>
        <groupId>org.springframework.data</groupId>
        <artifactId>spring-data-jpa</artifactId>
        <version>2.0.5.RELEASE</version>
    </dependency>

    <!--el(spring data jpa 依赖el)-->
    <dependency>
        <groupId>org.glassfish.web</groupId>
        <artifactId>javax.el</artifactId>
        <version>2.2.4</version>
    </dependency>

    <!--hibernate-->
    <dependency><!--hibernate jpa 实现-->
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-entitymanager</artifactId>
        <version>${hibernate.version}</version>
    </dependency>
    <dependency><!--hibernate 校验-->
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-validator</artifactId>
        <version>5.2.1.Final</version>
    </dependency>

    <!--c3-0-->
    <dependency>
        <groupId>c3p0</groupId>
        <artifactId>c3p0</artifactId>
        <version>${c3p0.version}</version>
    </dependency>

    <!--log-->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>${log4j.version}</version>
    </dependency>
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>${slf4j.version}</version>
    </dependency>

    <!--mysql-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>${mysql.version}</version>
    </dependency>

</dependencies>
```

### 4-1-3. 编写 Spring 配置
Spring Data JPA 的配置文件就是Spring的配置文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:jpa="http://www.springframework.org/schema/data/jpa"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/data/jpa
        http://www.springframework.org/schema/data/jpa/spring-jpa.xsd">

    <!--实体管理器-->
    <bean id="entityManagerFactory" class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
        <property name="dataSource" ref="dataSource"/><!--连接池-->
        <property name="packagesToScan" value="com.example.domain"/><!--实体类所在的包-->
        <property name="persistenceProvider"><!--jpa 实现提供者-->
            <bean class="org.hibernate.jpa.HibernatePersistenceProvider"/>
        </property>
        <property name="jpaVendorAdapter"><!--jpa 提供者适配-->
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter">
                <property name="generateDdl" value="false"/><!--自动创建数据库表-->
                <property name="database" value="MYSQL"/><!--数据库类型-->
                <property name="databasePlatform" value="org.hibernate.dialect.MySQLDialect"/><!--数据库方言-->
                <property name="showSql" value="true"/><!--显示SQL语句-->
            </bean>
        </property>
        <property name="jpaDialect"><!--jpa 方言（hibernate高级特性）-->
            <bean class="org.springframework.orm.jpa.vendor.HibernateJpaDialect"/>
        </property>
    </bean>

    <!--连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="com.mysql.jdbc.Driver"/>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"/>
        <property name="user" value="root"/>
        <property name="password" value="root"/>
    </bean>

    <!--spring data jpa 配置-->
    <jpa:repositories
            base-package="com.example.repository"
            repository-impl-postfix="Impl"
            entity-manager-factory-ref="entityManagerFactory"
            transaction-manager-ref="jpaTransactionManager"/>

    <!--事务管理器-->
    <bean id="jpaTransactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactory"/><!--实体管理工厂-->
    </bean>

    <!--注解事务-->
    <tx:annotation-driven transaction-manager="jpaTransactionManager" proxy-target-class="true"/>

</beans>
```

### 4-1-4. 编写持久层接口
要使用 Spring Data JPA，必须符合它的规范，持久层接口需要继承 JpaRepository 接口。
`CustomerRepository.java`
```java
/**
 * @author qinghua
 * @date 2018/9/15 14:54
 *
 * JpaRepository<实体类型, 实体类主键类型> 要符合JPA规范,必须继承JpaRepository
 * JpaSpecificationExecutor<实体类型> JPA提供的到动态查询接口
 */
public interface CustomerRepository extends JpaRepository<Customer, Long>, JpaSpecificationExecutor<Customer> {
    //...
}
```

## 4-2. 增删改查
```java
package com.example;

import com.example.domain.Customer;
import com.example.repository.CustomerRepository;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.List;

/**
 * @author qinghua
 * @date 2018/9/15 14:54
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class CustomerRepositoryTest {

    @Autowired
    private CustomerRepository customerRepository;

    /**
     * 根据ID查询
     */
    @Test
    public void testFindById() {
        Customer customer = customerRepository.findById(1L).get();
        System.out.println(customer);
    }

    /**
     * 保存/更新对象
     * 传递的对象参数有ID：直接保存
     * 传递的对象参数没有ID：先查询ID是否存在，如果存在则修改，不存在则保存
     */
    @Test
    public void testSave() {
        //创建要保存的对象
        Customer customer = new Customer();
        customer.setCustId(6L);
        customer.setCustName("Facebook");
        customer.setCustIndustry("IT/互联网");

        //执行保存或更新操作
        Customer dbCustomer = customerRepository.save(customer);
        System.out.println(dbCustomer);
    }

    /**
     * 根据ID删除
     */
    @Test
    public void testDeleteById() {
        customerRepository.deleteById(6L);
    }

    /**
     * 查询所有客户
     */
    @Test
    public void testFindAll() {
        List<Customer> list = customerRepository.findAll();
        for (Customer customer : list) {
            System.out.println(customer);
        }
    }

}
```

## 4-3. 其他查询
JPA 除了基本的增删改查以外，还支持查询总数（`count`），判断主键是否存在（`exists`），延迟加载（`getById`）等。
示例程序
```java
package com.example;

import com.example.domain.Customer;
import com.example.repository.CustomerRepository;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import java.util.List;

/**
 * @author qinghua
 * @date 2018/9/15 14:54
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class CustomerRepositoryTest {

    @Autowired
    private CustomerRepository customerRepository;

    /**
     * 查询总数
     */
    @Test
    public void testCount() {
        long count = customerRepository.count();
        System.out.println(count);
    }

    /**
     * 根据ID判断记录是否存在
     */
    @Test
    public void testExistsById() {
        boolean exists = customerRepository.existsById(3L);
        System.out.println(exists);
    }

    /**
     * 延迟加载
     * 底层调用的是 em.getReference
     */
    @Test
    public void testGetOne() {
        Customer customer = customerRepository.getOne(1L);
        System.out.println(customer);
    }

}
```

## 4-4. JPQL 和 本地 SQL
### 4-4-1. JPQL 查询
Spring Data JPA 支持 JPQL 查询，如果需要使用 JPQL 需要在持久层接口中定义抽象方法，并在抽象方法上使用 `@Query` 注解。
<br>
- JPQL 查询
在持久层接口定义抽象方法，具体的 JPQL 参数传递看下面的代码注释：
    ```java
    /**
     * @author qinghua
     * @date 2018/9/15 14:54
     */
    public interface CustomerRepository extends JpaRepository<Customer, Long> {
    
        /**
         * 根据名称和ID查询客户
         *
         * 替换占位符注意：
         * JPQL 语句中的占位符默认按形参的顺序进行替换
         * 如果需要调整顺序，只需要在占位符后面加上形参的索引值
         *
         * @param name
         * @return
         */
        @Query(value = "from Customer where custName = ?1 and custId = ?2")
        Customer findByName(String name, Long id);
    }
    ```
<br>
- JPQL 修改
JPQL 不但支持查询，也支持数据库的更新和删除操作，在 Spring Data JPA 中要使用 JPQL 做修改和删除操作，持久层接口的方法上不但需要添加 `@Query` 主键，还需要添加一个 `@Modifiy` 标签
<br>
示例代码：
    ```java
    /**
     * @author qinghua
     * @date 2018/9/15 14:54
     */
    public interface CustomerRepository extends JpaRepository<Customer, Long> {
    
        /**
         * 根据ID修改客户的名称
         * @param name
         * @param id
         */
        @Query(value = "update Customer set custName = ?1 where custId = ?2")
        @Modifying //表示这是一个修改操作（必须要加）
        void updateCustomer(String name, Long id);

    }
    ```
持久层接口的方法写完了，此时如果在测试方法中执行修改，数据库中的记录并不会发生变化。如果要使用 JPQL 进行修改和删除操作，执行的操作必须要受事务控制，且主动提交事务。
<br>
测试示例代码：
    ```java
    /**
     * @author qinghua
     * @date 2018/9/15 16:12
     */
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(locations = "classpath:applicationContext.xml")
    public class JPQLTest {

        @Autowired
        private CustomerRepository customerRepository;
        
        /**
         * JPQL 修改
         */
        @Test
        @Transactional //事务控制
        @Commit //提交事务
        public void testUpdate() {
            customerRepository.updateCustomer("IBM", 5L);
        }

    }
    ```

### 4-4-2. 本地 SQL 查询
Spring Data JPA 同时也支持使用原生的 SQL 进行查询，只需要将 `@Query` 注解的 `nativeQuery` 属性设置为 true 即可。
<br>
示例程序：
```java
/**
 * @author qinghua
 * @date 2018/9/15 14:54
 */
public interface CustomerRepository extends JpaRepository<Customer, Long> {

    /**
     * 使用本地SQL查询
     *
     * Query注解
     * value ：JPQL语句 / SQL语句
     * nativeQuery ：是否使用本地SQL查询，为true时，value中的值将会被作为本地SQL语句执行
     *
     * 此时返回值是List<Object[]>，也可以映射为实体类对象的集合List<Customer>
     *
     * @param name
     * @return
     */
    @Query(value = "select * from cst_customer where cust_name like ?1", nativeQuery = true)
    List<Object[]> findByName(String name);

}
```

# 5. 命名方法查询
Spring Data JPA 能根据方法的名字动态生成 SQL 语句，只需要按照 Spring Data JPA 方法定义的规则来定义方法即可。<br/>
<br>
## 5-1. 命名方法查询的规则
| Keyword | Sample | JPQL snippet |
| --- |
| And | findByLastnameAndFirstname | … where x.lastname = ?1 and x.firstname = ?2 |
| Or | findByLastnameOrFirstname | … where x.lastname = ?1 or x.firstname = ?2 |
| Is,Equals | findByFirstname,findByFirstnameIs,findByFirstnameEquals | … where x.firstname = ?1 |
| Between | findByStartDateBetween | … where x.startDate between ?1 and ?2 |
| LessThan | findByAgeLessThan | … where x.age < ?1 |
| LessThanEqual | findByAgeLessThanEqual | … where x.age <= ?1 |
| GreaterThan | findByAgeGreaterThan | … where x.age > ?1 |
| GreaterThanEqual | findByAgeGreaterThanEqual | … where x.age >= ?1 |
| After | findByStartDateAfter | … where x.startDate > ?1 |
| Before | findByStartDateBefore | … where x.startDate < ?1 |
| IsNull | findByAgeIsNull | … where x.age is null |
| IsNotNull,NotNull | findByAge(Is)NotNull | … where x.age not null |
| Like | findByFirstnameLike | … where x.firstname like ?1 |
| NotLike | findByFirstnameNotLike | … where x.firstname not like ?1 |
| StartingWith | findByFirstnameStartingWith | … where x.firstname like ?1(parameter bound with appended %) |
| EndingWith | findByFirstnameEndingWith | … where x.firstname like ?1(parameter bound with prepended %) |
| Containing | findByFirstnameContaining | … where x.firstname like ?1(parameter bound wrapped in %) |
| OrderBy | findByAgeOrderByLastnameDesc | … where x.age = ?1 order by x.lastname desc |
| Not | findByLastnameNot | … where x.lastname <> ?1 |
| In | findByAgeIn(Collection<Age> ages) | … where x.age in ?1 |
| NotIn | findByAgeNotIn(Collection<Age> ages) | … where x.age not in ?1 |
| TRUE | findByActiveTrue() | … where x.active = true |
| FALSE | findByActiveFalse() | … where x.active = false |
| IgnoreCase | findByFirstnameIgnoreCase | … where UPPER(x.firstame) = UPPER(?1) |

## 5-2. 命名方法示例
```java
/**
 * @author qinghua
 * @date 2018/9/15 14:54
 */
public interface CustomerRepository extends JpaRepository<Customer, Long> {

    /**
     * And
     * … where x.cust_name = ?1 and x.cust_industry = ?2
     */
    List<Customer> findByCustNameAndCustIndustry(String custName, String industry);

    /**
     * Or
     * … where x.cust_name = ?1 or x.cust_industry = ?2
     */
    List<Customer> findByCustNameOrCustIndustry(String custName, String industry);

    /**
     * is | equals
     * … where x.cust_name = ?1
     */
    List<Customer> findByCustNameEquals(String custName);

    /**
     * Between
     * … where x.cust_id between ?1 and ?2
     */
    List<Customer> findByCustIdBetween(Long start, Long end);

    /**
     * LessThan
     * … where x.cust_id < ?1
     */
    List<Customer> findByCustIdLessThan(Long custId);

    /**
     * LessThanEqual
     * … where x.cust_id <= ?1
     */
    List<Customer> findByCustIdLessThanEqual(Long custId);

    /**
     * GreaterThan
     * … where x.cust_id > ?1
     */
    List<Customer> findByCustIdGreaterThan(Long custId);

    /**
     * GreaterThanEqual
     * … where x.cust_id >= ?1
     */
    List<Customer> findByCustIdGreaterThanEqual(Long custId);

    /**
     * After
     * … where x.cust_id > ?1
     */
    List<Customer> findByCustIdAfter(Long custId);

    /**
     * Before
     * … where x.cust_id < ?1
     */
    List<Customer> findByCustIdBefore(Long custId);

    /**
     * IsNull
     * … where x.cust_phone is null
     */
    List<Customer> findByCustPhoneIsNull();

    /**
     * Before
     * … where x.cust_phone is not null
     */
    List<Customer> findByCustPhoneIsNotNull();

    /**
     * Like
     * … where x.cust_name like ?1
     */
    List<Customer> findByCustNameLike(String custName);

    /**
     * NotLike
     * … where x.cust_name not like ?1
     */
    List<Customer> findByCustNameNotLike(String custName);

    /**
     * NotLike
     * … where x.cust_name like %?1
     */
    List<Customer> findByCustNameStartingWith(String custName);

    /**
     * NotLike
     * … where x.cust_name like ?1%
     */
    List<Customer> findByCustNameEndingWith(String custName);

    /**
     * NotLike
     * … where x.cust_name like %?1%
     */
    List<Customer> findByCustNameContaining(String custName);

    /**
     * OrderBy
     * … where x.cust_name like %?1% order by cust_id desc
     */
    List<Customer> findByCustNameContainingOrderByCustIdDesc(String custName);

    /**
     * Not
     * … where x.cust_name != ?1
     */
    List<Customer> findByCustNameNot(String custName);

    /**
     * In
     * … where x.cust_id in (ids)
     */
    List<Customer> findByCustIdIn(List<Long> ids);

    /**
     * NotIn
     * … where x.cust_id not in (ids)
     */
    List<Customer> findByCustIdNotIn(List<Long> ids);

}
```

# 6. Specifications 动态查询
## 6-1. 动态查询的方法列表
`JpaSpecificationExecutor`
```java
/**
 * 查询一个对象
 */
Optional<T> findOne(@Nullable Specification<T> spec);

/**
 * 查询多个对象
 */
List<T> findAll(@Nullable Specification<T> spec);

/**
 * 分页查询
 */
Page<T> findAll(@Nullable Specification<T> spec, Pageable pageable);

/**
 * 排序查询
 */
List<T> findAll(@Nullable Specification<T> spec, Sort sort);

/**
 * 统计查询
 */
long count(@Nullable Specification<T> spec);
```

## 6-2. 条件查询案例
### 6-2-1. 持久层接口准备
要使用Specification条件查询，持久层接口必须继承JpaSpecification接口。
`CustomerRepository`
```java
/**
 * @author qinghua
 * @date 2018/9/21 10:46
 */
public interface CustomerRepository extends JpaRepository<Customer, Long>, JpaSpecificationExecutor<Customer> {

}
```

### 6-2-2. 条件查询示例
```java
/**
 * @author qinghua
 * @date 2018/9/21 10:47
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:applicationContext.xml"})
public class SpecificationTest {

    @Autowired
    private CustomerRepository customerRepository;

    /**
     * 单条件精准匹配，查询一个客户
     */
    @Test
    public void testFindOne() {
        Customer customer = customerRepository.findOne(new Specification<Customer>() {

            /**
             *
             * @param root 获取要查询的对象属性
             * @param query 条件查询对象
             * @param criteriaBuilder 条件构造器
             * @return
             */
            @Override
            public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) {
                //将作为条件的属性构建成路径对象
                Path<Object> custName = root.get("custName");

                //构建精准匹配的断言对象
                Predicate predicate = criteriaBuilder.equal(custName, "Google");

                return predicate;
            }
        }).get();

        System.out.println(customer);
    }

    /**
     * 多条件精准匹配，查询一个客户
     */
    @Test
    public void testFindOne2() {
        Customer customer = customerRepository.findOne(new Specification<Customer>() {
            @Override
            public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) {
                //将作为条件的属性构建成路径对象
                Path<Object> custName = root.get("custName");
                Path<Object> custIndustry = root.get("custIndustry");

                //构建精准匹配的断言对象
                Predicate p1 = criteriaBuilder.equal(custName, "Google");
                Predicate p2 = criteriaBuilder.equal(custIndustry, "互联网");

                //将两个精准匹配建立And关系
                Predicate predicate = criteriaBuilder.and(p1, p2);

                return predicate;
            }
        }).get();

        System.out.println(customer);
    }

    /**
     * 模糊匹配，查询客户列表
     */
    @Test
    public void testFindAll() {
        List<Customer> list = customerRepository.findAll(new Specification<Customer>() {
            @Override
            public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) {
                //将作为条件的属性构建成路径对象
                Path<Object> custName = root.get("custName");

                //对于 gt,lt,ge,le,like 查询，需要根据路径对象指定参数的类型来构建断言
                Predicate predicate = criteriaBuilder.like(custName.as(String.class), "%o%");
                return predicate;
            }
        });

        for (Customer customer : list) {
            System.out.println(customer);
        }
    }

    /**
     * 排序查询
     * Sort.by(排序规则, 排序属性)
     */
    @Test
    public void testSort() {
        List<Customer> list = customerRepository.findAll(new Specification<Customer>() {
            @Override
            public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) {
                //在这里构建查询条件，返回null表示没有条件查询所有
                return null;
            }
        }, Sort.by(Sort.Direction.DESC, "custId"));

        for (Customer customer : list) {
            System.out.println(customer);
        }
    }

    /**
     * 分页查询
     * Spring4.x 的分页对象是 new PageRequest(page, size)
     * Spring5.x 是使用PageRequest.of(page, size)
     */
    @Test
    public void testPageable() {
        Page<Customer> page = customerRepository.findAll(new Specification<Customer>() {
            @Override
            public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) {
                //在这里构建查询条件，返回null表示没有条件查询所有
                return null;
            }
        }, PageRequest.of(1, 3)); //page页码从0开始

        System.out.println("TotalElements:" + page.getTotalElements());//总记录数
        System.out.println("TotalPages:" + page.getTotalPages());//总页数
        System.out.println("Size:" + page.getSize());//每页显示的记录数

        List<Customer> list = page.getContent();
        for (Customer customer : list) {
            System.out.println(customer);
        }
    }


    /**
     * 查询总数
     */
    @Test
    public void testCount() {
        long count = customerRepository.count(new Specification<Customer>() {
            @Override
            public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder criteriaBuilder) {
                Path<Object> custName = root.get("custName");
                Predicate predicate = criteriaBuilder.like(custName.as(String.class), "%o%");
                return predicate;
            }
        });

        System.out.println(count);
    }
}
```
# 7. 多表的关系和操作
## 7-1. 一对多
### 7-1-1. 明确表关系
- **客户表**
一家公司（Google公司是我司的客户公司）
- **联系人表**
这家公司的联系人（我司联系Google公司时要联系的人，这些人都是Google公司的员工）

### 7-1-2. 编写实体类
`Customer`
```java
/**
 * @author qinghua
 * @date 2018/9/21 10:42
 */
@Entity //声明实体类
@Table(name = "cst_customer") //实体类和表的映射关系
public class Customer {

    /**
     * 主键生成策略
     * IDENTITY : 需要数据库支持自动增长 (MySQL,SqlServer)
     * SEQUENCE : 需要数据库支持序列 (Oracle)
     * TABLE : 由JPA维护一张独立的主键表
     * AUTO : 根据数据库类型自动选择生成策略
     */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "cust_id")
    private Long custId; //客户编号（主键）
    @Column(name = "cust_name")
    private String custName; //客户名称（公司名称）
    @Column(name = "cust_source")
    private String custSource; //客户信息来源
    @Column(name = "cust_industry")
    private String custIndustry; //客户所属行业
    @Column(name = "cust_level")
    private String custLevel; //客户级别
    @Column(name = "cust_address")
    private String custAddress; //客户联系地址
    @Column(name = "cust_phone")
    private String custPhone; //客户联系电话

    /**
     * OneToMay:
     * targetEntity: 对方的字节码对象
     * JoinColumn:
     * name: 外键字段名
     * referencedColumnName: 外键对应的主键字段名
     */
    @OneToMany(targetEntity = LinkMan.class)
    @JoinColumn(name = "lkm_cust_id", referencedColumnName = "cust_id")
    private Set<LinkMan> linkMans = new HashSet<>();//联系人集合

    //getter & setter & toString
}
```
<br>
`LinkMan` 
```java
/**
 * @author qinghua
 * @date 2018/9/22 9:47
 */
@Entity
@Table(name = "cst_linkman")
public class LinkMan {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "lkm_id")
    private Long lkmId; //联系人编号
    @Column(name = "lkm_name")
    private String lkmName; //联系人姓名
    @Column(name = "lkm_gender")
    private String lkmGender; //联系人性别
    @Column(name = "lkm_phone")
    private String lkmPhone; //联系人办公电话
    @Column(name = "lkm_mobile")
    private String lkmMobile; //联系人手机
    @Column(name = "lkm_email")
    private String lkmEmail; //联系人邮箱
    @Column(name = "lkm_position")
    private String lkmPosition; //联系人职位
    @Column(name = "lkm_memo")
    private String lkmMemo; //联系人备注

    /**
     * OneToMay:
     * targetEntity: 对方的字节码对象
     * JoinColumn:
     * name: 外键字段名
     * referencedColumnName: 外键对应的主键字段名
     */
    @ManyToOne(targetEntity = Customer.class)
    @JoinColumn(name = "lkm_cust_id", referencedColumnName = "cust_id")
    private Customer customer; //联系人所属公司

    //getter & setter & toString
}
```

### 7-1-3. 持久层接口
`CustomerRepository`
```java
/**
 * @author qinghua
 * @date 2018/9/21 10:46
 */
public interface CustomerRepository extends JpaRepository<Customer, Long> {

}
```
<br>
`LinkManRepository`
```java
/**
 * @author qinghua
 * @date 2018/9/22 9:54
 */
public interface LinkManRepository extends JpaRepository<LinkMan, Long> {

}
```

### 7-1-4. 保存操作
编写测试类完成客户和联系人的保存操作
```java
/**
 * @author qinghua
 * @date 2018/9/22 10:19
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class OneToManyTest {

    @Autowired
    private CustomerRepository customerRepository;

    @Autowired
    private LinkManRepository linkManRepository;

    /**
     * 测试保存操作
     */
    @Test
    @Commit
    public void testSave() {
        //创建客户
        Customer customer = new Customer();
        customer.setCustName("Google");
        customer.setCustIndustry("互联网");

        //创建联系人
        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("拉里·佩奇");
        linkMan.setLkmPhone("13800138000");

        //建立关系
        customer.getLinkMans().add(linkMan);

        //保存联系人（没有配置级联保存时，联系人需要先保存到数据库）
        linkManRepository.save(linkMan);
        //保存客户
        customerRepository.save(customer);
    }
}
```

### 7-1-5. 放弃外键维护
现在，客户和联系人实体类中都配置了外键关系，如下所示：
`Customer`
```java
@OneToMany(targetEntity = LinkMan.class)
@JoinColumn(name = "lkm_cust_id", referencedColumnName = "cust_id")
private Set<LinkMan> linkMans = new HashSet<>();//联系人集合
```
<br>
`LinkMan`
```java
@ManyToOne(targetEntity = Customer.class)
@JoinColumn(name = "lkm_cust_id", referencedColumnName = "cust_id")
private Customer customer; //联系人所属公司
```
当两个实体类都配置外键关系的时候，保存任何一个实体类的对象到数据库中，JPA 都会去维护这个对象在数据库中的外键关系，在上面的保存示例中，我们分别保存了两个实体的对象，外键关系将会被维护两次，联系人在插入到数据库时外键已经确定，但是客户实体还要再维护一次，此时会多发送一条 update 语句，如下所示：


要解决这个问题也很简单，我们没有必要让两个实体都去维护外键，一般外键字段在哪个实体类中，就让谁来维护，也就是说多的一方来维护外键。现在，我们让客户放弃外键的维护权，如下：
`Customer`
```java
@Entity
@Table(name = "cst_customer")
public class Customer {

    //略...

    /**
     * OneToMay:
     * mappedBy: 表示当前实体放弃维护外键，表的关系配置在对方的customer属性上
     */
    @OneToMany(mappedBy = "customer")
    private Set<LinkMan> linkMans = new HashSet<>();//联系人集合

    //略...

}
```
<br>
当客户放弃外键的维护全之后，保存客户时就不会进行外键维护，只有在保存联系人时才会维护外键，这里需要注意一个问题，要想让联系人正常的进行外键维护，则必须要让联系人知道跟他关联的客户是谁，这很简单，只需要给联系人对象中的客户属性进行赋值就好了，另外，在保存联系人之前必须要保证客户已经被保存。
以下是客户放弃外键维护权之后的示例程序：
```java
/**
 * @author qinghua
 * @date 2018/9/22 10:19
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class OneToManyTest {

    @Autowired
    private CustomerRepository customerRepository;

    @Autowired
    private LinkManRepository linkManRepository;

    /**
     * 测试保存操作
     */
    @Test
    @Commit
    public void testSave() {
        //创建客户
        Customer customer = new Customer();
        customer.setCustName("Google");
        customer.setCustIndustry("互联网");

        //创建联系人
        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("拉里·佩奇");
        linkMan.setLkmPhone("13800138000");

        //建立关系（必须要让联系人知道客户是谁，因为外键是联系人在维护）
        linkMan.setCustomer(customer);

        //保存客户（在没有配置级联保存时，客户需要先保存）
        customerRepository.save(customer);

        //保存联系人
        linkManRepository.save(linkMan);

    }
}
```

### 7-1-6. 级联操作
级联配置的取值
| CascadeType | 说明 |
| --- |
| CascadeType.ALL | 所有操作都级联 |
| CascadeType.PERSIST | 级联保存 |
| CascadeType.MERGE | 级联更新 |
| CascadeType.REMOVE | 级联删除 |
<br>
级联配置在哪个实体类中？
先定位`要操作的主体`，例如保存客户的同时，级联保存客户所有的联系人，那么操作的主体就是`客户`，级联配置在客户实体类即可。
<br>
- **级联保存**
需求：保存客户的同时，保存这个客户的所有联系人
`Customer` 的级联配置：
    ```java
    @Entity
    @Table(name = "cst_customer")
    public class Customer {
        
        //略...
        
        /**
         * OneToMay:
         *      cascade = CascadeType.PERSIST ：级联保存
         */
        @OneToMany(cascade = CascadeType.PERSIST)
        @JoinColumn(name = "lkm_cust_id", referencedColumnName = "cust_id")
        private Set<LinkMan> linkMans = new HashSet<>();//联系人集合
        
        //略...
    }
    ```
<br>
测试示例代码：
    ```java
    /**
     * 级联保存
     */
    @Test
    @Commit
    public void testCascadePersist() {
        //创建客户
        Customer customer = new Customer();
        customer.setCustName("Google");
        customer.setCustIndustry("互联网");
    
        //创建联系人
        LinkMan linkMan = new LinkMan();
        linkMan.setLkmName("拉里·佩奇");
        linkMan.setLkmPhone("13800138000");
    
        //添加关系
        customer.getLinkMans().add(linkMan);
    
        //保存（此时会级联保存联系人）
        //注意：此时保存的主体是客户，所以客户不能放弃外键维护
        customerRepository.save(customer);
    }
    ```
<br>
- **级联删除**
需求：删除客户的同时删除客户的所有联系人
`Customer` 的级联配置：
    ```java
    @Entity
    @Table(name = "cst_customer")
    public class Customer {
        
        //略...
        
        /**
         * OneToMay:
         *      cascade = CascadeType.REMOVE：级联删除
         */
        @OneToMany(cascade = CascadeType.REMOVE)
        @JoinColumn(name = "lkm_cust_id", referencedColumnName = "cust_id")
        private Set<LinkMan> linkMans = new HashSet<>();//联系人集合
        
        //略...
    }
    ```
<br>
测试示例代码：
    ```java
    /**
     * 级联保存
     */
    @Test
    @Commit
    public void testCascadeRemove() {
        //查询客户
        Customer customer = customerRepository.findById(1L).get();
    
        //删除客户（此时会级联删除该客户的所有联系人）
        customerRepository.delete(customer);
    }
    ```

## 7-2. 多对多
### 7-2-1. 明确表关系
- **用户表**
张三、李四...
- **角色表**
系统管理员、普通用户...
- **用户_角色中间表**
中间表是用来关联用户和角色之间的多对多的关系的

### 7-2-2. 编写实体类
`User`
```java
/**
 * @author qinghua
 * @date 2018/9/22 15:17
 */
@Entity
@Table(name = "sys_user")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "user_id")
    private Long userId;
    @Column(name = "username")
    private String username;
    @Column(name = "password")
    private String password;

    /**
     * 当前用户拥有的所有角色
     *
     * ManyToMany
     * targetEntity: 对方的字节码对象
     *
     * JoinTable
     *      name: 中间表的名称
     *      joinColumns
     *          当前对象在中间表的外键
     *     inverseJoinColumns
     *          对方对象在中间表的外键
     */
    @ManyToMany(targetEntity = Role.class)
    @JoinTable(name = "sys_user_role",
            joinColumns = {@JoinColumn(name = "user_id", referencedColumnName = "user_id")},
            inverseJoinColumns = {@JoinColumn(name = "role_id", referencedColumnName = "role_id")}
    )
    private Set<Role> roles = new HashSet<>();
    
    //getter & setter & toString
}
```
<br>
`Role`
```java
/**
 * @author qinghua
 * @date 2018/9/22 15:18
 */
@Entity
@Table(name = "sys_role")
public class Role {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "role_id")
    private Long roleId; //角色id
    @Column(name = "role_name")
    private String roleName;//角色名称

    /**
     * 拥有当前角色的用户
     *
     * ManyToMany
     *      targetEntity: 对方的字节码对象
     *
     * JoinTable
     *      name: 中间表的名称
     *      joinColumns
     *          当前对象在中间表的外键
     *      inverseJoinColumns
     *          对方对象在中间表的外键
     */
    @ManyToMany(targetEntity = User.class)
    @JoinTable(name = "sys_user_role",
            joinColumns = {@JoinColumn(name = "role_id", referencedColumnName = "role_id")},
            inverseJoinColumns = {@JoinColumn(name = "user_id", referencedColumnName = "user_id")}
    )
    private Set<User> users = new HashSet<>();
    
    //getter & setter & toString
}
```

### 7-2-3. 持久层接口
`UserRepository`
```java
/**
 * @author qinghua
 * @date 2018/9/22 15:29
 */
public interface UserRepository extends JpaRepository<User, Long> {
    
}
```
<br>
`RoleRepository`
```java
/**
 * @author qinghua
 * @date 2018/9/22 15:29
 */
public interface RoleRepository extends JpaRepository<Role, Long> {

}
```

### 7-2-4 保存操作
```java
/**
 * @author qinghua
 * @date 2018/9/22 15:56
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class ManyToManyTest {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private RoleRepository roleRepository;

    /**
     * 保存实体
     */
    @Test
    public void testSave() {
        //创建用户
        User user = new User();
        user.setUsername("jack");
        user.setPassword("123");

        //创建角色
        Role role = new Role();
        role.setRoleName("admin");

        //建立关系（用户来维护外键关系）
        user.getRoles().add(role);

        //这里不能让双方都进行外键维护，否则会往中间表中插入两条关联记录，导致主键冲突
        //role.getUsers().add(user);

        //在没有配置级联保存之前，需要先将角色保存到数据库
        roleRepository.save(role);
        userRepository.save(user);
    }
}
```

### 7-2-5. 放弃外键维护
外键的放弃和一对多的配置是一样的，在一对多关系中，我们经常让一的一方放弃外键维护，由多的一方来维护。在多对多关系中谁放弃外键维护取决于谁是被动的，例如前面的添加操作，我们将角色添加到用户的角色列表中，此时，角色就是被动的，谁是被动的，谁就放弃外键的维护权。
`Role`
```java
@Entity
@Table(name = "sys_role")
public class Role {

    //略...

    /**
     * 拥有当前角色的用户
     *
     * ManyToMany
     * mappedBy: 当前实体放弃外键维护，外键的关系配置在对方的 roles 属性上
     *
     */
    @ManyToMany(mappedBy = "roles")
    private Set<User> users = new HashSet<>();

    //略...
}
```

### 7-2-6. 级联操作
级联操作的配置和一对多的配置也是一样的，级联的配置写在哪个实体类中呢？仍然要先定位操作的主体，例如：保存用户，同时保存该用户所有的角色，那么操作的主体就是用户，级联的配置写在用户的实体类中。
`User`
```java
@Entity
@Table(name = "sys_user")
public class User {

    //略...

    /**
     * 当前用户拥有的所有角色
     *
     * ManyToMany
     * cascade: 级联操作的类型
     */
    @ManyToMany(targetEntity = Role.class, cascade = CascadeType.ALL)
    @JoinTable(name = "sys_user_role",
            joinColumns = {@JoinColumn(name = "user_id", referencedColumnName = "user_id")},
            inverseJoinColumns = {@JoinColumn(name = "role_id", referencedColumnName = "role_id")}
    )
    private Set<Role> roles = new HashSet<>();
    
    //略...
}
```

# 8. 对象导航查询
## 8-1. 对象导航查询和延迟加载
对象导航查询是通过对象调用方法来查询关联对象的一种查询方式，例如通过客户查询客户的联系人
```java
/**
 * @author qinghua
 * @date 2018/9/22 17:12
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class ObjectQueryTest {

    @Autowired
    private CustomerRepository customerRepository;

    /**
     * 通过客户查询客户的所有联系人
     */
    @Test
    @Transactional //no session
    public void testOneFindMany() {
        Customer customer = customerRepository.findById(1L).get();
        Set<LinkMan> linkMans = customer.getLinkMans();//默认延迟加载
        System.out.println(linkMans.size());
    }
}
```
<br>
默认情况下，`从一的一方查询多的一方`使用的是`延迟加载`，如果需要立即加载，修改注解配置中的 `fetch` 属性即可。
例如，上面的对象导航查询示例是通过客户来查询联系人，我们希望查询的联系人时立即被加载出来的，这就需要在客户的联系人属性上进行配置的修改
`Customer`
```java
@Entity
@Table(name = "cst_customer")
public class Customer {

    //略...

    /**
     * OneToMay
     * fetch 加载策略
     * FetchType.EAGER: 立即加载
     */
    @OneToMany(cascade = CascadeType.ALL, fetch = FetchType.EAGER)
    @JoinColumn(name = "lkm_cust_id", referencedColumnName = "cust_id")
    private Set<LinkMan> linkMans = new HashSet<>();//联系人集合

    //略...
}
```

## 8-2. 从多放查询一方
注意，从多的一方查询一的一方默认采用的是立即加载，因为查询出来的只有一个对象，延迟加载的意义不大。如果需要延迟加载，也可以通过 `fetch` 配置更改加载策略。
```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class ObjectQueryTest {

    @Autowired
    private CustomerRepository customerRepository;

    @Autowired
    private LinkManRepository linkManRepository;

    /**
     * 通过联系人查询客户
     */
    @Test
    public void testManyFindOne() {
        LinkMan linkMan = linkManRepository.findById(1L).get();
        Customer customer = linkMan.getCustomer();//默认立即加载
        System.out.println(customer);
    }

}
```