# Task

Task Executor dan proses asinkronus.

## Bean

``` java
@Bean(destroyMethod = "shutdown")
protected TaskHandler commonTask() {
    return new TaskHandlerImpl()
    .setTaskProperties(appProperties.getTask().getCommon());
}
```

### Properties

- `threadNamePrefix`: Prefix nama thread.
- `corePoolSize`: Minimum thread yang aktif di pool.
- `maxPoolSize`: Maksimum thread yang aktif di pool.
- `daemon`: Task dikerjakan di latar atau tidak.
- `allowCoreThreadTimeOut`: Batas waktu thread saat eksekusi task.
- `keepAliveSeconds`: Waktu tunngu thread selama eksekusi task, dalam detik.
- `threadPriority`: Priotitas thread.
- `queueCapacity`: Kapasitas antrian.
- `waitForJobsToCompleteOnShutdown`: Tunggu semua task selesai dieksekusi pada saat di-_shutdown_.
- `awaitTerminationSeconds`: Waktu tunggu setelah di-_shutdown_, dalam detik.

##

### [Index](./index.md)
