---
title: redis的使用
date: 2018-12-18 11:26:45
tags:
	- java
	- redis
categories:
	- java
---
## java中融入redis缓存技术的使用

redis相关的配置文件及工具类等：

```
pom.xml       导入redis.clients jedis 这里用到的版本号 2.9.0
spring-redis.xml    这里能直接使用redis.properties里定义的属性是因为在spring-dao.xml引入了
redis.properties
web.xml(这里已经通过spring-*.xml都引入了)
spring-dao.xml（和jdbc.properties一样将redis.properties引入）
JedisPoolWriper.java
JedisUtil.java
CacheService(&impl).java
```

因为我们的redis.properties里的配置是阿里云上面的，所以在直接运行时会报错.

```
could not get a resource from the pool
```

在我们本地执行命令看是否能连上阿里云上的redis

```
redis-cli -h 101.132.138.185
```

然后打ping报error了，并没有收到意料中的pong，可以看到错误信息

```
DENIED Redis is running in protected mode
```

说明现在redis处于保护模式下 这种模式下是不允许远程连接的，所以我们要改变连接模式
登录阿里云去更改redis的设置

![](/uploads/181218redis/1.png)

redis.conf中将protected-mode由yes改为no

![](/uploads/181218redis/2.png)

重启redis（先找到进程编号，再关掉进程）
将redis.conf传入redis-server启动redis

```
src/redis-server redis.conf 
```

![](/uploads/181218redis/3.png)

再次查看发现redis已经又启动起来了
回到本地环境，再次远程访问服务器的redis，输入ping，输出pong，连接成功。

![](/uploads/181218redis/4.png)

这个时候我们的本地就可以正常使用redis功能了
在service层中写好代码，这里我们是以地区类的service实现类来做例子，因为通常地区的变更很少，
所以我们可以从缓存中读出来，减少服务器数据库压力。
AreaService.java:

```
public interface AreaService {

	public static final String AREALISTKEY = "arealist";
	
	/**
	 * 获取区域列表，优先从缓存读取
	 * @return
	 */
	List<Area> getAreaList();
}
```

AreaServiceImpl.java:

```
@Service
public class AreaServiceImpl implements AreaService{

	@Autowired
	private AreaDao areaDao;
	@Autowired
	private JedisUtil.Keys jedisKeys;
	@Autowired
	private JedisUtil.Strings jedisStrings;
	
	//为了方便销毁redis的值，我们要将这个key的值放到接口中去 并且在变成不可更改的final值
	//private static String AREALISTKEY = "arealist";
	private static Logger logger = LoggerFactory.getLogger(AreaServiceImpl.class);
	
	@Override
	@Transactional
	public List<Area> getAreaList() {
		String key = AREALISTKEY;
		List<Area> areaList = null;
		ObjectMapper mapper = new ObjectMapper();
		if(!jedisKeys.exists(key)){
			//当jedis没有目标信息时
			//1.从数据库中取出目标集合
			areaList = areaDao.queryArea();
			String jsonString;
			try {
				//2.将目标集合转化为json字符串
				jsonString = mapper.writeValueAsString(areaList);
			} catch (JsonProcessingException e) {
				e.printStackTrace();
				logger.error(e.getMessage());
				throw new AreaOperationException(e.getMessage());
			}
			//3.将json串存入redis中
			jedisStrings.set(key, jsonString);
		}else{
			String jsonString = jedisStrings.get(key);
			JavaType javaType = mapper.getTypeFactory().constructParametricType(ArrayList.class, Area.class);
			try {
				areaList = mapper.readValue(jsonString, javaType);
			} catch (JsonParseException e) {
				e.printStackTrace();
				logger.error(e.getMessage());
				throw new AreaOperationException(e.getMessage());
			} catch (JsonMappingException e) {
				e.printStackTrace();
				logger.error(e.getMessage());
				throw new AreaOperationException(e.getMessage());
			} catch (IOException e) {
				e.printStackTrace();
				logger.error(e.getMessage());
				throw new AreaOperationException(e.getMessage());
			}
		}
		return areaList;
	}
	
}
```

这里redis使用成功了，当需要加载地区信息时，首先会先查看redis中是否有地区信息，
如果没有去数据库中查找出来并转换为json串存到redis中，下次就可以在redis中读到了

可是如果我们长期从这里读取，当数据库里面的信息更新改变时就读不到了，所以我们要定期更新。

这里我们通过CacheService来清空我们想要清除的redis key的信息。
CacheService.java:

```
public interface CacheService {

	/**
	 * 依据key前缀删除匹配该模式下的所有key-value 如传入：shopCategory，则shopcategory_allfirstlevel等所有
	 * 以shopcategory打头的key-value都会被清空
	 * @param keyPrefix
	 */
	void removeFromCache(String keyPrefix);
	
}
```

CacheServiceImple.java:

```
@Service
public class CacheServiceImpl implements CacheService{

	@Autowired
	private JedisUtil.Keys jedisKeys;

	@Override
	public void removeFromCache(String keyPrefix) {
		Set<String> keySet = jedisKeys.keys(keyPrefix + "*");
		for(String key:keySet){
			jedisKeys.del(key);
		}
	}
}
```

在测试类中测试发现，第一次进入query方法是从redis中读到的，在经过cache的remove方法后，
第二次进入query方法就是从数据库中重新读取写入redis缓存中的了.

```
public class AreaServiceTest extends BaseTest{

	@Autowired
	private AreaService areaService;
	@Autowired
	private CacheService cacheService;
	@Test
	public void testGetAreaList(){
		//因为之前redis中已经存过所以，这里是从redis中取出来的
		List<Area> areaList = areaService.getAreaList();
		assertEquals("杭电",areaList.get(0).getAreaName());
		//删除redis中key为arealist的缓存数据
		cacheService.removeFromCache(areaService.AREALISTKEY);
		//上面已经删除了，所以这里会访问数据库并再次存入redis
		areaList = areaService.getAreaList();
	}
}
```

至此，redis配置结束。