# Task

Untuk proses asinkronus.

## Bean

``` java
@Bean(destroyMethod = "shutdown")
protected TaskHandler commonTask() {
    return new TaskHandlerImpl()
    .setTaskProperties(appProperties.getTask().getCommon());
}
```

### Properties

``` md
common:
    threadNamePrefix: "TASK-COMMON"
    corePoolSize: 2
    maxPoolSize: 8
    waitForJobsToCompleteOnShutdown: true
    allowCoreThreadTimeOut: true
```
