# Java笔记

### Java GC(Garbage Collection)

![java gc](/Users/beidi.lbd/Documents/Notes/recollection/java gc.jpeg)





-------

### String、StringBuffer和StringBuilder

- String是字符串常量，长度不可变。每次修改String变量时，会new一个新的String对象，然后将指针指向新的String对象。若字符串改变较多，会对性能产生影响，当无引用对象增多后，会触发JVM的GC工作。

- StringBuffer，每次都会对StringBuffer对象本身进行操作，而不是生成新的对象并改变对象引用。StringBuffer是线程安全的。常用的是append方法和insert方法。

- StringBuilder与StringBuffer类似，但是非线程安全。

- 使用策略：

  （1）基本原则：如果要操作少量的数据，用String ；单线程操作大量数据，用StringBuilder ；多线程操作大量数据，用StringBuffer。

  （2）不要使用String类的"+"来进行频繁的拼接，因为那样的性能极差的，应该使用StringBuffer或StringBuilder类，这在Java的优化上是一条比较重要的原则。

  （3）为了获得更好的性能，在构造 StringBuffer 或 StringBuilder 时应尽可能**指定它们的容量**。当然，如果你操作的字符串长度（length）不超过 16 个字符就不用了，当不指定容量（capacity）时默认构造一个容量为16的对象。不指定容量会显著降低性能。

  （4）StringBuilder一般使用在方法内部来完成类似"+"功能，因为是线程不安全的，所以用完以后可以丢弃。StringBuffer主要用在全局变量中。

  （5）相同情况下使用 StringBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。而在现实的模块化编程中，负责某一模块的程序员不一定能清晰地判断该模块是否会放入多线程的环境中运行，因此：除非确定系统的瓶颈是在 StringBuffer 上，并且确定你的模块不会运行在多线程模式下，才可以采用StringBuilder；否则还是用StringBuffer。

---------

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


### Java死锁及解决方法

##### java 死锁产生的四个必要条件：

1、互斥条件，即当资源被一个线程使用(占有)时，别的线程不能使用

2、请求和保持条件，即当资源请求者在请求其他的资源的同时保持对原有资源的占有。

3、不可抢占条件，资源请求者不能强制从资源占有者手中夺取资源，资源只能由资源占有者主动释放。

4、循环等待条件，即存在一个等待队列：P1占有P2的资源，P2占有P3的资源，P3占有P1的资源。这样就形成了一个等待环路。



##### 解决方法：

当上述四个条件都成立的时候，便形成死锁。当然，死锁的情况下如果打破上述任何一个条件，便可让死锁消失。

1、互斥条件：很多系统资源如IO等必须是互斥的，破坏互斥条件的成本较大。

2、请求与保持条件：可以通过一次性获取所有资源即对需要的资源进行原子申请可以解决死锁问题，这种方式对系统开销较大，不太理想。

3、不可抢占条件：可以通过定时释放占有的资源解决死锁问题，但是这也会带来过多的资源占有释放操作。

4、循环等待条件：这是解决死锁常用的方法，设置超时时间tryLock/tryAcquire，一旦超时就释放资源。



synchronized关键字是java的锁

---------------------------------------

### Java线程安全和非线程安全

ArrayList - 非线程安全		Vector - 线程安全

HaspMap - 非线程安全	HashTable - 线程安全

StringBuilder - 非线程安全	StringBuffer - 线程安全

非线程安全是指**多线程操作同一个对象**可能会出现问题。而线程安全则是多线程操作同一个对象不会有问题。

##### 取舍：

线程安全必须要使用很多**synchronized关键字**来同步控制，所以必然会导致**性能的降低**。所以在使用的时候，如果是多个线程操作同一个对象，那么使用线程安全的Vector；否则，就使用效率更高的ArrayList。

[例子](https://blog.csdn.net/xiao__gui/article/details/8934832)

--------------------------









