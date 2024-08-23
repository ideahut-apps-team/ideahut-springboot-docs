# CRUD

- Secara otomatis semua entity yang terdeteksi dapat diakses menggunakan modul CRUD ini
- Untuk membatasi akses dapat dipasangkan dengan ApiRole (lihat: modul [API](./14-api.md))

## Bean

- `CrudHandler`: meng-handle pengambilan resource dan permission
- `CrudResource`: mendapatkan CrudProperties yang akan dieksekusi oleh `CrudHandler`
- `CrudPermission`: mengecek apakah eksekusi diijinkan atau tidak

``` java
@Bean
CrudHandler crudHandler(
    ApplicationContext applicationContext,
    EntityTrxManager entityTrxManager,
    DataMapper dataMapper,
    CrudResource resource,
    CrudPermission permission
) {
    return new CrudHandlerImpl()
    .setApplicationContext(applicationContext)
    .setEntityTrxManager(entityTrxManager)
    .setResource(resource)
    .setPermission(permission)
    .setSpecifics(CrudSupport.getSpecifics());
}

// Contoh CrudResource berdasarkan nama class yang didefinisikan di CrudRequest
@Bean
CrudResource crudResource(
    EntityTrxManager entityTrxManager
) {
    return new CrudResource() {
        @Override
        public CrudProps getCrudProps(String manager, String name) {
            try {
                Class<?> clazz = FrameworkUtil.classOf(EntityFill.class.getPackageName() + "." + name);
                TrxManagerInfo trxManagerInfo = entityTrxManager.getDefaultTrxManagerInfo();
                if (manager != null && !manager.isEmpty()) {
                    trxManagerInfo = entityTrxManager.getTrxManagerInfo(manager);
                }
                EntityInfo entityInfo = trxManagerInfo.getEntityInfo(clazz);
                CrudProps resource = new CrudProps();
                resource.setEntityInfo(entityInfo);
                resource.setMaxLimit(200);
                resource.setUseNative(false);
                return resource;
            } catch (Exception e) {
                throw FrameworkUtil.exception(e);
            }
        }
    };
}

// Contoh CrudResource yang diambil menggunakan ApiService
@Bean
CrudResource crudResource(
    WebMvcApiService apiService
) {
    return (manager, name) -> {
        ApiAccess apiAccess = RequestContext.currentContext().getAttribute(ApiAccess.CONTEXT);
        CrudProperties properties = apiService.getApiCrudProperties(apiAccess, name);
        Assert.notNull(properties, "CrudProperties is not found: " + name);
        return properties;
    };
}

// Contoh CrudPermission mengijinkan semua CrudRequest
@Bean
CrudPermission crudPermission() {
    return (action, request) -> true;
}

// Contoh CrudPermission yang mengecek apakah action didefinisikan di table / ApiService
@Bean
CrudPermission crudPermission() {
    return (action, request) -> {
        CrudProperties properties = request.getProperties();
        Set<CrudAction> actions = properties.getActions();
        return actions != null && actions.contains(action);
    };
}
```

## Controller

### WebMvc

``` java
@RestController
@RequestMapping("/crud")
class CrudController extends net.ideahut.springboot.crud.WebMvcCrudController {
    @PostMapping(value = "/action/{action}")
    Result action(
        @PathVariable("action") String action, 
        HttpServletRequest request
    ) throws Exception {
        byte[] data = RequestUtil.getBodyAsBytes(request);
        return super.body(CrudAction.valueOf(action.toUpperCase()), data);
    }
}
```

### WebFlux

``` java
@RestController
@RequestMapping("/crud")
class CrudController extends net.ideahut.springboot.crud.WebFluxCrudController {
    @PostMapping(value = "/action/{action}") 
    Mono<Result> action(
        @PathVariable("action") String action,
        ServerHttpRequest request
    ) throws Exception {
        return DataBufferUtils
        .join(request.getBody())
        .flatMap(dataBuffer -> {
            byte[] data = new byte[dataBuffer.readableByteCount()];
            dataBuffer.read(data);
            DataBufferUtils.release(dataBuffer);
            Result result = super.body(CrudAction.valueOf(action.toUpperCase()), data);
            return Mono.just(result);
        });
    }
}
```

## Action

- `UNIQUE` mendapatkan satu data unik.
- `SINGLE` mendapatkan hanya satu data.
- `PAGE` mendapatkan koleksi data disertai informasi halaman.
- `LIST` mendapatkan koleksi data.
- `MAP` mendapatkan koleksi data dalam bentuk _key_-_value_.
- `CREATE` membuat data baru.
- `UPDATE` memperbaharui data yang sudah ada.
- `SAVE` membuat/memperbaharui data.
- `DELETE` menghapus satu data.
- `DELETES` menghapus koleksi data.

## Condition

- `ANY_LIKE` mengandung kalimat (huruf besar/kecil tidak wajib)
- `ANY_START` dimulai dengan kalimat (huruf besar/kecil tidak wajib)
- `ANY_END` diakhiri dengan kalimat (huruf besar/kecil tidak wajib)
- `ANY_EQUAL` sama dengan kalimat (huruf besar/kecil tidak wajib)
- `LIKE` mengandung kalimat
- `START` dimulai dengan kalimat
- `END` diakhiri dengan kalimat
- `NOT_ANY_LIKE` tidak mengandung kalimat (huruf besar/kecil tidak wajib)
- `NOT_ANY_START` tidak dimulai dengan kalimat (huruf besar/kecil tidak wajib)
- `NOT_ANY_END` tidak diakhiri dengan kalimat (huruf besar/kecil tidak wajib)
- `NOT_ANY_EQUAL` tidak sama dengan kalimat (huruf besar/kecil tidak wajib)
- `NOT_LIKE` tidak mengandung kalimat
- `NOT_START` tidak dimulai dengan kalimat
- `NOT_END` tidak diakhiri dengan kalimat
- `NOT_EQUAL` tidak sama dengan
- `BETWEEN` diantara, min - max
- `NOT_NULL` bukan null
- `IS_NULL` hanya yang null
- `GREATER_THAN` lebih besar
- `GREATER_EQUAL` lebih besar atau sama dengan
- `LESS_THAN` lebih kecil
- `LESS_EQUAL` lebih kecil atau sama dengan
- `IN` hanya yang di dalam
- `NOT_IN` selain yang di dalam
- `EQUAL` sama dengan

## Request

- `name` nama resource.
- `manager` nama transaction manager.
- `replica` nomor table dari entity.
- `ids` array id entity.
- `id` id entity.
- `map` definisi key-value untuk action MAP.
- `page` informasi halaman.
- `start` start offset.
- `limit` batas jumlah data.
- `filters` array filter.
- `orders` array order (descending dimulai dengan karakter '-').
- `fields` spesifik field yang ditampilkan.
- `loads` daftar field yang datanya diambil dari entity lain (Lazy Load).
- `values` array value, untuk insert & update.
- `joins` array join dengan entity lain.
- `stacks` array stack, proses bertingkat. Contoh: setelah user disimpan, bisa dilanjutkan dengan menyimpan data detail atau otentikasi.

### Page

``` js
{
    "name": "AutoGenStrIdHardDel",
    "page": {
        "index": 1,
        "size": 10,
        "count": true
    },
    "filters": [
        {
            "field": "name",
            "condition": "NOT_NULL"
        },
        {
            "field": "date",
            "condition": "BETWEEN",
            "values": ["2024-01-01 00:00:00", "2024-01-31 23:59:59"]
        }
    ],
    "orders":["-createdOn"]
}

// Contoh menggunakan replica
{
    "name": "Information",
    "replica": 1,
    "page": {
        "index": 1,
        "size": 100,
        "count": true
    },
    "orders":["-createdOn"]
}
```

### Map

``` js
{
    "name": "AutoGenStrIdHardDel",
    "start": 0,
    "limit": 10,
    "map": {
        "keys": ["id", "name"],
        "flat": true
    },
    "filters": [
        {
            "field": "createdOn",
            "condition": "not_null"
        }        
    ],
    "orders":["-createdOn"]

}
```

### Unique

``` js
{
    "name": "AutoGenStrIdHardDel",
    "id": "2024-102085-1930-22404-25200-0000"
}
```

### Create

``` js
{
    "name": "AutoGenStrIdHardDel",
    "value": {
        "name": "COBA",
        "isActive": "Y",
        "description": "description",
        "date": "2024-01-02 18:43:24",
        "condition": "ANY_LIKE"
    }
}
```

### Update

``` js
{
    "name": "AutoGenStrIdHardDel",
    "id": "2024-102107-1544-16584-25200-0001",
    "value": {
        "name": "yyyyyTest (Edited)",
        "description": null,
        "date": "2024-01-03 18:00:02",
        "condition": "BETWEEN"
    }
}
```

### Delete

``` js
{
    "name": "AutoGenStrIdHardDel",
    "id": "2024-102037-1717-17536-25200-0001"
}
```

### Deletes

``` js
{
    "name": "AutoGenStrIdHardDel",
    "ids": [
        "2024-204067-1553-20523-25200-0000",
        "2024-101056-1708-46195-25200-0001",
        "2024-101056-1711-09428-25200-0002"
    ]
}
```

## Screenshot

<div align="center">
   <img src="./images/crud.jpg" alt="CRUD" title="CRUD" width="800" />
</div>

