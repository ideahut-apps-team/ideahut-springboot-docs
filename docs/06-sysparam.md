# System Parameter

Menyimpan konfigurasi aplikasi ke database dan redis.
``` java
public interface SysParamHandler {
	SysParamDto getSysParam(String sysCode, String paramCode);
	Map<String, SysParamDto> getSysParamMap(String sysCode);
	Map<String, Map<String, SysParamDto>> getSysParamMaps(String... sysCodes);
	
	<T> T getValue(Class<T> type, String sysCode, String paramCode, T defaultValue);
	<T> T getValue(Class<T> type, String sysCode, String paramCode);
	
	<T> T getValue(Class<T> type, Map<String, SysParamDto> sysMap, String paramCode, T defaultValue);
	<T> T getValue(Class<T> type, Map<String, SysParamDto> sysMap, String paramCode);
	
	byte[] getBytes(String sysCode, String paramCode, byte[] defaultValue);
	byte[] getBytes(String sysCode, String paramCode);
}
```
## Bean

``` java
@Bean
SysParamHandler sysParamHandler(
    BinarySerializer binarySerializer,
    RedisTemplate<String, byte[]> redisTemplate,
    EntityTrxManager entityTrxManager
) {
    return new SysParamHandlerImpl()
            
    // Serialize & deserialize byte array ke redis		
    .setBinarySerializer(binarySerializer)
    
    // Daftar Entity class dan nama trxManager yang terkait dengan SysParamHandler
    // default semua class di package 'net.ideahut.springboot.sysparam.entity'
    .setEntityClass(null)
    
    // EntityTrxManager
    .setEntityTrxManager(entityTrxManager)
    
    // RedisTemplate dan definisi penyimpanan key-nya
    .setRedisParam(
        new RedisParam<String, byte[]>()	
        .setAppIdEnabled(true)
        .setEncryptEnabled(true)
        .setPrefix("SYS-PARAM")
        .setRedisTemplate(redisTemplate)
    );
}
```

* `setBinarySerializer`: [BinarySerializer](./03-serializer.md) bean.
* `setEntityTrxManager`: [EntityTrxManager](./05-entity.md) bean.
* `setEntityClass`: Nama class entity SysParam.
* `setRedisParam`: Redis parameter, termasuk [RedisTemplate](./15-redis.md) dan prefix key-nya.

## Contoh

``` java
@Autowired
private SysParamHandler sysParamHandler;

@Public
@GetMapping("/sysparam/value")
String value() throws Exception {
    return sysParamHandler.getValue(String.class, "API-TOKEN", "SERVICE-ACCOUNT");
}

@Public
@GetMapping("/sysparam/maps")
Map<String, Map<String, EntSysParam>> sysParamMaps() throws Exception {
    return sysParamHandler.getSysParamMaps("ARTICLE", "MULTIMEDIA");
}

@Public
@GetMapping("/sysparam/value")
EntSysParam sysParamValue() {
    return sysParamHandler.getSysParam("SENTIMENT", "DEFAULT_ANALYZER_ID");
}

@Public
@GetMapping("/sysparam/reloadSysCodes")
void reloadSysCodes() {
    ((SysParamReloader) sysParamHandler).reloadSysCodes("ARTICLE", "MULTIMEDIA");
}

@Public
@GetMapping("/sysparam/removeSysCodes")
void removeSysCodes() {
    ((SysParamRemover) sysParamHandler).removeSysCodes("ARTICLE", "MULTIMEDIA");
}

@Public
@GetMapping("/sysparam/reloadSysParam")
void reloadSysParam() {
    ((SysParamReloader) sysParamHandler).reloadSysParam("SENTIMENT", "DEFAULT_ANALYZER_ID");
}

@Public
@GetMapping("/sysparam/removeSysParam")
void removeSysParam() {
    ((SysParamRemover) sysParamHandler).removeSysParam("SENTIMENT", "DEFAULT_ANALYZER_ID");
}
```

## Screenshot

<div>
   <img src="./assets/sysparam.jpg" alt="SysParam" title="SysParam" width="800" />
</div>

##

### [Index](./index.md)

