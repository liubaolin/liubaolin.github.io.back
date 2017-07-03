---
layout: post
title: spring-Data-Jpa
category: 技术
tags: 框架
keywords:
description:
---
# 目录

## 1. Spring Data 简介
    1.1 什么是Spring Data ？
    1.2 Spring Data 概览
    1.3 Spring Data应用场景

## 2. 什么是Spring Data
    2.1 传统的JDBC访问数据库
    2.2 Spring JdbcTemplate
    2.3 弊端分析

## 3. Spring Data快速入门
    3.1 JPA简介
    3.2 开发环境搭建
    3.3 Quick Start

## 4. Spring Data JPA进阶
    4.1 关于Repository接口
    4.2 Repository子接口
    4.3 Repository查询方法定义规则和使用
    4.4 Query注解使用
    4.5 更新操作整合事务使用

## 5. Spring Data JPA高级
    5.1 CrudRepository接口使用详解
    5.2 PagingAndSortingRepository接口使用详解
    5.3 JpaRepository接口使用详解
    5.4 JpaSpecificationExecutor接口使用详解

## 6. 总结


# 1.Spring Data 简介

## 1.1 什么是Spring Data？

> Spring Data’s mission is to provide a familiar and consistent, Spring-based programming model for data access.


## 1.2 Spring Data 概览

![image](http://orybhlel7.bkt.clouddn.com/image/png/SpringDataOverview.png)

## 1.3 Spring Data 应用场景

* Spring Data包含多个子项目：
    * Spring Data JPA
    * Spring Data Mongo DB
    * Spring Data Redis
    * Spring Data Solr
    * ......
    * 具体查看官网

* 目的：减少数据访问层的开发工作量

# 2. 什么是Spring Data

## 2.1 传统的JDBC访问数据库
![image](http://orybhlel7.bkt.clouddn.com/SpringData/JPA/JDBC/SpringDataJpaJdbc.png)

* 环境准备

       // 数据库驱动等依赖
        <dependency>
         	  <groupId>mysql</groupId>
          	 <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
         	  <groupId>junit</groupId>
         	  <artifactId>junit</artifactId>
        </dependency>
       // 数据表准备
        create schema spring_data;
        create table student(
           id int not null auto_increment,
           name varchar(20) not null,
           age int not null,
           primary key(id)
        )
        insert into student (name,age) values("Richey",18);
        insert into student (name,age) values("zhangSan",28);

* 开发Demo

        编写JDBCUtil处理数据库连接
        	获取连接
        	释放DB相关的资源（resultSet，Statement，Connection）

        建立领域对象DO，DAO

        public class Student implements Serializable{

           private int id;

           private String name;

           private int age;
        }

* 增删改查

![image](http://orybhlel7.bkt.clouddn.com/SpringData/JPA/JDBC/jdbcCRUD.png)

* 思考使用传统的JDBC方式的优点和弊端
    * 每次查询都有许多重复性的工作
    * 开发繁琐，效率低下
    * 使用原生sql，很灵活，查询效率高

## 2.2 Spring JdbcTemplate

* Spring对数据库的操作在jdbc上面做了深层次的封装，使用spring的注入功能，可以把DataSource注册到JdbcTemplate之中。
* 使用JdbcTemplate访问数据库

        @Override
        public List<Student> query() {
           final List<Student> students = new ArrayList<Student>();
           String sql = "SELECT id,name,age FROM student";
           jdbcTemplate.query(sql, new RowCallbackHandler(){
        @Override
         public void processRow(ResultSet resultSet) throws
         SQLException {
                   int id = resultSet.getInt("id");
                   String name = resultSet.getString("name");
                   int age = resultSet.getInt("age");
                   students.add(new Student(id,name,age));
               }
           });
           return students;
        }

        @Override
        public void save(Student student) {
           String sql = "INSERT  INTO student(name,age)
        VALUES(?,?) ";
           jdbcTemplate.update(sql,
        new Object[]{
        student.getName(),
        student.getAge()}
        );
        }

## 2.3 弊端分析

* DAO层要编写很多的代码
* DAO层有很多重复的代码
* 如果要实现分页等功能，需要进行封装，工作量较大
* 需要对结果集进行处理，存在硬编码

# 3. Spring Data JPA快速入门

## 3.1 JPA简介

* JPA是什么
    * Java持久化API（Java Persistence API）
    * 对象-关系映射（ORM）是JPA的一个主要组件，并且JPA希望整合ORM技术，实现天下归一
    * 是一个独立的规范，JPA的目标之一是制定一个可以由很多供应商实现的API，并且开发人员可以编码来实现该API，而不是使用私有供应商特有的API
    * 目前主要包含3方面
        * ORM映射元数据
        * API用来操纵实体对象，简化CRUD操作
        * JPQL面向对象的查询语言

* 没有JPA之前我们是怎样持久化对象的
    * JPA第一个版本出现时，ORM已经发展了10年
    * Hibernate、TopLink以及其它持久化API已经在应用程序中存在，并完美的满足了应用程序的需求。

* 为什么需要JPA，为什么不能基于开源产品（比如Hibernate）来执行标准化
    * 一个标准远比一个产品更深入，单一产品不能体现一个规范
    * 一个规范的意图是它由不同的供应商实现，并且由不同的产品提供标准的接口和语义，不能把应用程序耦合到任何一个款产品
    * 把标准绑定在一个快源项目，无疑会使项目管理变的混乱
    * 技术选型是一项投资，使用标准技术（规范），调整的灵活性 比较大

## 3.2 开发环境搭建#Quick Start

        <!--1.配置数据源-->
        <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
           <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
           <property name="username" value="root"/>
           <property name="password" value="root"/>
           <property name="url" value="jdbc:mysql://localhost:3306/spring_data"/>
        </bean>

        <!--2.配置entityManagerFactory-->
        <bean id="entityManagerFactory"
        class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean">
           <property name="dataSource" ref="dataSource"/>
           <property name="jpaVendorAdapter">
               <bean class="org.springframework.orm.jpa.vendor.HibernateJpaVendorAdapter"/>
           </property>
           <property name="packagesToScan" value="com.richey"/>
           <property name="jpaProperties">
               <props>
                   <prop key="hibernate.ejb.naming_strategy">
        org.hibernate.cfg.ImprovedNamingStrategy</prop>
                   <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>
                   <prop key="hibernate.show_sql">true</prop>
                   <prop key="hibernate.hbm2ddl.auto">update</prop>
               </props>
           </property>
        </bean>

        <!--3.配置事务管理器-->
        <bean id="transactionManager" ="org.springframework.orm.jpa.JpaTransactionManager">
        <property name="entityManagerFactory" ref="entityManagerFactory"/>
        </bean>

        <!--4.配置支持注解的事务-->
        <tx:annotation-driven transaction-manager="transactionManager"/>

        <!--5.配置Spring Data-->
        <jpa:repositories base-package="com.richey" entity-manager-factory-ref="entityManagerFactory"/>

        <context:component-scan base-package="com.richey"/>

        <!--1.写一个接口-->
        public interface CustomerRepository extends Repository<Customer,Long>{
         	  public Customer findByName(String name);
        }
        <!--2.测试类-->
        @Test
        public void testFindByName() {
         	  Customer customer = customerRepository.findByName("richey");
         	  System.out.println("id:" + customer.getId() + " name:" + customer.getName());
        }

        //只需要两步，是不是很溜？

# Spring Data JPA 进阶

## 4.1 关于Repository接口

* Repository接口是Spring Data的核心接口，不提供任何方法

        public interface Repository<T, ID extends Serializable>{}

    * 标记接口

* @RepositoryDefinition注解的使用

        //用注解代替 extend Repository
        @RepositoryDefinition(domainClass = Customer.class, idClass = Long.class)
        public interface CustomerRepository


## 4.2 Repository子接口
* CrudRepository，实现了CRUD相关的方法

* PagingAndSortingRepository extend CrudRepository
    * 继承了CRUD接口，实现了分页排序相关的方法

* JpaRepository extends PagingAndSortingRepository
    * 实现JPA规范相关的方法

## 4.3 Repository查询方法定义规则和使用
* Spring Data中查询方法名称的定义规则

* 使用Spring Data完成复杂查询方法名称的命名

* http://docs.spring.io/spring-data/jpa/docs/1.10.11.RELEASE/reference/html/#repositories.query-methods

        And --- 等价于 SQL 中的 and 关键字，比如 findByUsernameAndPassword(String user, Striang pwd)；
        Or --- 等价于 SQL 中的 or 关键字，比如 findByUsernameOrAddress(String user, String addr)；
        Between --- 等价于 SQL 中的 between 关键字，比如 findBySalaryBetween(int max, int min)；
        LessThan --- 等价于 SQL 中的 "<"，比如 findBySalaryLessThan(int max)；
        GreaterThan --- 等价于 SQL 中的">"，比如 findBySalaryGreaterThan(int min)；
        IsNull --- 等价于 SQL 中的 "is null"，比如 findByUsernameIsNull()；
        IsNotNull --- 等价于 SQL 中的 "is not null"，比如 findByUsernameIsNotNull()；
        NotNull --- 与 IsNotNull 等价；
        Like --- 等价于 SQL 中的 "like"，比如 findByUsernameLike(String user)；
        NotLike --- 等价于 SQL 中的 "not like"，比如 findByUsernameNotLike(String user)；
        OrderBy --- 等价于 SQL 中的 "order by"，比如 findByUsernameOrderBySalaryAsc(String user)；
        Not --- 等价于 SQL 中的 "！ ="，比如 findByUsernameNot(String user)；
        In --- 等价于 SQL 中的 "in"，比如 findByUsernameIn(Collection<String> userList) ，方法的参数可以是 Collection 类型，也可以是数组或者不定长参数；
        NotIn --- 等价于 SQL 中的 "not in"，比如 findByUsernameNotIn(Collection<String> userList) ，方法的参数可以是 Collection 类型，也可以是数组或者不定长参数；

* 思考：按照方法命名规则来使用的弊端
    * 方法名会比较长
    * 对于一些复杂的查询很难实现

## 4.4 Query注解使用

* DEMO
        @Query("select c from Customer  c where id = (select max(id) from Customer)")
        public Customer getCustomerByMaxId();

        @Query("select c from Customer  c where c.name = ?1 and c.age = ?2")
        public List<Customer> queryParams(String name, String age);

        @Query("select c from Customer  c where c.name = :name and c.age = :age")
        public List<Customer> queryParams2(@Param("name") String name,@Param("age") String age);

        @Query("select c from Customer c where c.name like %?1%")
        public List<Customer> queryList1(String name);

        @Query("select c from Customer c where c.name like %:name%")
        public List<Customer> queryList2(@Param("name") String name);

* 支持原生SQL

        @Query(nativeQuery = true, value = "select count(1) from customer")
        public long getCount();

        @Query(nativeQuery = true, value = "select id,name,age from customer")
        public List<Customer> getNativeCustomer();

        @Query(nativeQuery = true, value = "select id,name,age from customer")
        public List<Object[]> getNativeCustomer2();

* 我们可以用一个@Entity注解过的实体类接收原生sql 的查询结果，且查询字段和实体表字段必须一一对应，不能多也不能少。但是不能用一个普通的POJO去接原生sql的查询结果
* @Query应该是工作中用的最多的方式

## 4.5 更新操作整合事务使用

* @Modifying注解的使用
* @Modifying注解结合@Query注解执行更新操作
* @Transactional在Spring Data中的使用

        @Modifying
        @Query("update Customer c set c.age = :age where c.id = :id")
        public void update(@Param("id") Long id,@Param("age") Integer age);

        @Transactional
        public void update(Long id, Integer age) {
           customerRepository.update(id, age);
        }

* 总结：事务在Spring Data中的使用
    * 事务一般是在Service层的
    * @Query、@Modifying、@Transaction一起使用才能进行更行或者删除操作


# 5. Spring Data JPA高级

## 5.1 CrudRepository接口使用详解
- save(entity)
- save(entities)
- findOne(id)
- exists(is)
- findAll()
- delete(id)
- delete(entity)
- delete(entities)
- deleteAll()

        public interface CustomerCrudRepository extends CrudRepository<Customer,Long> {}

        @Transactional
        public void saveCustomers(List<Customer> customerList) {
           customerCrudRepository.save(customerList);
        }

        @Test
        public void testSaveCustomers() {
           List<Customer> customers = new ArrayList<Customer>();
         for(int i=0;i<100;i++) {
             Customer customer = new Customer();
             customer.setName("richey" + i);
             customer.setAge(1 + i);
             customers.add(customer);
         }
           customerService.saveCustomers(customers);
        }

## 5.2 PagingAndSortingRepository接口使用详解
* 该接口包含分页和排序的功能
* 带排序的查询：findAll（Sort sort）
* 带排序的分页查询：findAll（Pageable pageable）
* 分页查询DEMO：

        public interface CustomerPagingAndSortingRepository
        extends PagingAndSortingRepository<Customer,Long> {}

        @Test
        public void testPage() {
           //page:index是从0开始的
           Pageable pageable = new PageRequest(0,5);
           Page<Customer> page = customerPagingAndSortingRepository.findAll(pageable);
           System.out.println("总共有多少页:"+page.getTotalPages());
           System.out.println("总共有多少条记录:"+page.getTotalElements());
           System.out.println("查询的当前第几页:"+page.getNumber()+1);
           System.out.println("查询的当前页面的集合:"+page.getContent());
           System.out.println("当前页面的记录数:" + page.getNumberOfElements());
        }

* 排序查询DMOE：

        @Test
        public void tesSort() {

           Sort.Order order = new Sort.Order(Sort.Direction.DESC, "id");
           Sort sort = new Sort(order);

           Pageable pageable = new PageRequest(0,5,sort);
           Page<Customer> page = customerPagingAndSortingRepository.findAll(pageable);
           System.out.println("总共有多少页:"+page.getTotalPages());
           System.out.println("总共有多少条记录:"+page.getTotalElements());
           System.out.println("查询的当前第几页:"+page.getNumber()+1);
           System.out.println("查询的当前页面的集合:"+page.getContent());
           System.out.println("当前页面的记录数:" + page.getNumberOfElements());
        }


## 5.3 JpaRepository接口使用详解

- findAll
- findAll（Sort sort）
- save（entities）
- flush
- deleteInBatch（entities）


## 5.4 JpaSpecificationExecutor接口使用详解


- T findOne(Specification<T> spec);

- List<T> findAll(Specification<T> spec);

- Page<T> findAll(Specification<T> spec, Pageable pageable);

- List<T> findAll(Specification<T> spec, Sort sort);

- long count(Specification<T> spec);

      public interface CustomerSpecificationExecutorRepository extends JpaSpecificationExecutor<Customer> ,JpaRepository<Customer,Long>{
      }

       /**
       * 1)分页
       * 2）排序
       * 3）查询条件
       */
      @Test
      public void testQuery() {
          Sort.Order order = new Sort.Order(Sort.Direction.ASC, "id");
          Sort sort = new Sort(order);
          Pageable pageable = new PageRequest(0,5,sort);

          /**
           * root 就是我们要查询的类型（Customer）
           * query：添加查询条件
           * cb：构建Predicate
           */
          Specification<Customer> specification = new Specification<Customer>() {
              @Override
              public Predicate toPredicate(Root<Customer> root,
                                           CriteriaQuery<?> query,
                                           CriteriaBuilder cb) {
                  //root (customer(age))
                  Path path = root.get("age");
                  return cb.gt(path, 50);
              }
          };
          Page<Customer> page = customerSpecificationExecutorRepository.findAll(specification, pageable);
          System.out.println("总共有多少页:"+page.getTotalPages());
          System.out.println("总共有多少条记录:"+page.getTotalElements());
          System.out.println("查询的当前第几页:"+page.getNumber()+1);
          System.out.println("查询的当前页面的集合:"+page.getContent());
          System.out.println("当前页面的记录数:" + page.getNumberOfElements());
      }

      @Test
      public void testQuery2() {
          //混合条件查询
          Specification<Customer> specification = new Specification<Customer>() {
              @Override
              public Predicate toPredicate(Root<Customer> root,
                                           CriteriaQuery<?> query,
                                           CriteriaBuilder cb) {
                  Path<String> exp1 = root.get("name");
                  Path<Integer> exp2 = root.get("age");
                  Predicate predicate = cb.and(cb.like(exp1, "richey"), cb.lessThan(exp2, 30));
                  return predicate;
              }
          };

          //多表查询
          Specification<Customer> specification2 = new Specification<Customer>() {
              @Override
              public Predicate toPredicate(Root<Customer> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
                  Join<Customer, Company> join = root.join("company", JoinType.INNER);
                  Path<String> exp1 = join.get("name");
                  return cb.like(exp1, "richey");
              }
          };

          customerSpecificationExecutorRepository.findAll(specification2);

      }
