# Instalasi

Untuk mengaktifkan library Ideahut Spring Boot di project.

## Maven

``` xml
<dependency>
    <groupId>net.ideahut</groupId>
    <artifactId>ideahut-springboot-3x</artifactId>
    <version>25.12.12</version>
</dependency>
<dependency>
    <groupId>net.ideahut</groupId>
    <artifactId>ideahut-springboot-3.2.3</artifactId>
	<version>25.12.12</version>
</dependency>
```

## Gradle

``` cmd
implementation group: 'net.ideahut', name: 'ideahut-springboot-3x', version: '25.12.12'
implementation group: 'net.ideahut', name: 'ideahut-springboot-3.2.3', version: '25.12.12'
```

Daftar versi dapat dilihat di:

- [Maven](https://mvnrepository.com/artifact/net.ideahut)
- [Sonatype](https://central.sonatype.com/search?namespace=net.ideahut)

## @SpringBootApplication

- Mengecek _dependencies_ dan bean-bean yang wajib ada.
- Menambahkan _applicationContext_ ke Runtime.shutdownHook.
- Mengkonfigurasi bean-bean yang ada di _applicationContext_.
- Menampilkan versi _library_ yang digunakan.

```java
@SpringBootApplication
public class Application extends WebMvcLauncher {
	
	public static class Package {
		private Package() {}
		public static final String LIBRARY		= FrameworkHelper.PACKAGE;
		public static final String APPLICATION	= "net.ideahut.springboot.template";
	}
	
	private static Closeable runningApp;
	private static boolean ready = false;
	private static void setReady(boolean b) { ready = b; }
	public static boolean isReady() { return ready; }
	
	private final TaskHandler taskHandler;
	
	@Autowired
	Application(
		TaskHandler taskHandler
	) {
		this.taskHandler = taskHandler;
	}
	
	@Override
	protected Class<? extends ApplicationLauncher> source() {
		return Application.class;
	}
	
	@Override
	public Closeable runningApp() {
		return runningApp;
	}

	@Override
	public TaskHandler taskHandler() {
		return taskHandler;
	}

	@Override
	public Class<?>[] initialBeanConfigureTypes() {
		return new Class<?>[] {
			EntityTrxManager.class,
			SysParamHandler.class,
			AuditHandler.class
		};
	}

	@Override
	public void onReady(ApplicationContext applicationContext) {
		setReady(true);
		RuntimeHintsConfig.registerToNativeImageAgent(applicationContext);
		super.runInit(applicationContext, !FrameworkHelper.inNativeImage().booleanValue());
	}
	
	
	/*
	 * MAIN
	 */
	public static void main(String[] args) {
		SpringApplication application = new SpringApplication(Application.class);
		application.setLazyInitialization(false);
		application.setLogStartupInfo(true);
		runningApp = application.run(args);
	}
	
}
```

## Template

- [Springboot 3x WebMvc](https://github.com/thomson470/ideahut-springboot-3x-template-mvc)
- [Springboot 3x WebFlux](https://github.com/thomson470/ideahut-springboot-3x-template-flux)
- [Springboot 2x WebMvc](https://github.com/thomson470/ideahut-springboot-2x-template-mvc)
- [Springboot 2x WebFlux](https://github.com/thomson470/ideahut-springboot-2x-template-flux)

##

### [Index](./index.md)
