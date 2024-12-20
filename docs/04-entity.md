# Entity Transaction Manager

* Mengumpulkan informasi semua entity / model dari _Transaction Manager_ yang ada (bisa lebih dari satu _Transaction Manager_).
* Dapat digunakan untuk transaksi tanpa menggunakan DAO _Repository_.
* Digunakan di [CRUD](./06-crud.md), [Audit](./08-audit.md), & [Grid](./07-grid.md).
* Mendukung hibernate query (hql) maupun native query (sql).
* Mendukung _replica_ (satu entity dimapping ke banyak table), contoh: entity User di-_mapping_ ke table user_1, user_2, user_3, dst.

## Bean

``` java
@Bean
EntityTrxManager entityTrxManager() {
    return new EntityTrxManagerImpl()
    //.setApiExcludeParams()
    //.setAuditParams()
    //.setEntityListenerParam()
    //.setForeignKeyParam()
    ;
}
```

* `setApiExcludeParams`: Entity / Model yang tidak memiliki anotasi @ApiExclude, dan tidak ingin dipublikasikan di ApiService.
* `setAuditParams`: Entity / Model yang tidak memiliki anotasi @Audit, dan ingin setiap perubahannya disimpan.
* `setEntityListenerParam`: Daftar EntityPreListener & EntityPostListener, secara default akan di-_detect_ otomatis dari semua bean yang ada _applicationContext_.
* `setForeignKeyParam`: Untuk menghandle anotasi @ForeignKeyEntity yang ada di entity / model.

## Tipe ID

### `STANDARD` satu id untuk satu entity

``` java
public class User extends EntityAuditSoftDelete {
    @Id
    @GeneratedValue(generator = OdtIdGenerator.NAME)
    @GenericGenerator(name = OdtIdGenerator.NAME, strategy = OdtIdGenerator.STRATEGY)
    @Column(name = "user_id", unique = true, nullable = false, length = 64)
    private String userId;
}
```

### `COMPOSITE` multi id untuk satu entity

``` java
public class CompositeHardDel extends EntityAudit {
    @Id
    @Column(name = "type", nullable = false)
    private Integer type;
    @Id
    @Column(name = "code", length = 3, nullable = false)
    private String code;
}
```

### `EMBEDDED` multi id yang didefinisikan ke satu object untuk di-_embed_ ke entity

``` java
public class EmbededId implements java.io.Serializable {
    @Column(name = "type", nullable = false)
    private Integer type;
    @Column(name = "code", length = 3, nullable = false)
    private String code;
}

public class EmbeddedHardDel extends EntityAudit {
    @EmbeddedId
    @AttributeOverride(name = "type", column = @Column(name = "type", nullable = false))
    @AttributeOverride(name = "code", column = @Column(name = "code", nullable = false))
    private EmbededId id;
}
```

## Contoh Transaksi

``` java
TrxManagerInfo trxManagerInfo = entityTrxManager.getDefaultTrxManagerInfo();
User user = trxManagerInfo.transaction(new SessionCallable<User>() {
    @Override
    public User call(Session session) throws Exception {
        return session.get(User.class, "user_id");
    }
});
```

## PreListener

Listener sebelum entity mengalami perubahan (INSERT, UPDATE, & DELETE).
Contoh penggunaan di [cache](./09-cache.md), untuk membuang data yang tersimpan di memori.

``` java
public interface EntityPreListener { 
    void onPreDelete(Object entity);
    void onPreUpdate(Object entity);
    void onPreInsert(Object entity); 
}
```

## PostListener

Listener setelah entity mengalami perubahan (INSERT, UPDATE, & DELETE).
Contoh penggunaan di [audit](./08-audit.md), untuk menyimpan data perubahan ke audit handler.

``` java
public interface EntityPostListener {
    void onPostDelete(Object entity);
    void onPostInsert(Object entity);
    void onPostUpdate(Object entity);
}
```

## Screenshot

<div>
   <img src="./assets/entity.jpg" alt="Entity" title="Entity" width="800" />
</div>

##

### [Index](./index.md)
