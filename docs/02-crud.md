# CRUD

Secara otomatis semua entity yang terdeteksi dapat diakses menggunakan modul CRUD ini. Untuk membatasi akses dapat ditambahkan _permission_.

## Bean
``` java
@Bean
protected CrudHandler crudHandler(
    EntityTrxManager entityTrxManager,
    DataMapper dataMapper,
    CrudResource resource,
    CrudPermission permission
) {
    return new CrudHandlerImpl()
    .setEntityTrxManager(entityTrxManager)
    .setResource(resource);
    .setPermission(permission);
}

// Contoh ambil Crud Resource
// Bisa disimpan di database untuk Entity yang boleh di-crud
// Ditambah dengan Permission untuk membatasi akses
@Bean
protected CrudResource crudResource(
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

// Contoh cek akses crud
// Bisa disimpan di database dengan memasangkan Resource & Role
// Termasuk bisa diset Specific Filter di bagian ini
@Bean
protected CrudPermission crudPermission() {
    return new CrudPermission() {
        @Override
        public boolean isCrudAllowed(CrudAction action, CrudRequest request) {
            //request.setSpesifics(null);
            return true;
        }
    };
}
```

## Controller
### Mvc
``` java
@RestController
@RequestMapping("/crud")
class CrudController extends net.ideahut.springboot.crud.CrudController {
    @PostMapping(value = "/action/{action}")
    protected Result action(@PathVariable("action") String action, HttpServletRequest request) throws Exception {
        byte[] data = RequestUtil.getBodyAsBytes(request);
        return super.body(CrudAction.valueOf(action.toUpperCase()), data);
    }
}
```
### Reactive
``` java
@RestController
@RequestMapping("/crud")
class CrudController extends net.ideahut.springboot.crud.ReactiveCrudController {
    @PostMapping(value = "/action/{action}") 
    protected Mono<Result> action(@PathVariable("action") String action,ServerHttpRequest request) throws Exception {
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
* `UNIQUE` mendapatkan satu data unik.
* `SINGLE` mendapatkan hanya satu data.
* `PAGE` mendapatkan koleksi data disertai informasi _paging_-nya.
* `LIST` mendapatkan koleksi data.
* `MAP` mendapatkan koleksi data dalam bentuk _key_-_value_.
* `CREATE` membuat data baru
* `UPDATE` memperbaharui data yang sudah ada
* `SAVE` membuat/memperbaharui data
* `DELETE` menghapus satu data
* `DELETES` menghapus koleksi data

## Contoh Request
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


