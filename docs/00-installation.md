# Instalasi

Untuk mengaktifkan library Ideahut Spring Boot di project.

## Maven
``` xml
<dependency>
    <groupId>net.ideahut</groupId>
    <artifactId>ideahut-springboot-3x</artifactId>
    <version>2.0.0</version>
</dependency>
<dependency>
    <groupId>net.ideahut</groupId>
    <version>2.0.0</version>
    <artifactId>ideahut-springboot-3.2.3</artifactId>
</dependency>
```

## Gradle
``` cmd
implementation group: 'net.ideahut', name: 'ideahut-springboot-3x', version: '2.0.0'
implementation group: 'net.ideahut', name: 'ideahut-springboot-3.2.3', version: '2.0.0'
```

Daftar versi dapat dilihat di:
- [Maven](https://mvnrepository.com/artifact/net.ideahut)
- [Sonatype](https://central.sonatype.com/search?namespace=net.ideahut)

## @SpringBootApplication
```java
@Slf4j
@SpringBootApplication
public class Application extends SpringBootServletInitializer implements ApplicationListener<ContextRefreshedEvent> {
	
    public static void main(String[] args) {
		SpringApplication application = new SpringApplication(Application.class);
		application.setLazyInitialization(false);
		application.setLogStartupInfo(true);
		application.run(args);
	}
	
	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
		return application.sources(Application.class);
	}
	
	@Override
	public void onApplicationEvent(ContextRefreshedEvent event) {
		ApplicationContext applicationContext = event.getApplicationContext();
		FrameworkUtil.checkDependecies(applicationContext);
		
		log.info("**** Initializing application '" + applicationContext.getId() + "'");
		
		TaskHandler taskHandler = applicationContext.getBean(AppConstants.Bean.Task.COMMON, TaskHandler.class);
		taskHandler.execute(() -> {
			try {
				BeanConfigure.runBeanConfigure(
					taskHandler, 
					applicationContext, 
					EntityTrxManager.class, 
					AuditHandler.class
				);
			} catch (Exception e) {
				throw FrameworkUtil.exception(e);
			}
			
			log.info("**** Reactive         : " + FrameworkUtil.isReactiveApplication(applicationContext));
			log.info("**** JDK              : " + System.getProperty("java.version"));
			log.info("**** Spring Framework : " + SpringVersion.getVersion());
			log.info("**** Spring Boot      : " + SpringBootVersion.getVersion());
			log.info("**** Hibernate        : " + Version.getVersionString());
			log.info("**** Ideahut          : " + IdeahutVersion.getVersion());
			log.info("**** Application '" + applicationContext.getId() + "' is ready to serve on port " + FrameworkUtil.getPort(applicationContext));
		});
	}
}
```
- Mengecek _dependencies_.
- Me-_reconfigure_ bean-bean yang digunakan.

## Template (Contoh)
- [Springboot 3x WebMvc](https://github.com/thomson470/ideahut-springboot-3x-template-mvc)
- [Springboot 3x WebFlux](https://github.com/thomson470/ideahut-springboot-3x-template-flux)
- [Springboot 2x WebMvc](https://github.com/thomson470/ideahut-springboot-2x-template-mvc)
- [Springboot 2x WebFlux](https://github.com/thomson470/ideahut-springboot-2x-template-flux)
