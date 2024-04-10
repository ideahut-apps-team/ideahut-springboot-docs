# Redis

## Bean
``` java
@Bean
protected RedisTemplate<String, byte[]> commonRedis() throws Exception {
    RedisProperties properties = appProperties.getRedis().getCommon();
    RedisConnectionFactory connectionFactory = RedisHelper.createRedisConnectionFactory(properties, true);
    return RedisHelper.createRedisTemplate(connectionFactory, false);
}
```

## Type
* `1`: standalone
* `2`: cluster
* `3`: sentinel

## Contoh Properties
``` md
common:
    type: 1
    testOnStartup: true
    standalone:
        host: 127.0.0.1
        port: 6379
        password: "<password>"
        database: 0
```