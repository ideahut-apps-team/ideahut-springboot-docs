# Annotation
Daftar _annotation_ yang tersedia.

## @Audit
Untuk mengidentifikasi suatu entity/model untuk diaudit setiap perubahannya.
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
Sebagai tanda apakah API yang diakses bersifat public atau tidak. Default: true.
``` java
@Public
@PostMapping(value = "/info")
public Result info() {
}
```

## @Reactive
Sebagai tanda apakah request atau response reactive atau tidak.
Request yang tidak reactive akan di-_load_ _body_-nya di Filter, sedangkan yang reactive akan di-_load_ di controller.
``` java
@Reactive(request = true, response = false)
@PostMapping(value = "/upload")
public Mono<Result> upload(ServerHttpRequest request) {
}

@Reactive(request = false, response = true)
@PostMapping(value = "/stream")
public Flux<byte[]> stream(@RequestParam("id") String id) {
}
```