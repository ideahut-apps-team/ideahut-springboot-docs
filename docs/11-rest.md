# Rest

Http client untuk request ke server.

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
- `setDataMapper`: Data [Mapper](./02-mapper.md) bean.
- `setDefaultRestClient`: Konfigurasi default http client.
- `setEnableExecutionTime`: Waktu eksekusi disertakan di respons atau tidak.
- `setEnableRequestLimit`: Jumlah request secara bersamaan dibatasi atau tidak. Terkait dengan jumlah port yang digunakan oleh http client pada saat request ke server.
- `setTaskHandler`: [Task handler](./10-task.md), untuk membatasi jumlah request secara bersamaan.

##

### [Index](./index.md)
