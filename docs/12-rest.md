# Rest

Http client untuk request ke server.

``` java
public interface RestHandler {
    // single request
	RestResponse call(RestClient restClient, RestRequest restRequest);
	RestResponse call(RestRequest restRequest);
	
    // list requests, dan response diambil berdasarkan index
    List<RestResponse> call(RestClient restClient, List<RestRequest> restRequests);
	List<RestResponse> call(List<RestRequest> restRequests);

    // map requests, dan response diambil berdasarkan key
	Map<String, RestResponse> call(RestClient restClient, Map<String, RestRequest> restRequests);
	Map<String, RestResponse> call(Map<String, RestRequest> restRequests);
}
```

## Bean

``` java
// OK HTTP
@Bean
RestHandler restHandler(
    DataMapper dataMapper
) {
    return new OkHttpRestHandler()
    .setClientDestroyable(null)
    .setDataMapper(dataMapper)
    .setDefaultRestClient(null)
    .setEnableExecutionTime(null)
    .setEnableRequestLimit(null)
    .setTaskHandler(null);
}

// APACHE HTTP CLIENT VERSI 4
@Bean
RestHandler restHandler(
    DataMapper dataMapper
) {
    return new ApacheHttpClient4xRestHandler()
    .setClientDestroyable(null)
    .setDataMapper(dataMapper)
    .setDefaultRestClient(null)
    .setEnableExecutionTime(null)
    .setEnableRequestLimit(null)
    .setTaskHandler(null);
}

// APACHE COMMONS HTTP CLIENT
@Bean
RestHandler restHandler(
    DataMapper dataMapper
) {
    return new ApacheCommonsHttpClientRestHandler()
    .setClientDestroyable(null)
    .setDataMapper(dataMapper)
    .setDefaultRestClient(null)
    .setEnableExecutionTime(null)
    .setEnableRequestLimit(null)
    .setTaskHandler(null);
}
```

- `setClientDestroyable`: Http client di-_close_ atau tidak setelah proses selesai.
- `setDataMapper`: [DataMapper](./02-mapper.md) bean.
- `setDefaultRestClient`: Konfigurasi default http client.
- `setEnableExecutionTime`: Waktu eksekusi disertakan di respons atau tidak.
- `setEnableRequestLimit`: Jumlah request secara bersamaan dibatasi atau tidak. Hal ini terkait dengan jumlah port yang digunakan oleh http client pada saat request ke server.
- `setTaskHandler`: [TaskHandler](./11-task.md), untuk membatasi jumlah request secara bersamaan.

##

### [Index](./index.md)
