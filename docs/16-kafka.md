# Kafka
Untuk meng-_handle_ producer dan consumer kafka.

```java
public interface KafkaHandler {
	KafkaProperties getProperties();
	BinarySerializer getBinarySerializer();
	
	// INTERNAL
	<K, V> KafkaSender<K, V> getSender(String topic);
	<K, V, R> ReplyKafkaSender<K, V, R> getReplySender(String topic);
	
	// DYNAMIC
	<K, V> KafkaSender<K, V> createSender(String topic);
	<K, V, R> ReplyKafkaSender<K, V, R> createReplySender(String topic);
}
```

## Bean

``` java
@Bean
KafkaHandler kafkaHandler(
    AppProperties appProperties
) {
    return new KafkaHandlerImpl()
    .setProperties(null)
    .setConfigurationFile(appProperties.getKafkaConfigurationFile());
}
```

- `setProperties`: Kafka properties, atau bisa juga menggunakan configuration file.
- `setConfigurationFile`: Kafka properties yang disimpan ke file, [contoh file](./assets/kafka.yaml).

## Screenshot

<div>
   <img src="./assets/kafka.jpg" alt="Kafka" title="Kafka" width="800" />
</div>

##

### [Index](./index.md)
