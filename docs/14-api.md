# API

* Security untuk mengakses CRUD dan _Request Mapping_ berdasarkan ApiRole
* Antar service / provider bisa berkomunikasi menggunakan token / signature

## ApiHandler

* Menangani data yang tersimpan di database dan redis

``` java
@Bean
ApiHandler apiHandler(
    AppProperties appProperties,
    DataMapper dataMapper,
    EntityTrxManager entityTrxManager,
    RedisTemplate<String, byte[]> redisTemplate,
    TaskHandler taskHandler
) {
    AppProperties.Api.Enable enable = appProperties.getApi().getEnable();
    return new ApiHandlerImpl()
    .setEnableSync(enable.getSync())
    .setEnableCrud(enable.getCrud())
    .setEnableConsumer(enable.getConsumer())
    .setDataMapper(dataMapper)
    .setEntityTrxManager(entityTrxManager)
    .setRedisPrefix("API-HANDLER")
    .setRedisTemplate(redisTemplate)
    .setTaskHandler(taskHandler);
}
```

## ApiTokenService

* Membuat _consumer token_ untuk komunikasi antar service / provider
* Memvalidasi signature (secret & digest) untuk komunikasi antar service / provider
* Membuat _access token_ untuk ApiProcessor yang bertipe JWT
* _SignatureTimeSpan_ adalah batas min / max timestamp terhadap waktu server dalam detik

``` java
@Bean
ApiTokenService apiTokenService(
    AppProperties appProperties,
    DataMapper dataMapper
) {
    AppProperties.Api.Consumer consumer = appProperties.getApi().getConsumer();
    AppProperties.Api.JwtProcessor jwtProcessor = appProperties.getApi().getJwtProcessor();
    return new ApiTokenServiceImpl()
    .setConsumer(new ApiTokenServiceImpl.Consumer()
       .setDigest(consumer.getDigest())
       .setExpiry(consumer.getExpiry())
       .setSecret(consumer.getSecret())
    )
    .setDataMapper(dataMapper)
    .setJwtProcessor(new ApiTokenServiceImpl.JwtProcessor()
       .setDigest(jwtProcessor.getDigest())
       .setExpiry(jwtProcessor.getExpiry())
       .setSecret(jwtProcessor.getSecret())
    )
    .setSignatureTimeSpan(appProperties.getApi().getSignatureTimeSpan());
}
```

## ApiAccessInternalService

* Untuk mendapatkan ApiAccess jika ApiName token dan service sama (proses dilakukan di server penerbit token)

``` java
@Bean
ApiAccessInternalService apiAccessInternalService(
    DataMapper dataMapper,
    EntityTrxManager entityTrxManager,
    RedisTemplate<String, byte[]> redisTemplate
) {
    return parameter -> {
       return null;
    };
}
```

## ApiConsumerService

* Untuk melakukan request dari satu service ke service yang lain

``` java
@Bean
ApiConsumerService apiConsumerService(
    DataMapper dataMapper,
    WebMvcApiService apiService
) {
    return new ApiConsumerServiceImpl()
    .setApiService(apiService)
    .setDataMapper(dataMapper);
}
```

## ApiService

* Meng-handle semua yang terkait dengan API CRUD dan Request Mapping
* Ada 2 jenis, yaitu: WebMvcApiService & WebFluxApiService
* Untuk mendapatkan ApiAccess custom bisa menggunakan setApiAccessRemoteService
* Untuk mendapatkan ApiSource custom bisa menggunakan setApiSourceService
* Untuk definisi request header custom bisa menggunakan setHeader
* Untuk ApiName custom bisa menggunakan setApiName (default diambil dari 'spring.application.name')

```` java
@Bean
WebMvcApiService apiService(
    AppProperties appProperties,
    DataMapper dataMapper,
    RedisTemplate<String, byte[]> redisTemplate,
    ApiHandler apiHandler,
    TaskHandler taskHandler,
    ApiTokenService apiTokenService,
    ApiAccessInternalService apiAccessInternalService 
) {
    AppProperties.Api.RedisExpiry redisExpiry = appProperties.getApi().getRedisExpiry();
    return new WebMvcApiServiceImpl()
    .setApiAccessInternalService(apiAccessInternalService)
    //.setApiAccessRemoteService(null)
    .setApiHandler(apiHandler)
    //.setApiName(null)
    .setApiProcessors(Arrays.asList(
       StandardAuthApiProcessor.class,
       StandardHeaderApiProcessor.class,
       StandardJwtApiProcessor.class,
       AgentAuthApiProcessor.class,
       AgentHeaderApiProcessor.class,
       AgentJwtApiProcessor.class,
       HostAuthApiProcessor.class,
       HostHeaderApiProcessor.class,
       HostJwtApiProcessor.class,
       AgentHostAuthApiProcessor.class,
       AgentHostHeaderApiProcessor.class,
       AgentHostJwtApiProcessor.class
    ))
    //.setApiSourceService(null)
    .setApiTokenService(apiTokenService)
    .setDataMapper(dataMapper)
    .setDefaultDigest("SHA-256")
    //.setHeader(null)
    .setRedisExpiry(new WebMvcApiServiceImpl.RedisExpiry()
       .setAccessItem(redisExpiry.getAccessItem())
       .setAccessNull(redisExpiry.getAccessNull())
       .setConsumerItem(redisExpiry.getConsumerItem())
       .setConsumerNull(redisExpiry.getConsumerItem())
    )
    .setRedisPrefix("API-SERVICE")
    .setRedisTemplate(redisTemplate)
    .setTaskHandler(taskHandler);
}
````

## Default HTTP Header

* `Access-Token`: token access atau token consumer
* `Access-Type`: kode tipe ApiProcessor
* `Access-Signature`: signature untuk komunikasi antar service, hash-digest(secret + timestamp)
* `Access-Timestamp`: timestamp dari service yang me-request
* `Access-ZoneOffset`: time zone offset dalam detik dari service yang me-request, contoh: GMT+7 = 25200
* `Access-From`: ApiName dari service yang me-request
* `Access-Data`: data tambahan dalam format json/xml

## ApiProcessor

* Meng-_handle_ token access dan token consumer
* Untuk menambah custom processor dengan cara meng-extends class 'net.ideahut.springboot.api.ApiProcessor' dan gunakan ApiType yang belum terpakai.
* Daftar default ApiProcessor bisa dilihat di table di di bawah (validasi berdasarkan User-Agent & Remote-Host pada saat token dibuat)

||ApiType|Token Header|Scheme|User Agent|Remote Host|
|---|:---:|:---:|:---:|:---:|:---:|
|`StandardAuthApiProcessor`|A00|Authorization||&#x2612;|&#x2612;|
|`StandardHeaderApiProcessor`|A01|Access-Token||&#x2612;|&#x2612;|
|`StandardJwtApiProcessor`|A02|Authorization|Bearer|&#x2612;|&#x2612;|
|`AgentAuthApiProcessor`|A03|Authorization||&#9745;|&#x2612;|
|`AgentHeaderApiProcessor`|A04|Access-Token||&#9745;|&#x2612;|
|`AgentJwtApiProcessor`|A05|Authorization|Bearer|&#9745;|&#x2612;|
|`HostAuthApiProcessor`|A06|Authorization||&#x2612;|&#9745;|
|`HostHeaderApiProcessor`|A07|Access-Token||&#x2612;|&#9745;|
|`HostJwtApiProcessor`|A08|Authorization|Bearer|&#x2612;|&#9745;|
|`AgentHostAuthApiProcessor`|A09|Authorization||&#9745;|&#9745;|
|`AgentHostHeaderApiProcessor`|A10|Access-Token||&#9745;|&#9745;|
|`AgentHostJwtApiProcessor`|A11|Authorization|Bearer|&#9745;|&#9745;|

### Contoh Token

```` md
// A00
{
    "time": 328332417,
    "status": 0,
    "data": {
        "token": "OGNjNjc0ZDViZjhlNTU3NTNkNDBhZTc1YmEyYTc3NWNiZTZhYmFjYmI3MjM3N2ZlMDU0M2QwOGU3ODM5Nzk2NTo6QTAwOjptb2JpbGUtYmFja2VuZA==::49d22562-794e-425e-bb20-53f49ae7ea63",
        "scheme": "",
        "headers": {
            "Authorization": "OGNjNjc0ZDViZjhlNTU3NTNkNDBhZTc1YmEyYTc3NWNiZTZhYmFjYmI3MjM3N2ZlMDU0M2QwOGU3ODM5Nzk2NTo6QTAwOjptb2JpbGUtYmFja2VuZA==::49d22562-794e-425e-bb20-53f49ae7ea63"
        }
    }
}

// A01
{
    "time": 196847667,
    "status": 0,
    "data": {
        "token": "NDc2MDM0ZGU5ODU5YjZmNGU1NTkzY2VhYTNmYmI1M2ZhNDk3NmVmNDAxYzAzYjRhNDhmYTRkOWE3ZGEwOGE3OTo6QTAxOjptb2JpbGUtYmFja2VuZA==::70bd30ec-7c83-4467-90a5-c762ca7ed8bc",
        "headers": {
            "Access-Token": "NDc2MDM0ZGU5ODU5YjZmNGU1NTkzY2VhYTNmYmI1M2ZhNDk3NmVmNDAxYzAzYjRhNDhmYTRkOWE3ZGEwOGE3OTo6QTAxOjptb2JpbGUtYmFja2VuZA==::70bd30ec-7c83-4467-90a5-c762ca7ed8bc"
        }
    }
}

// A02
{
    "time": 253228583,
    "status": 0,
    "data": {
        "token": "MTBmZjMyZjk3YzhjMTNiMjBmZTcyN2NiNmFlZjQ3YjJkYjhjMTQ5Mjo6QTAyOjptb2JpbGUtYmFja2VuZA==::eyJhbGciOiJIUzI1NiJ9.eyJhdHRyaWJ1dGVzIjp7ImRldmljZUlkIjoiNmY5Yzg4NDJhNDk5Nzk5NmVlMzZiZjIyMTcwNWQ0OTRkN2JmMjQ1YzcyZjhiNzNkYTUxNTY2NGY3ZDYxYjY2YyJ9LCJhcGlLZXkiOiIxMGZmMzJmOTdjOGMxM2IyMGZlNzI3Y2I2YWVmNDdiMmRiOGMxNDkyIiwiYXBpVXNlciI6eyJhdHRyaWJ1dGVzIjp7ImZ1bGxuYW1lIjoiQWRtaW4ifSwiaWQiOiJVU1IwMDAwLTAwMDAwLTAwMDAtMDAwMDAtMC0wMDAwIiwidXNlcm5hbWUiOiJhZG1pbiJ9LCJ2YWxpZFVudGlsIjoxNzI0NDgyNDk0Nzk1LCJzZXJ2aWNlUm9sZSI6eyJpZGVhaHV0LXNwcmluZ2Jvb3QtdGVtcGxhdGUiOiJURVNUIiwibW9iaWxlLWJhY2tlbmQiOiJBRE1JTiJ9fQ.wGaGv8ZN7KTdOOoW6tcR8oFFUsCfrXHlru8QiSS0mt0",
        "scheme": "Bearer",
        "headers": {
            "Authorization": "Bearer MTBmZjMyZjk3YzhjMTNiMjBmZTcyN2NiNmFlZjQ3YjJkYjhjMTQ5Mjo6QTAyOjptb2JpbGUtYmFja2VuZA==::eyJhbGciOiJIUzI1NiJ9.eyJhdHRyaWJ1dGVzIjp7ImRldmljZUlkIjoiNmY5Yzg4NDJhNDk5Nzk5NmVlMzZiZjIyMTcwNWQ0OTRkN2JmMjQ1YzcyZjhiNzNkYTUxNTY2NGY3ZDYxYjY2YyJ9LCJhcGlLZXkiOiIxMGZmMzJmOTdjOGMxM2IyMGZlNzI3Y2I2YWVmNDdiMmRiOGMxNDkyIiwiYXBpVXNlciI6eyJhdHRyaWJ1dGVzIjp7ImZ1bGxuYW1lIjoiQWRtaW4ifSwiaWQiOiJVU1IwMDAwLTAwMDAwLTAwMDAtMDAwMDAtMC0wMDAwIiwidXNlcm5hbWUiOiJhZG1pbiJ9LCJ2YWxpZFVudGlsIjoxNzI0NDgyNDk0Nzk1LCJzZXJ2aWNlUm9sZSI6eyJpZGVhaHV0LXNwcmluZ2Jvb3QtdGVtcGxhdGUiOiJURVNUIiwibW9iaWxlLWJhY2tlbmQiOiJBRE1JTiJ9fQ.wGaGv8ZN7KTdOOoW6tcR8oFFUsCfrXHlru8QiSS0mt0"
        }
    }
}
````

## Screenshot

<div align="left">
   <img src="./images/api-01.jpg" alt="api-01" title="api-01" width="800" />
</div>
<div align="left">
   <img src="./images/api-02.jpg" alt="api-02" title="api-02" width="800" />
</div>
<div align="left">
   <img src="./images/api-03.jpg" alt="api-03" title="api-03" width="800" />
</div>
<div align="left">
   <img src="./images/api-04.jpg" alt="api-04" title="api-04" width="800" />
</div>
<div align="left">
   <img src="./images/api-05.jpg" alt="api-05" title="api-05" width="800" />
</div>
<div align="left">
   <img src="./images/api-06.jpg" alt="api-06" title="api-06" width="800" />
</div>
<div align="left">
   <img src="./images/api-07.jpg" alt="api-07" title="api-07" width="800" />
</div>
