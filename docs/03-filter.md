# Filter

Http request filter

## Bean

``` java
// WebMvc
@Bean
FilterRegistrationBean<WebMvcRequestFilter> defaultRequestFilter(
    Environment environment,
    AppProperties appProperties,
    RequestMappingHandlerMapping handlerMapping
) { 
    return WebMvcHelper.createFilterBean(
        environment,
        new WebMvcRequestFilter()
            .setHandlerMapping(handlerMapping)
            .setCORSHeaders(appProperties.getCors())
            .setTraceEnable(true)
            .setEnableTimeResult(true)
            .initialize(), 
        1, 
        "/*"
    );
}

// WebFlux
@Bean
WebFluxRequestFilter defaultRequestFilter(
    AppProperties appProperties,
    RequestMappingHandlerMapping requestMappingHandlerMapping,
    RootRequestInterceptor rootRequestInterceptor,
    AdminRequestInterceptor adminRequestInterceptor
) {
    return new WebFluxRequestFilter()
    .setCORSHeaders(appProperties.getCors())
    .setTraceEnable(true)
    .setAllowPaths("/**")
    .setHandlerMapping(requestMappingHandlerMapping)
    .setInterceptors(rootRequestInterceptor, adminRequestInterceptor);
}
```

- `setCORSHeaders`: CORS headers.
- `setTraceEnable`: log MDC enable.
- `setTraceGenerator`: log MDC trace id generator.
- `setTraceKey`: key yang digunakan di log MDC.
- `setEnableTimeResult`: informasi waktu eksekusi di response result.
- `setAllowPaths`: path yang di-_filter_.

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

##

### [Index](./index.md)
