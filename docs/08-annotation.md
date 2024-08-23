# Annotation

Daftar _annotation_ yang tersedia.

## @ApiExclude

Penggunaan di entity/model (untuk crud endpoint), dan di controller (untuk request mapping).

* Entity / Model bisa didaftarkan ke CRUD Resource atau tidak.

```java
// Entity tidak disertakan ke CRUD Resource
@ApiExclude
@Audit
@Entity
@Table(name = "api_provider")
@Setter
@Getter
@SuppressWarnings("serial")
public class ApiProvider extends EntityAudit {
    @Id
    @Column(name = "api_name", unique = true, nullable = false, length = 128)
    private String apiName;
 
    @Column(name = "secret")
    private String secret;
}
```

* _Request mapping_ bisa di-set permission atau tidak.

```java
// Semua request mapping di AdminController tidak di-set permission
@ApiExclude
@ComponentScan
@RestController
@RequestMapping("/admin")
class AdminController extends WebMvcAdminController {
    private final DataMapper dataMapper;
    private final AdminHandler adminHandler;
    @Autowired
    AdminController(
       DataMapper dataMapper,
       AdminHandler adminHandler
    ) {
       this.dataMapper = dataMapper;
       this.adminHandler = adminHandler;
    }
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

## @ApiSkip

_Request mapping_ akan diproses oleh ApiService atau tidak.

```java
@ApiSkip
@PostMapping("/access")
ApiAccess access(HttpServletRequest request) {
    return authService.getApiAccessForExternal(request);
}
```

## @Audit

Untuk mengidentifikasi suatu entity/model bisa diaudit setiap perubahannya.

``` java
@Audit
@Entity
public class User extends EntityAuditSoftDelete {
    @Id
    @GeneratedValue(generator = OdtIdGenerator.NAME)
    @GenericGenerator(name = OdtIdGenerator.NAME, strategy = OdtIdGenerator.STRATEGY)
    @Column(name = "user_id", unique = true, nullable = false, length = 64)
    private String userId;
}
```

## @IdPrefix

Menambah prefix ke id yang auto generated.

``` java
@Audit
@IdPrefix("USR")
@Entity
public class User extends EntityAuditSoftDelete {
    @Id
    @GeneratedValue(generator = OdtIdGenerator.NAME)
    @GenericGenerator(name = OdtIdGenerator.NAME, strategy = OdtIdGenerator.STRATEGY)
    @Column(name = "user_id", unique = true, nullable = false, length = 64)
    private String userId;
}
```
Dari contoh di atas, jika id yang digenerate "abcd012989", maka yang tersimpan di table menjadi "USRabcd012989".

## @Login

Sebagai tanda apakah API yang diakses wajib login atau tidak. Default: true.

``` java
@Login(false)
@PostMapping(value = "/login")
public Result login(
    @RequestParam("username") String username,
    @RequestParam("password") String password
) {

}
```

## @Public

Sebagai tanda apakah API yang diakses public atau tidak. Default: true.

``` java
@Public
@PostMapping(value = "/info")
public Result info() {
}
```

## @Body

* Request body di-_consume_ di level filter atau controller (karena hanya bisa satu kali). Untuk stream request, sebaiknnya di-_consume_ di level controller.
* Response body di-_produce_ oleh framework response writer atau manual di controller. Untuk stream response, sebaiknya di-_produce_ di level controller.

``` java
// request body di-consume di controller
@@Body(request = true, response = false)
@PostMapping(value = "/upload")
public Mono<Result> upload(ServerHttpRequest request) {
}

// response body di-produce di controller
@Body(request = false, response = true)
@PostMapping(value = "/stream")
public Flux<byte[]> stream(@RequestParam("id") String id) {
}

// request body di-consume di controller, dan response body di-produce di controller
@Body(request = true, response = true)
@GetMapping
protected ResponseEntity<StreamingResponseBody> report(ServerHttpRequest request) throws Exception {
   // get stream request
   // send stream response
}
```
