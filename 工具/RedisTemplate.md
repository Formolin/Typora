# [关于spring boot使用redis的increment()方法自增问题](https://www.cnblogs.com/qsymg/p/9830877.html)



需求是限制IP频繁访问某接口，用的方案是使用redis记录访问IP的值，先设定好初始值，每次访问自增，达到某限定值后，进行阻止。

用的是自定义工具类，使用spring封装的spring-data-redis进行操作，在对某key进行increment()方法时，报错：

```
redis ERR value is not an integer or out of range 
```

代码逻辑如下：

```

```

[![复制代码](http://ww4.sinaimg.cn/large/006tNc79ly1g3dh8uz5lqg300k00k07b.gif)](javascript:void(0);)

```
Integer count = (Integer) redisUtil.get(ipAddress);//取得key的value
        if (count == null){
            redisUtil.set(ipAddress,1,10);
            return false;
        }else if(count == 3){
            return false;
        }else {
            redisUtil.incr(ipAddress,1);
            return false;
        }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```

```

 

```

```

 第一次进来，如果没有redis中没有数据，则设置key,value和time，key是ip， value初始值为1，有效时长为10秒。

如果没达到限制次数，则对key自增1。

redisUtil.incr()方法实现如下：

```

```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
@Resource
    private RedisTemplate<String, Object> redisTemplate; //这里使用的是redisTemplate

    public void setRedisTemplate(RedisTemplate<String, Object> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

   /**
     * 递增
     * @param key 键
//     * @param by 要增加几(大于0)
     * @return
     */
    public long incr(String key, long delta){
        if(delta<0){
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```

```

 

```

```

开始以为是incr方法接受的参数是long型，但我传入的是INTEGER类型，但转换后还是没有解决问题，问题不是出在这，后来通过查找资料发现，Spring对Redis序列化的策略有两种，分别是StringRedisTemplate和RedisTemplate，其中StringRedisTemplate用于操作字符串，RedisTemplate使用的是JDK默认的二进制序列化。

大家都知道redis序列化是将key，value值先转换为流的形式，再存储到redis中。

RedisTemplate是使用的JdkSerializationRedisSerializer序列化，序列化后的值包含了对象信息，版本号，类信息等，是一串字符串，所以无法进行数值自增操作。

而StringRedisTemplate序列化策略是字符串的值直接转为字节数组，所以存储到redis中是数值，所以可以进行自增操作。

StringRedisSerializer源码：

```

```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public class StringRedisSerializer implements RedisSerializer<String> {
    private final Charset charset;

    public StringRedisSerializer() {
        this(StandardCharsets.UTF_8);
    }

    public StringRedisSerializer(Charset charset) {
        Assert.notNull(charset, "Charset must not be null!");
        this.charset = charset;
    }

    public String deserialize(@Nullable byte[] bytes) {
        return bytes == null ? null : new String(bytes, this.charset);
    }

    public byte[] serialize(@Nullable String string) {
        return string == null ? null : string.getBytes(this.charset); //注意这里是字节数组
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```

```

 

```

```

所以问题出在这里，我们需要自定义序列化策略，在application启动类中添加如下：

```

```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 @Bean
    public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory factory) {
        StringRedisTemplate template = new StringRedisTemplate(factory);
        //定义key序列化方式
        //RedisSerializer<String> redisSerializer = new StringRedisSerializer();//Long类型会出现异常信息;需要我们上面的自定义key生成策略，一般没必要
        //定义value的序列化方式
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);

        // template.setKeySerializer(redisSerializer);
        template.setValueSerializer(jackson2JsonRedisSerializer);
        template.setHashValueSerializer(jackson2JsonRedisSerializer);
        template.afterPropertiesSet();
        return template;
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```

```

费了2多小时才成功解决问题，RedisUtil.incr()能够成功对key进行自增了，如有错误之处请欢迎指出。