# Spring
## IOC/DI依赖注入
* 注解：@Compnonet，@Autowired
* 需导入jar包：spring-context
* spring配置文件中需要添加：<context:component-scan base-package="com.*.*">
  其中，com.*.*为需要spring扫描的路径
* 将扫描路径下的class之上加@Component，表示此类是bean
* 将扫描路径下的class的某个属性之上加@Autowired，表示该属性将通过spring自动注入该类中
## AOP面向切面编程
* 注解：@Aspect,@Around,@PointCut
* 需导入jar包：aspectjtools或者aspectjrt和aspectjweaver
* spring配置文件中需添加：\<aop:aspectj-autoproxy/>
* 在切面class之上加@Aspect，表示这是一个切面
* 在切面类中加@Around(value="execution(* com.how2java.service.ProductService.*(..))"),表示对com.how2java.service.ProductService 这个类中的所有方法进行切面操作
代码如下：
```java
package com.how2java.aspect;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoggerAspect { 
	
	@Around(value = "execution(* com.how2java.service.ProductService.*(..))")
	public Object log(ProceedingJoinPoint joinPoint) throws Throwable {
		System.out.println("start log:" + joinPoint.getSignature().getName());
		Object object = joinPoint.proceed();
		System.out.println("end log:" + joinPoint.getSignature().getName());
		return object;
	}
}
```
  也可以先加入一个切点表达式，@PointCut(value = "execution()"),代码如下：
  ```java
  @Aspect
  @Component
  public class LoggerAspect {

    @Pointcut(value = "execution(* com.husky.service.ProductService.*(..))")
    public void pointcut(){

    }
    @Around("pointcut()")
    public Object log(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("start log:" + joinPoint.getSignature().getName());
        Object o = joinPoint.proceed();
        System.out.println("end log:" + joinPoint.getSignature().getName());
        return o;
    }
}
  ```
------




## Spring常用注解  
  @Controller(value=" ") ,其中value为注入的bean的名字。  
  @Service  
  @Reposiory  
  @Component  
  @RestController：表示该Controller不返回视图，而仅返回数据。

------

# SprinvMVC

- SpringMVC常用注解：   
  @RequestMapping(value=" ", method=" ")

