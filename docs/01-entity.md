# Entity Transaction Manager

* Men-_scan_ semua entity/model dari setiap _Transaction Manager_ yang tersedia (bisa lebih dari satu).
* Dapat digunakan untuk transaksi tanpa menggunakan DAO _Repository_.
* Digunakan di [CRUD](./02-crud.md), [Audit](./04-audit.md), & [Grid](./03-grid.md).
* Mendukung hibernate query (hql) maupun native query (sql).
* Mendukung _replica_ (satu entity dimapping ke banyak table), contoh: entity User di-_mapping_ ke table user_1, user_2, user_3, dst.

## Bean

``` java
@Bean
EntityTrxManager entityTrxManager() {
    return new EntityTrxManagerImpl();
}
```

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
Contoh penggunaan di [cache](./05-cache.md), untuk membuang data yang tersimpan di memori.

``` java
public interface EntityPreListener { 
    void onPreDelete(Object entity);
    void onPreUpdate(Object entity);
    void onPreInsert(Object entity); 
}
```

## PostListener

Listener setelah entity mengalami perubahan (INSERT, UPDATE, & DELETE).
Contoh penggunaan di [audit](./04-audit.md), untuk menyimpan data perubahan ke audit handler.

``` java
public interface EntityPostListener {
    void onPostDelete(Object entity);
    void onPostInsert(Object entity);
    void onPostUpdate(Object entity);
}
```

## Screenshot

<div align="center">
   <img src="./images/entity.jpg" alt="Entity" title="Entity" width="800" />
</div>
