# Filter

## Bean

### Mvc
``` java
@Bean
protected FilterRegistrationBean<DefaultRequestFilter> defaultRequestFilter() {		
    return FrameworkUtil.createFilterBean(
        environment,
        new DefaultRequestFilter()
            .setCORSHeaders(appProperties.getCors())
            .setRequestWrapperEnable(true)
            .setTraceEnable(true)
            .initialize(), 
        1, 
        "/*"
    );
}
```

### Reactive
``` java
@Autowired
private RequestMappingHandlerMapping requestMappingHandlerMapping;

@Bean
protected ReactiveRequestFilter defaultRequestFilter() {
    return new ReactiveRequestFilter()
    .setCORSHeaders(appProperties.getCors())
    .setTraceEnable(true)
    .setAllowPaths("/**")
    .setHandlerMapping(requestMappingHandlerMapping)
    .setInterceptors(rootRequestInterceptor, adminRequestInterceptor);
}
```

## CORS
``` md
cors:
    "Access-Control-Allow-Credentials": "true"
    "Access-Control-Allow-Origin": "*"
    "Access-Control-Allow-Methods": "*"
    "Access-Control-Max-Age": "360"
    "Access-Control-Allow-Headers": "*"
    "Access-Control-Expose-Headers": "*"
```