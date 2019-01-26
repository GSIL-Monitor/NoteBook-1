# SpringBoot
## 注解
* **@RequestParam**，获取请求参数
其有三个属性：value、required、defaultValue
value:请求参数名（必须配置）,即入参的请求参数的名字，如username表示请求的参数区中的名字为username的参数的值将传入
required：是否必需，默认为true，即请求中必须包含该参数，如果没有包含，将会抛出异常
defaultValue：默认值，如果设置了该值，requred将自动设为false
* **@PathVariable**，获取请求路径中参数的值
通过 @PathVariable 可以将 URL 中占位符参数绑定到控制器处理方法的入参中：URL 中的 {xxx} 占位符可以通过@PathVariable("xxx") 绑定到操作方法的入参中。
代码示例：  
```java
//@PathVariable可以用来映射URL中的占位符到目标方法的参数中
@RequestMapping("/testPathVariable/{id}")
    public String testPathVariable(@PathVariable("id") Integer id)
    {
        System.out.println("testPathVariable:"+id);
        return SUCCESS;

```
