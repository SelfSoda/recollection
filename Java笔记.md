# Java笔记

### Json和list互相转换

```java
//list转换为json
List<CustPhone> list = new ArrayList<CustPhone>();
String str=JSON.toJSON(list).toString();
```

```java
//json转换为list
List<Person> list = new ArrayList<Person>();  
list = JSONObject.parseArray(jasonArray, Person.class);  
```

Maven：

```xml
<dependency>
	<groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.48</version>
</dependency>
```

-----------

### RestTemplate发送GET请求

```java
public String getFeedByRestTemplate(){
    	String url = "https://itunes.apple.com?value1={value1}&value2={value2}";
        // 设置GET请求的参数
        HashMap<String, Object> params = new HashMap<String, Object>();
        params.put("value1", "test1");
        params.put("value2", 123);
        // 设置Headers
        HttpHeaders headers = new HttpHeaders();
        headers.add("User-Agent", "iTunes/11.0 (Windows; Microsoft Windows 7 Business Edition Service Pack 1 (Build 7601)) AppleWebKit/536.27.1");
        HttpEntity<String> entity = new HttpEntity<String>(null, headers);
        // 发送GET请求
        RestTemplate restTemplate = new RestTemplate();
        ResponseEntity<String> response = restTemplate.exchange(url, HttpMethod.GET, entity, String.class, params);
        String result = response.getBody();
        return result;
    }
```

--------

### Date类

利用SimpleDateFormat类解析String

```java
String dateStr = "1999-12-01 23:59:59"
SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
Date date = df.parse(dateStr);
```

Date类改变格式

```java
Date curDate = new Date(); // 获取当前时间
SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
Date curDate2 = df.format(curDate) // 修改了格式
```

-------

### Spring中引用静态文件

如模型的参数文件等

静态文件要放在starter服务的resources文件夹中

```java
// Method1 : use ClassPathResource 
ClassPathResource resource = new ClassPathResource("application.yml");

InputStream inputStream = resource.getInputStream();
IOUtils.readLines(inputStream).forEach(System.out::println);

File file = resource.getFile();

// Method2 : use ResourceUtil
File file = ResourceUtils.getFile("classpath:model_file");

```

--------------

### 对象序列化

序列化 (Serialization)是将对象的状态信息转换为可以存储或传输的格式的过程。 反序列化 (Deserialization)是通过从存储或者网络读取对象的状态，重新创建该对象。序列化广泛应用在远程调用（RPC）或者数据存取。

Java为我们提供了Serializable接口，这是一个空接口；如果一个类实现了Serializable接口，那么就代表这个类是自动支持序列化和反序列化的，毋须我们编程实现。

**序列化／反序列化示例**

```java
// Person对象文件路径
String path = "d:/tmp/person.dat";
// 创建一个Person对象
Person p1 = new Person("张三", 18);
// 序列化Person对象
ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream(path));
out.writeObject(p1);
out.close();
// 反序列化
ObjectInputStream in = new ObjectInputStream(new FileInputStream(path));
Person p2 = (Person) in.readObject();
in.close();
// 反序列化后对象的内存地址和原来的地址不同，是深拷贝。
System.out.println("p1 == p2 is: " + (p1 == p2));
```

------------











