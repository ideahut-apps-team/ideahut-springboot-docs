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
``` java
public class TaskProperties {
	private Boolean allowCoreThreadTimeOut;
	private Integer awaitTerminationSeconds;	
	private Integer corePoolSize;	
	private Boolean daemon;	
	private Integer keepAliveSeconds;	
	private Integer maxPoolSize;	
	private Integer queueCapacity;	
	private String threadNamePrefix;	
	private Integer threadPriority;	
	private Boolean waitForJobsToCompleteOnShutdown;
}
```
- `allowCoreThreadTimeOut`: Ijinkan timeout thread saat eksekusi task (false / true).
- `awaitTerminationSeconds`: Waktu tunggu pada saat _shutdown_, dalam detik.
- `corePoolSize`: Minimum thread yang aktif di pool (meskipun tidak ada task yang dikerjakan).
- `daemon`: Task dikerjakan di latar atau tidak.
- `keepAliveSeconds`: Waktu tunggu thread selama eksekusi task, dalam detik.
- `maxPoolSize`: Maksimum thread yang aktif di pool.
- `queueCapacity`: Kapasitas antrian jika thread aktif di pool sudah penuh (sama dengan `maxPoolSize`).
- `threadNamePrefix`: Prefix nama thread.
- `threadPriority`: Priotitas thread.
- `waitForJobsToCompleteOnShutdown`: Tunggu semua task selesai dieksekusi pada saat _shutdown_.


##

### [Index](./index.md)
