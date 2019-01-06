# java

- public static void ... : 修饰静态方法，直接使用类名.方法名调用。
- StringBuffer（字符串缓冲区）: 创建内容和长度可变的字符串。
- foreach: 主要用于遍历数组或集合。  
  for（元素类型t 元素变量x ：遍历对象obj）｛  
  　　　　引用了x的java语句；　　　　  
  　　　　｝
- Stream  
  List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);  
  　number.parallesStream().forEach(out::println);  
  　Stream具有平行处理能力，处理的过程会分而治之，也就是将一个大任务切分成多个小任务，这表示每个任务都是一个操作。
- Lambda表达式。  
  参考资料地址：https://blog.csdn.net/dd864140130/article/details/50603420