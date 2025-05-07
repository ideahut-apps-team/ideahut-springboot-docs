# Binary Serializer
* Untuk _serialize_ / _deserialize_ object ke / dari byte array.
* Bisa berguna untuk menyimpan data biner ke penyimpanan seperti database dan [redis](./15-redis.md).
* Bisa berguna untuk service berbasis data biner.
``` java
public interface BinarySerializer {
	<T> byte[] serialize(Class<? extends T> type, T object);
	<T> void serialize(Class<? extends T> type, T object, OutputStream outputStream);
	
	<T> T deserialize(Class<T> type, byte[] bytes);
	<T> T deserialize(Class<T> type, InputStream inputStream);
}
```

## DataMapperBinarySerializer
[(Sumber)](https://github.com/FasterXML/jackson/) -> Menggunakan [Data Mapper](./02-mapper.md) untuk byte array dalam format json / xml.
``` java
// JSON
BinarySerializer binarySerializer = new DataMapperBinarySerializer()
.setMapper(dataMapper)
.setFormat(DataMapper.JSON);

// XML
BinarySerializer binarySerializer = new DataMapperBinarySerializer()
.setMapper(dataMapper)
.setFormat(DataMapper.XML);
```
* `setMapper`: [Data Mapper](./02-mapper.md) bean.
* `setFormat`: Format DataMapper.JSON atau DataMapper.XML.

## HessianBinarySerializer
[(Sumber)](http://hessian.caucho.com/) -> Mendukung versi 1 & 2.
``` java
BinarySerializer binarySerializer = new HessianBinarySerializer()
.setSerializerFactory(null)
.setVersion(null);
```
* `setSerializerFactory`: Untuk custom serializer factory.
* `setFormat`: Format hessian yang digunakan 1 atau 2.

## JdkBinarySerializer
[(Sumber)](https://docs.oracle.com/javase/7/docs/api/java/io/ObjectOutputStream.html) -> Menggunakan bawaan JDK.
``` java
BinarySerializer binarySerializer = new JdkBinarySerializer();
```

## KryoBinarySerializer
[(Sumber)](https://github.com/EsotericSoftware/kryo)
``` java
BinarySerializer binarySerializer = new KryoBinarySerializer()
.setAutoReset(null)
.setBufferSize(null)
.setClassLoader(null)
.setCopyReferences(null)
.setDefaultSerializerClass(null)
.setDefaultSerializerObject(null)
.setInstantiatorStrategy(null)
.setKryo(null)
.setMaxBufferSize(null)
.setMaxDepth(null)
.setOptimizedGenerics(null)
.setReferenceResolver(null)
.setReferences(null)
.setRegistrationRequired(null)
.setWarnUnregisteredClasses(null);
```
* `setKryo`: Jika ingin menggunakan object Kryo yang sudah ada.
* Untuk setter lain sama dengan penjelasan di [(Sumber)](https://github.com/EsotericSoftware/kryo).

## FuryBinarySerializer
[(Sumber)](https://fury.apache.org/)
``` java
BinarySerializer binarySerializer = new FuryBinarySerializer()
.setAsyncCompilationEnabled(null)
.setBasicTypesRefIgnored(null)
.setCheckClassVersion(null)
.setCheckJdkClassSerializable(null)
.setClassChecker(null)
.setClassLoader(null)
.setCodeGenEnabled(null)
.setCompatibleMode(null)
.setCompressInt(null)
.setCompressString(null)
.setCopyRef(null)
.setDeserializeNonexistentClass(null)
.setDeserializeNonexistentEnumValueAsNull(null)
.setDisableLogging(null)
.setFury(null)
.setInstanceType(null)
.setLanguage(null)
.setLongEncoding(null)
.setMaxPoolSize(null)
.setMetaCompressor(null)
.setMetaShareEnabled(null)
.setMinPoolSize(null)
.setName(null)
.setPoolExpiry(null)
.setRegisterGuavaTypes(null)
.setRequireClassRegistration(null)
.setScalaOptimizationEnabled(null)
.setScopedMetaShareEnabled(null)
.setSerializeEnumByName(null)
.setSerializerFactory(null)
.setStrictJava(null)
.setStringRefIgnored(null)
.setSuppressClassRegistrationWarnings(null)
.setTimeRefIgnored(null)
.setTrackingRef(null)
```
* `setFury`: Jika ingin menggunakan object Fury yang sudah ada.
* `setStrictJava`: Flag true (menggunakan fury.serializeJavaObject & fury.deserializeJavaObject), false (menggunakan fury.serialize & fury.deserialize).
* Untuk setter lain sama dengan penjelasan di [(Sumber)](https://fury.apache.org/docs/guide/java_object_graph_guide).

##

### [Index](./index.md)
