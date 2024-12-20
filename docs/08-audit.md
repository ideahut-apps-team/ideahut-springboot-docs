# Audit

Untuk menyimpan perubahan data entity / model.
Akan disimpan tanggal perubahan, pengubah/user, aksi (INSERT / UPDATE / DELETE), dan data perubahan.
Proses penyimpanan dilakukan asinkronus.

## Multi

Semua table entity (yang ada anotasi @Audit) akan diduplikat, ditambah dengan field tanggal (entry), pengubah (actor), informasi tambahan (info), dan aksi (action).

``` java
@Bean
AuditHandler auditHandler(
    EntityTrxManager entityTrxManager,
    TaskHandler taskHandler
) {
    return new DatabaseMultiAuditHandler()
    .setEntityTrxManager(entityTrxManager)
    .setProperties(appProperties.getAudit().getProperties())
    .setTaskHandler(taskHandler)
    .setRejectNonAuditEntity(true);
}
```

## Single

Satu TransactionManager hanya disimpan ke satu table. Jadi semua perubahan entity/model akan disimpan ke satu table.

``` java
@Bean
AuditHandler auditHandler(
    EntityTrxManager entityTrxManager,
    TaskHandler taskHandler
) {
    return new DatabaseSingleAuditHandler()
    .setEntityTrxManager(entityTrxManager)
    .setProperties(appProperties.getAudit().getProperties())
    .setTaskHandler(taskHandler);
}
```

## Properties

``` java
public class DatabaseAuditProperties implements Serializable { 
    private Table table;
    private Column column;
    private Enable enable;
    private Generate generate; 
    private Length length;
 
    @Setter
    @Getter
    public static class Table implements Serializable {
        private String prefix; // prefix nama table yang akan digenerate
        private String suffix; // suffix nama table yang akan digenerate
    }  
 
    @Setter
    @Getter
    public static class Column implements Serializable {
        private String replica; // nama kolom replica
        private String actor;   // nama kolom actor
        private String action;  // nama kolom action
        private String info;    // nama kolom info
        private String entry;   // nama kolom entry
    }
 
    @Setter
    @Getter
    public static class Length implements Serializable {
        private Integer id;         // panjang karakter kolom id
        private Integer type;       // panjang karakter kolom type
        private Integer action;     // panjang karakter kolom action
        private Integer auditor;    // panjang karakter kolom auditor
        private Integer info;       // panjang karakter kolom info
    }
 
    @Setter
    @Getter
    public static class Enable implements Serializable {
       private Boolean audit;  // enable audit
       private Boolean rowid;  // enable rowid
       private Boolean index;  // enable index (index akan dibuat di table audit)
       private Boolean any;
    }  
 
    @Setter
    @Getter
    public static class Generate implements Serializable {
       private Boolean table;          // otomatis buat table atau tidak
       private Integer maxPrecision;   // maksimum precision
       private Integer maxScale;       // maksimum scale
    }
 
}
```

## Screenshot

<div>
   <img src="./assets/audit.jpg" alt="Audit" title="Audit" width="800" />
</div>

##

### [Index](./index.md)
