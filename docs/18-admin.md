# Admin

Untuk mengakses API dan halaman Admin, yang berguna untuk manajemen service:

* `Reload` memuat ulang bean tanpa harus merestart service.
* `Request` daftar request mapping yang tersedia di service.
* `Redis` daftar redis server yang digunakan di service.
* `Cache` menghapus / membersihkan data cache baik itu group maupun tunggal.
* `Grid` mengakses table-table database (operasi CRUD).
* `Audit` melihat perubahan-perubahan data dari tabel-tabel database (INSERT, UPDATE, DELETE).
* `Entity` melihat daftar entity/model yang tersedia berdasarkan transactionManager, termasuk untuk membuat replica & grid.
* `Scheduler` memonitor job, schedule/unschedule job, start/stop scheduler.

## Bean

``` java
@Bean
AdminHandler adminHandler(
    DataMapper dataMapper,
    RedisTemplate<String, byte[]> redisTemplate
) {
    AppProperties.Admin admin = appProperties.getAdmin();
    return new AdminHandlerImpl()
    .setApplicationContext(applicationContext)
    .setConfigFile(admin.getConfigFile())
    .setDataMapper(dataMapper)
    .setGridAdditionals(GridSupport.getAdditionals())
    .setGridOptions(GridSupport.getOptions())
    .setProperties(admin)
    .setRedisTemplate(redisTemplate);
}

@Bean(name = AppConstants.Bean.Credential.ADMIN)
RedisMemoryCredential adminCredential(
    DataMapper dataMapper,
    RedisTemplate<String, byte[]> redisTemplate
) {
    AppProperties.Admin admin = appProperties.getAdmin();
    return new RedisMemoryCredential()
    .setConfigFile(admin.getCredentialFile())
    .setDataMapper(dataMapper)
    .setRedisPrefix("ADMIN-CREDENTIAL")
    .setRedisTemplate(redisTemplate);
}

@Bean(name = AppConstants.Bean.Security.ADMIN)
AdminSecurity adminSecurity(
    DataMapper dataMapper,
    AdminHandler adminHandler,
    SecurityCredential credential
) {
    return new AdminSecurity()
    .setCredential(credential)
    .setDataMapper(dataMapper)
    //.setEnableRemoteHost(true)
    //.setEnableUserAgent(true)
    .setProperties(adminHandler.getProperties());
}
```

## Konfigurasi

``` js
{
    "api": {
        "requestPath": "/admin"
    },
    "resource": {
        "title": " ",
        "requestPath": "/_",
        "locations": "file:{user.dir}/extras/admin/resource/",
        "indexFile": "index.html",
        "alwaysToIndex": true,
        "allowedPaths": ["css", "fonts", "icons", "img", "js"]
    },
    "grid": {
        "location": "file:{user.dir}/extras/admin/grid/**/*.json",
        "definition": "file:{user.dir}/extras/admin/grid/grid.def"
    },
    "crud": {
        "maxLimit": 200,
        "useNative": false
    },
    "central": {
        "resourceEndpoint": "http://localhost:6401/sync/resource",
        "tokenEndpoint": "http://localhost:6401/sync/token",
        "resourceDirectory": "{user.dir}/extras/admin/resource"
    }
}
```

## Kredensial

``` js
{
    "passwordType": "bcrypt",
    "redisExpiry": 15,
    "users": [
    {
            "username": "admin",
            "password": "$2a$10$NL8fAwz/UG6FCk6sEo10Ueuihe.oiX4DQHN4OWqXmDUM9.4Hnu8EC"
        },
        {
            "username": "mimin",
            "password": "$2a$10$uIAtTYQcSsXOR7xABu/gwOLqf3mOde7z2vZVqug3OjItsdKrmuc5m"
        }
    ]
}
```

## Controller

``` java
// WebMvc
@RestController
@RequestMapping("/admin")
class AdminController extends net.ideahut.springboot.admin.WebMvcAdminController {
 
    @Autowired
    private DataMapper dataMapper;
    @Autowired
    private AdminHandler adminHandler;
 
    @Override
    protected AdminHandler adminHandler() {
       return adminHandler;
    }

    @Override
    protected DataMapper dataMapper() {
       return dataMapper;
    } 

}

// WebFlux
@RestController
@RequestMapping("/admin")
class AdminController extends net.ideahut.springboot.admin.WebFluxAdminController {
 
    @Autowired
    private DataMapper dataMapper;
    @Autowired
    private AdminHandler adminHandler;
 
    @Override
    protected AdminHandler adminHandler() {
       return adminHandler;
    }

    @Override
    protected DataMapper dataMapper() {
       return dataMapper;
    } 

}
```

## Static Resource

``` java
// WebMvc
@Configuration
@EnableWebMvc
class WebMvcConfig extends net.ideahut.springboot.config.WebMvcBasicConfig {
    @Autowired
    private AdminHandler adminHandler;

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
       AdminProperties.Resource resource = adminHandler.getProperties().getResource();
       if (resource != null) {
           registry
           .addResourceHandler(resource.getRequestPath() + "/**")
           .addResourceLocations(resource.getLocations())
           .setCacheControl(CacheControl.maxAge(60, TimeUnit.DAYS))
           .resourceChain(false)
           .addResolver(new VersionResourceResolver().addContentVersionStrategy(resource.getRequestPath() + "/**"));
       }
       super.addResourceHandlers(registry);
    }
}

// WebFlux
@Configuration
@EnableWebFlux
class WebFluxConfig extends net.ideahut.springboot.config.WebFluxBasicConfig {
    @Autowired
    private AdminHandler adminHandler;

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
       AdminProperties.Resource resource = adminHandler.getProperties().getResource();
       if (resource != null) {
           registry
           .addResourceHandler(resource.getRequestPath() + "/**")
           .addResourceLocations(resource.getLocations())
           .setCacheControl(CacheControl.maxAge(60, TimeUnit.DAYS))
           .resourceChain(false)
           .addResolver(new VersionResourceResolver().addContentVersionStrategy(resource.getRequestPath() + "/**"));
       }
       super.addResourceHandlers(registry);
    }
}
```

## Screenshot

<div>
   <img src="./assets/admin-01.jpg" alt="admin-01" title="admin-01" width="800" />
</div>
<div>
   <img src="./assets/admin-02.jpg" alt="admin-02" title="admin-02" width="800" />
</div>

##

### [Index](./index.md)
