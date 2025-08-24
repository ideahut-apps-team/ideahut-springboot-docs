# Task

Task Executor dan proses asinkronus.

``` java
public interface TaskHandler {

	// submit map callables, dan result diambil berdasarkan key
	Map<String, TaskResult> submit(Map<String, Callable<?>> callables, TimeValue timeout);
	Map<String, TaskResult> submit(Map<String, Callable<?>> callables);	
	
	// submit list callables, dan result diambil berdasarkan index
	List<TaskResult> submit(List<Callable<?>> callables, TimeValue timeout);
	List<TaskResult> submit(List<Callable<?>> callables);
	
	// submit single callable
	<T> Future<T> submit(Callable<T> callable);
	
	// execute multi tasks
	void execute(Collection<Runnable> tasks);

	// execute single task
	void execute(Runnable task);
}
```

## Bean

``` java
@Bean(destroyMethod = "shutdown")
TaskHandler taskHandler() {
    return new TaskHandlerImpl()
    .setTaskProperties(appProperties.getTask().getCommon());
}
```

### Properties

- `threadNamePrefix`: Prefix nama thread.
- `corePoolSize`: Minimum thread yang aktif di pool.
- `maxPoolSize`: Maksimum thread yang aktif di pool.
- `daemon`: Task dikerjakan di latar atau tidak.
- `allowCoreThreadTimeOut`: Ada timeout thread saat eksekusi task (false / true).
- `keepAliveSeconds`: Waktu tunggu thread selama eksekusi task, dalam detik.
- `threadPriority`: Priotitas thread.
- `queueCapacity`: Kapasitas antrian jika thread aktif di pool sudah penuh.
- `waitForJobsToCompleteOnShutdown`: Tunggu semua task selesai dieksekusi pada saat _shutdown_.
- `awaitTerminationSeconds`: Waktu tunggu setelah _shutdown_, dalam detik.

##

### [Index](./index.md)
