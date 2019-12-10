#### 添加 MongoDB 依赖并配置

```xml
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

```yaml
spring:
    data:
        mongodb:
          uri: mongodb://192.168.56.101:27017/sysblog
```

#### 建立图片实体

```java
import org.bson.types.Binary;

@Document
public class UploadFile {

    @Id
    private String id;
    private String name; // 文件名
    private Date createdTime; // 上传时间
    private Binary content; // 文件内容
    private String contentType; // 文件类型
    private long size; // 文件大小
 
    // getter/setter
    
}
```

- 此处 id 的类型要设置为 String ，MongoDB 会自动分配 id ，设置为数值型会报错。
- [BSON](https://docs.mongodb.com/manual/reference/glossary/#term-bson) 是一个二进制序列化格式，在MongoDB里面被用来做文档存储和远程程序调用，我们使用 org.bson.types.Binary 类来封装文件内容。`new Binary(byte[] byte)`

#### 上传图片

此处仅演示功能，便不分层等操作。

> 上传图片后，保存至 mongodb 数据库，并返回图片的访问 url 

```java
@Autowired
private MongoTemplate mongoTemplate;

@PostMapping("/file/uploadImage")
@ResponseBody
public String uploadImage(@RequestParam(value = "image") MultipartFile file){
    if(file.isEmpty())
            return JSONResult.build(200, "请选择一张图片", null);

    // 返回的 JSON 对象，这种类可自己封装
    JSONResult jsonResult = new JSONResult();
    String fileName = file.getOriginalFilename();
    try {
        UploadFile uploadFile = new UploadFile();
        uploadFile.setName(fileName);
        uploadFile.setCreatedTime(new Date());
        uploadFile.setContent(new Binary(file.getBytes()));
        uploadFile.setContentType(file.getContentType());
        uploadFile.setSize(file.getSize());

        UploadFile savedFile = mongoTemplate.save(uploadFile);
        String url = "http://localhost:8080/file/image/"+ savedFile.getId();

        jsonResult =  JSONResult.build(200, "图片上传成功", url);
    } catch (IOException e) {
        e.printStackTrace();
        jsonResult =  JSONResult.build(500, "图片上传失败", null);
    }
    return jsonResult;

}
```

- **MongoTemplate**：操作 MongdoDB 的类，例如增删该查等。

  你也可以创建一个 repository 层接口继承 MongodRepository，类似于 JPA 的操作。

- **JSONResult**：自己封装的返回 JSON 数据的工具类：

  `JSONResult(状态码, 信息, 数据);`

- **String url**：我们获取保存到数据库中的图片 id ，封装成访问图片的地址，其实也是一个请求，下面我们实现这个请求。

#### 获取图片

> 根据图片 id 获取图片

```java
import org.springframework.http.MediaType;

@GetMapping(value = "/file/image/{id}", produces = {MediaType.IMAGE_JPEG_VALUE, MediaType.IMAGE_PNG_VALUE})
@ResponseBody
public byte[] image(@PathVariable String id){
    byte[] data = null;
    UploadFile file = mongoTemplate.findImageById(id, UploadFile.class);
    if(file != null){
        data = file.getContent().getData();
    }
    return data;
}
```

- **produces**：表示返回给前端的类型，比如文本、GIF、PDF 等等，这里我们当然返回图片了。

