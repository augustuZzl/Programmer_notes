## 配置文件

Spring Boot 使用全局配置文件，配置文件可以修改SB底层封装好的默认值，配置文件名是固定的：

- application.properties
- application.yml

### YAML

语法格式：key: value (注意：冒号后面必须有一个空格)，键和值都是大小写敏感的。

以空格的缩进来表示层级关系，左对齐的一列数据，便是同一级的：

```xml
server:
  port: 8888
  servlet:
    context-path: /SB
```

### 配置文件值注入

#### @ConfigurationProperties

先在 application 中定义一些键值：

```yml
person:
  name: zzl
  boos: false
  map: { k1: v1, k2: v2}
  list:
    - man
    - 18
  dog:
    name: wangwang
    age: 2
```

在 application.properties 中要写成这个样子：

> 在IDEA中设置编码：settings-->file encoding -->property-->utf-8 ,勾选转成ascii

```properties
person.name=zzl
person.boos=false
person.map.k1=v1
person.map.k2=v2
person.list=man,18
person.dog.name=wangwang
person.dog.age=2
```

下面就可以定义对应的 javaBean 了：

```java
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    
    private String name;
    private Boolean boos;
    private Map<String, Object> map;
    private List<Object> list;
    private Dog dog;

}
```

导入配置文件处理器，以后编写配置就有提示了

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
</dependency>
```































