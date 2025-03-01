# Instalasi

Untuk mengaktifkan library Ideahut Spring Boot di project.

## Maven

``` xml
<dependency>
    <groupId>net.ideahut</groupId>
    <artifactId>ideahut-springboot-3x</artifactId>
    <version>3.2.0</version>
</dependency>
<dependency>
    <groupId>net.ideahut</groupId>
    <version>3.2.0</version>
    <artifactId>ideahut-springboot-3.2.3</artifactId>
</dependency>
```

## Gradle

``` cmd
implementation group: 'net.ideahut', name: 'ideahut-springboot-3x', version: '3.2.0'
implementation group: 'net.ideahut', name: 'ideahut-springboot-3.2.3', version: '3.2.0'
```

Daftar versi dapat dilihat di:

- [Maven](https://mvnrepository.com/artifact/net.ideahut)
- [Sonatype](https://central.sonatype.com/search?namespace=net.ideahut)

## @SpringBootApplication

- Mengecek _dependencies_
- Menambahkan _applicationContext_ ke Runtime.shutdownHook
- Me-_reconfigure_ bean-bean yang ada di _applicationContext_
- Menampilkan versi yang digunakan

```java
@Slf4j
@SpringBootApplication
public class Application extends SpringBootServletInitializer implements ApplicationListener<ContextRefreshedEvent> {
 
    public static class Package {
        private Package() {}
        public static final String LIBRARY      = FrameworkHelper.PACKAGE;
        public static final String APPLICATION  = "net.ideahut.admin.central";
    }
 
    private static Closeable closeable;
    private static boolean isClosed = false;
    private static Runnable onConfigureError = () -> {
        if (closeable != null) {
            try {
                closeable.close();
                isClosed = true;
            } catch (Exception e) {
                log.error("", e);
            }
        }
    };
 
    private static boolean ready = false;
    private static void setReady(boolean b) {
        ready = b;
    }
    public static boolean isReady() {
        return ready;
    }
 
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(Application.class);
    }
 
    @Override
    public void onApplicationEvent(ContextRefreshedEvent event) {
        ApplicationContext applicationContext = event.getApplicationContext();
        FrameworkHelper.checkDependencies(applicationContext);
        BeanShutdown.RuntimeHook.register(applicationContext);
        log.info("Configuring application '" + applicationContext.getId() + "'...");
        AppProperties appProperties = applicationContext.getBean(AppProperties.class);
        TaskHandler taskHandler = applicationContext.getBean(TaskHandler.class);
        taskHandler.execute(() -> {
            long start = System.currentTimeMillis();
            try {
                BeanConfigure.runBeanConfigure(
                    !Boolean.FALSE.equals(appProperties.getWaitAllBeanConfigured()),
                    taskHandler, 
                    applicationContext,
                    onConfigureError,
                    EntityTrxManager.class,
                    AuditHandler.class
                );
            } catch (Exception e) {
                throw ErrorHelper.exception(e);
            }
            setReady(true);
            if (!isClosed) {
                log.info("Application has been configured in {} ms", System.currentTimeMillis() - start);
                runInit(taskHandler, applicationContext);
                VersionInfo versionInfo = FrameworkHelper.getVersionInfo();
                ApplicationInfo applicationInfo = FrameworkHelper.getApplicationInfo(applicationContext);
                writeInfo("Native           : ", applicationInfo.getInNativeImage());
                writeInfo("Reactive         : ", applicationInfo.getReactive());
                writeInfo("JDK              : ", versionInfo.getJava());
                writeInfo("Spring Framework : ", versionInfo.getSpringFramework());
                writeInfo("Spring Boot      : ", versionInfo.getSpringBoot());
                writeInfo("Hibernate        : ", versionInfo.getHibernate());
                writeInfo("Jedis            : ", versionInfo.getJedis());
                writeInfo("Quartz           : ", versionInfo.getQuartz());
                writeInfo("Kafka            : ", versionInfo.getKafka());
                writeInfo("Ideahut          : ", versionInfo.getIdeahut());
                int port = FrameworkHelper.getPort(applicationContext);
                log.info("Application '{}' is ready to serve on port {}", applicationContext.getId(), port);
            }
        });
    }
 
    private void writeInfo(String title, Object value) {
        if (title != null && value != null) {
            log.info("**** {} {}", title, value);
        }
    }
 
    private void runInit(TaskHandler taskHandler, ApplicationContext applicationContext) {
        taskHandler.execute(() -> {
            try {
                InitHandler initHandler = applicationContext.getBean(InitHandler.class);
                initHandler.initMapper(applicationContext);
                initHandler.initValidation();
                initHandler.initServlet();
            } catch (Exception e) {
                log.warn("InitHandler", e);
            }
        });
    }
 
    /*
     * MAIN
     */
    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(Application.class);
        application.setLazyInitialization(false);
        application.setLogStartupInfo(true);
        closeable = application.run(args);
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
