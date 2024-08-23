# Filter

## Bean

### WebMvc

``` java
@Bean
FilterRegistrationBean<WebMvcRequestFilter> defaultRequestFilter(
    Environment environment,
    AppProperties appProperties,
    RequestMappingHandlerMapping handlerMapping
) {  
    return FrameworkUtil.createFilterBean(
        environment,
        new WebMvcRequestFilter()
            .setHandlerMapping(handlerMapping)
            .setCorsHeaders(appProperties.getCors())
            .setTraceEnable(true)
            .setEnableTimeResult(true)
            .initialize(), 
        1, 
        "/*"
    );
}
```

### WebFlux

``` java
@Bean
 WebFluxRequestFilter defaultRequestFilter(
    AppProperties appProperties,
    RequestMappingHandlerMapping requestMappingHandlerMapping,
    RootRequestInterceptor rootRequestInterceptor,
    AdminRequestInterceptor adminRequestInterceptor
) {
    return new WebFluxRequestFilter()
    .setCorsHeaders(appProperties.getCors())
    .setTraceEnable(true)
    .setAllowPaths("/**")
    .setTraceEnable(true)
    .setHandlerMapping(requestMappingHandlerMapping)
    .setInterceptors(rootRequestInterceptor, adminRequestInterceptor);
}
```

### CORS

``` md
cors:
    "Access-Control-Allow-Credentials": "true"
    "Access-Control-Allow-Origin": "*"
    "Access-Control-Allow-Methods": "*"
    "Access-Control-Max-Age": "360"
    "Access-Control-Allow-Headers": "*"
    "Access-Control-Expose-Headers": "*"
```

### Properties

* `corsHeaders` CORS headers
* `traceEnable` log MDC enable
* `traceGenerator` log MDC trace id generator
* `traceKey` key yang digunakan di log MDC
* `enableTimeResult` informasi waktu eksekusi di response result
