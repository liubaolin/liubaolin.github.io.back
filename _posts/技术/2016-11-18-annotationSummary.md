---
layout: post
title: 注解总结
category: 技术
tags: 注解
keywords:
description:
---
### 一点总结

* 注解和接口最明显的区别是在@符号上，即注解定义时为@interface，接口为interface。

* 注解的元素看起来就像接口的方法，唯一的区别就是你可以为其指定默认值。

* 在Java中实现注解一般是按照以下步骤：定义注解—>实现注解处理器—>使用注解。注解处理器的实现方式分为两种，一种是用反射的方式实现，另一种则是使用apt工具实现，下面我们通过例子来学习这两种实现方式。

### 注解的一些规则

#### 注解可以使用的元素
 * 所有基本类型（int、float、boolean等）。
 * String
 * Class
 * enum
 * Annotation
 * 以上类型的数组


#### 默认值限制

 * 编译器对元素的默认值有些过分挑剔，首先元素不能有不确定的值，即元素必须要么有默认值，要么在使用注解的时候提供元素的值。
 *  其次，对于非基本类型的元素，无论是在源码声明时，或者是在注解接口中定义默认值时，都不能用null作为其值。这个约束使得处理器很难表现一个元素的存在或者缺失的状态，因为在每个注解的声明中，所有的元素都存在并且具有相应的值，那么我们怎么样表现元素的存在或缺失状态呢？我们可以定义一些特殊值，例如空字符串或者负数，用以表示某个元素不存在。


#### 注解不支持继承

 * 不能使用关键字extends来继承某个@interface


#### 注解的方法不能有参数

### 使用反射方式实现注解处理器

    //1.自定义注解
    @Target(ElementType.METHOD)
    @Retention(RetentionPolicy.RUNTIME)
    public @interface ReflectAnnotation {
        String auther() default "richey";
        String date();
        int version() default 1;
    }
    //2.注解解析器
    public class ReflectAnnotationResolver {
        public static void resolverReflectAnnotation(Class<?> clazz){
            for (Method method : clazz.getDeclaredMethods()){
                ReflectAnnotation reflectAnnotation = method.getAnnotation(ReflectAnnotation.class);
                if(reflectAnnotation != null){
                    System.out.println("ReflectAnnotation---author:"+reflectAnnotation.auther()+"/nReflectAnnotation---date:"+reflectAnnotation.date());
                }
            }
        }
    }
    //3.测试类
    public class Client {
    @ReflectAnnotation(auther = "richey",date = "2016-11-18",version = 1)
    public void testAll(){
    }
    //因为date没有给默认值,所以这里必须赋值
    @ReflectAnnotation(date = "2016-11-18")
    public void testDate(){
    }
    public static void main(String[] args) {
        ReflectAnnotationResolver.resolverReflectAnnotation(Client.class);
    }
}
