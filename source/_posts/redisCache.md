---
title: redis cache 操作
date: 2018-07-04 11:02:58
tags:
---

# 实体缓存方法
```java
Template template =
            Cache.hget(CacheName.TEMPLATE + sysService.getEnterpriseId(), customerId+"", Template.class,
            new ILoader() {
            	@Override
            	public Object load() {
                	return pfShopTemplateService.queryTemplate(sysService.getEnterpriseId(),customerId);
            	}
        	});
```

# list缓存方法
```java
List<PfGoodsCommodityListEntity> list = Cache.hgetList(CacheName.COMMODITY_LIST+params.get("goodsId").toString(),
		CacheKeyUtil.getKey(params), PfGoodsCommodityListEntity.class,
        new ILoader() {
            @Override
            public Object load() {
                return  pfGoodsInfoDao.queryCommodityList(params);
            }
        });

```

# ILoader 接口

```java
public interface ILoader {
	Object load();
}
```

# Cache 把注入的方法转为 静态方法

```java
@Component
public class Cache {

    @Autowired
    private BaseCache baseCache;

    private static Cache cache;

    @PostConstruct
    public void init() {
        cache = this;
        cache.baseCache = this.baseCache;
    }


    public static void hset(String cacheName,String key, Object object) {
        cache.baseCache.hset(cacheName,key,object);
    }


    public static <T> List<T> hgetList(String cacheName,String key, Class<T> clazz, ILoader iLoader) {
        return cache.baseCache.hgetList(cacheName,key,clazz,iLoader);
    }

    public static <T> T hget(String cacheName,String key,Class<T> clazz, ILoader iLoader) {
        return cache.baseCache.hget(cacheName, key,clazz, iLoader);
    }
}
```

# BaseCache 工具类
```java
@Component
public class BaseCache implements ICache{

    @Autowired
    private RedisService redisService;

    @Override
    public <T> T hget(String cacheName,String key, Class<T> clazz, ILoader iLoader) {
        Object data = redisService.hget(cacheName, key, clazz);
        if (data == null) {
            data = iLoader.load();
            hset(cacheName, key, data);
        }
        return (T) data;
    }

    @Override
    public <T> List<T> hgetList(String cacheName,String key, Class<T> clazz, ILoader iLoader) {
        Object data = redisService.hgetList(cacheName,key,clazz);
        if (data == null) {
            data = iLoader.load();
            hset(cacheName, key, data);
        }
        return (List<T>) data;
    }

    @Override
    public void hset(String cacheName, String key, Object value) {
        redisService.hset(cacheName, key, value);
    }
}

```

# RedisServer
```java
@Component
public class RedisService {
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    @Autowired
    private ValueOperations<String, String> valueOperations;
    @Autowired
    private HashOperations<String, String, Object> hashOperations;
    public <T> T hget(String cacheName,String key,Class<T> clazz){
        Object value = hashOperations.get(cacheName,key);
        return value == null ? null : fromJson(toJson(value), clazz);
    }

    public void hset(String cacheName,String key,Object value){
        hashOperations.put(cacheName,key,toJson(value));
    }

    public <T> List<T> hgetList(String cacheName,String key,Class<T> clazz) {
        Object value = hashOperations.get(cacheName,key);
        return parseString2List(toJson(value),clazz);
    }
}
```


# 缓存 json 转换为 list 工具方法
```java

    public <T> List<T> parseStringList(String json,Class clazz) {
        return gson.fromJson(json, new ParameterizedTypeImpl(clazz));
    }

    private  class ParameterizedTypeImpl implements ParameterizedType {
        Class clazz;

        public ParameterizedTypeImpl(Class clz) {
            clazz = clz;
        }

        @Override
        public Type[] getActualTypeArguments() {
            return new Type[]{clazz};
        }

        @Override
        public Type getRawType() {
            return List.class;
        }

        @Override
        public Type getOwnerType() {
            return null;
        }
    }
```

# 缓存 json 转换为 实体的工具方法

```java

	private <T> T fromJson(String json, Class<T> clazz){
        return gson.fromJson(json, clazz);
    }

```
