# FastJson

阿里的,下载jar包

1. 把java对象转成json字符串

   ```js
   Student s = new Student(1,"张三",20);
   JSON.toJSONString(s,true);//true或者false 是json的格式
   ```

   

2. 把json字符转成java对象

   ```
   String s = "{id:12,name:'lisi'}"
   JSON.parseObject(s,Student.class)
   ```

   

3. 任意集合转json

   ```
   JSON.toJSONString(list);
   ```

4. 字符串重新解析json

   ```sh
   JSON.parse(String str)
   ```

5. map转json

   ```java
   
   JSONObject json = new JSONObject(map);
   String jsonString = JSON.toJSONString(json);
   response.getWriter().print(jsonString);
   ```

   

# Jackson

简单基于java的应用库，将java对象转换成json，同样也可以将json变成java

依赖包

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-core</artifactId>
    <version>2.9.8</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.8</version>
</dependency>
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-annotations</artifactId>
    <version>2.9.8</version>
</dependency>
```

- json转java对象

  ```java
  
  ```

  