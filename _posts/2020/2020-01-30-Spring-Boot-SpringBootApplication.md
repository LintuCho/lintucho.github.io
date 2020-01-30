---
layout: post
title: Spring Boot: 入口类和@SpringBootApplication
date: 2020-01-30T00:00:00.000Z
author: Hanx
catalog:    true
tags:
  - Spring
  - SpringBoot

---

Spring Boot 通常有一个Application的入口类，通过其中的 main 方法启动Spring Boot 应用项目。代码如下：

```java
    package com.example.demo;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    @SpringBootApplication
    public class DemoApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    
    }
```

## @SpringBootApplication

`@SpringBootApplication` 是 SpringBoot 的核心注解，它是一个组合注解，源码如下：

```java
    /**
     * Indicates a {@link Configuration configuration} class that declares one or more
     * {@link Bean @Bean} methods and also triggers {@link EnableAutoConfiguration
     * auto-configuration} and {@link ComponentScan component scanning}. This is a convenience
     * annotation that is equivalent to declaring {@code @Configuration},
     * {@code @EnableAutoConfiguration} and {@code @ComponentScan}.
     *
     * @author Phillip Webb
     * @author Stephane Nicoll
     * @since 1.2.0
     */
    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Inherited
    @SpringBootConfiguration
    @EnableAutoConfiguration
    @ComponentScan(excludeFilters = {
    		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
    		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
    public @interface SpringBootApplicatio
```



`@SpringBootApplication` 注解主要组合了：@Configuration、@EnableAutoConfiguration、@ComponentScan，其中 @EnableAutoConfiguration 让 SpringBoot 根据类路径中的 jar 包依赖为当前项目进行自动配置。

@EnableAutoConfiguration 的源码如下：

```java
    /**
     * Enable auto-configuration of the Spring Application Context, attempting to guess and
     * configure beans that you are likely to need. **Auto-configuration classes are usually**
     * **applied based on your classpath and what beans you have defined**. For example, if you
     * have {@code tomcat-embedded.jar} on your classpath you are likely to want a
     * {@link TomcatServletWebServerFactory} (unless you have defined your own
     * {@link ServletWebServerFactory} bean).
     * <p>
     * When using {@link SpringBootApplication}, the auto-configuration of the context is
     * automatically enabled and adding this annotation has therefore no additional effect.
     * <p>
     * Auto-configuration tries to be as intelligent as possible and will back-away as you
     * define more of your own configuration. You can always manually {@link #exclude()} any
     * configuration that you never want to apply (use {@link #excludeName()} if you don't
     * have access to them). You can also exclude them via the
     * {@code spring.autoconfigure.exclude} property. Auto-configuration is always applied
     * after user-defined beans have been registered.
     * <p>
     * The package of the class that is annotated with {@code @EnableAutoConfiguration},
     * usually via {@code @SpringBootApplication}, has specific significance and is often used
     * as a 'default'. For example, it will be used when scanning for {@code @Entity} classes.
     * It is generally recommended that you place {@code @EnableAutoConfiguration} (if you're
     * not using {@code @SpringBootApplication}) in a root package so that all sub-packages
     * and classes can be searched.
     * <p>
     * Auto-configuration classes are regular Spring {@link Configuration} beans. They are
     * located using the {@link SpringFactoriesLoader} mechanism (keyed against this class).
     * Generally auto-configuration beans are {@link Conditional @Conditional} beans (most
     * often using {@link ConditionalOnClass @ConditionalOnClass} and
     * {@link ConditionalOnMissingBean @ConditionalOnMissingBean} annotations).
     * 1. 自动装载 classpath 以及自定义的 bean
     * 2. 用户定义的 bean 注册后才会进行自动配置
     * 
     *
     * @author Phillip Webb
     * @author Stephane Nicoll
     * @see ConditionalOnBean
     * @see ConditionalOnMissingBean
     * @see ConditionalOnClass
     * @see AutoConfigureAfter
     * @see SpringBootApplication
     */
    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Inherited
    @AutoConfigurationPackage
    @Import(AutoConfigurationImportSelector.class)
    public @interface EnableAutoConfiguratio
```



AutoConfigurationImportSelector 使用 `org.springframework.boot.autoconfigure.AutoConfigurationMetadataLoader#loadMetadata(java.lang.ClassLoader)` 扫描 factories 文件的 jar 包

注解的依赖关系如下图所示：

![SpringBootApplication-diagram.png](/_posts/2020/assets/SpringBootApplication-diagram-0384272.png)

**## @Condition**

在任意的 AutoConfiguration 文件中  存在注解 org.springframework.boot.autoconfigure.condition ， 具体注解如下：

[Conditional注解](https://www.notion.so/c35d14932c884b28a21d83b25314ab33)

以 `@ConditionalOnWebApplication` 注解为例

​    /**

​     \* {@link Conditional @Conditional} that matches when the application is a web

​     \* application. By default, any web application will match but it can be narrowed using

​     \* the {@link #type()} attribute.

​     *

​     \* @author Dave Syer

​     \* @author Stephane Nicoll

​     \* @since 1.0.0

​     */

​    @Target({ ElementType.TYPE, ElementType.METHOD })

​    @Retention(RetentionPolicy.RUNTIME)

​    @Documented

​    @Conditional(OnWebApplicationCondition.class)

​    public @interface ConditionalOnWebApplication