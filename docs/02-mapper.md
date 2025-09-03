# Data Mapper
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

## Properties

``` java
MapperProperties mprops = MapperProperties.create()
.setMapperFeature(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
.setMapperFeature(SerializationFeature.FAIL_ON_EMPTY_BEANS, false)
.setFactoryFeatures(YAMLGenerator.Feature.WRITE_DOC_START_MARKER, true)
.setFindAndRegisterModules(Boolean.TRUE);
if (!includeNull) {
    mprops.setSerializationInclusion(JsonInclude.Include.NON_NULL);
}
```
* `setMapperFeature`: Mengaktifkan atau tidak fitur-fitur mapper (contoh DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES & SerializationFeature.FAIL_ON_EMPTY_BEANS).
* `setFactoryFeatures`: Mengaktifkan atau tidak fitur-fitur factory (contoh YAMLGenerator.Feature.WRITE_DOC_START_MARKER).
* `setSerializationInclusion`: Contoh tidak menyertakan null property pakai JsonInclude.Include.NON_NULL
* `setFindAndRegisterModules`: Cari dan tambah module dari classpath secara otomatis.

##

### [Index](./index.md)
