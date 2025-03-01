# Mapper
Untuk _serialize_ / _deserialize_ object ke / dari format json, xml, & yaml.

## Bean

``` java
// JSON & XML
@Bean
DataMapper dataMapper() {
    return new DataMapperImpl();
}

// YAML
@Bean
YamlMapper yamlMapper() {
    return new YamlMapperImpl();
}
```

##

### [Index](./index.md)
