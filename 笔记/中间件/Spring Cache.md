## 介绍

Spring Cache 是一个框架，实现了基于注解的缓存功能，只需要简单添加一个注解，就能实现缓存功能。

Spring Cache提供了一层抽象，底层可以切换不同的cache实现。具体就是通过CacheManager接口来统一不同的缓存技术

## 常用注解

~~~java
@EnableCaching			 开启注解功能
@Cacheable					方法执行前spring先查看缓存中是否有数据，有则返回缓存数据，没有调用方法将返回值放到缓存中
@CachePut					 将方法的返回值放到缓存中
@CacheEvict					 将一条或多要数据从缓存中删除
~~~

在Springboot项目中，使用缓存技术只需在项目中导入相关缓存技术的依赖包，并在启动类上使用`@EnableCaching` 开启缓存支持即可

例如：使用Redis作为缓存技术，只需导入Spring data Redis

> condition 可能没有 result这个属性，所以可能需要换成unless（点源码可以查看）

~~~java
// condition 条件成立才缓存，此处意为 返回的结果不是null在缓存
// unless 条件不成立才缓存 举例：unless = "#result == null"
@Cacheable(value = "userCache",key = "#id",condition = "#result != null")
public User getById(@Pathvariable Long id){
    User user = userService.getById(id);
}

@Cacheable(value = "userCache",key = "#user.id + '_' + #user.name")
publlic List<User> list(User user){
    List<User> list = null;
    if(user.getId() != null && user.getName() != null){
       list =  // 执行查询
    }
    return list;
}
~~~



~~~java
/**
*CachePut：将方法返回值放入缓存
*value：缓存的名称， 每个缓存名称下面可以有多个key
*key：缓存的key
*/
@CachePut(value = "userCache",key = "#user.id")
public User save(User user){
    userService.sava(user);
    return user;
}
~~~

~~~java
/**
*CacheEvict：清理指定缓存
*value：缓存的名称，每个缓存名称下面可以有多个key
*key：缓存的key
*allEntries：删除userCache下的所有缓存
*/
//@CacheEvict(value = "userCache",key = "#p0")
//@CacheEvict(value = "userCache",key = "#root.args[0]",allEntries = true)
@CacheEvict(value = "userCache",key = "#id")
public void delete(@Pathvariable Long id){
    userService.removeById(id);
}

//@CacheEvict(value = "userCache",key = "#p0.id")
//@CacheEvict(value = "userCache",key = "#root.args[0].id")
//@CacheEvict(value = "userCache",key = "#result.id")
@CacheEvict(value = "userCache",key = "#user.id")
public void update(User user){
    userService.updateById(user);
}
~~~

## 使用redis做缓存

1. 导入依赖

~~~xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
~~~

2. 启动类上加入@EnableCaching注解，开启缓存注解功能
3. 在对应方法上加对应注解

~~~
和上面使用举例一样
~~~

