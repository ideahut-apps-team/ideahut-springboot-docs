# Filter

Http request filter.

## Bean

``` java
// WebMvc
@Bean
FilterRegistrationBean<WebMvcRequestFilter> defaultRequestFilter(
    Environment environment,
    AppProperties appProperties,
    RequestMappingHandlerMapping handlerMapping
) {	
    FilterDefinition filter = ObjectHelper.useOrDefault(
        appProperties.getFilter(), 
        FilterDefinition::new
    );
    return WebMvcHelper.createFilterBean(
        environment,
        new WebMvcRequestFilter()
            .setCorsHeaders(filter.getCorsHeaders())
            .setEnableTimeResult(filter.getEnableTimeResult())
            .setHandlerMapping(handlerMapping)
            .setTraceEnable(filter.getTraceEnable())
            .setTraceKey(filter.getTraceKey())
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
    FilterDefinition filter = ObjectHelper.useOrDefault(
        appProperties.getFilter(), 
        FilterDefinition::new
    );
    return new WebFluxRequestFilter()
    .setCorsHeaders(filter.getCorsHeaders())
    .setEnableTimeResult(filter.getEnableTimeResult())
    .setTraceEnable(filter.getTraceEnable())
    .setTraceKey(filter.getTraceKey())
    .setAllowPaths("/**")
    .setHandlerMapping(requestMappingHandlerMapping)
    .setInterceptors(rootRequestInterceptor, adminRequestInterceptor);
}
```

- `setCorsHeaders`: CORS headers.
- `setTraceEnable`: log [MDC](https://logback.qos.ch/manual/mdc.html) diaktifkan atau tidak.
- `setTraceGenerator`: log [MDC](https://logback.qos.ch/manual/mdc.html) trace id generator.
- `setTraceKey`: key yang digunakan di log [MDC](https://logback.qos.ch/manual/mdc.html).
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
