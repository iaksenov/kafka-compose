### Пример запуска брокера Apache Kafka в режиме KRaft (без Zookeeper).
Сервис выполняет две роли: брокер и координатор.

Подключение SASL_PLAINTEXT на порту 9093, порт координатора кластера 9094 (также SASL_PLAINTEXT).\
Механизм хеширования SCRAM-SHA-256


#### Пример sasl-scram.properties для клиентов:
```properties
sasl.mechanism=SCRAM-SHA-256
security.protocol=SASL_PLAINTEXT
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="admin" password="admin-secret";
```

#### Добавление нового пользователя alice:
```bash
./kafka-configs.sh --bootstrap-server kafka:9092 --alter -add-config 'SCRAM-SHA-256=[iterations=8192,password=alice-secret]' --entity-type users --entity-name alice --command-config ./sasl-scram.properties
```

#### Добавление прав пользователю alice:
```bash
# На чтение из топика TOPIC:
./kafka-acls.sh --bootstrap-server kafka:9092 --command-config ./sasl-scram.properties --add --allow-principal User:alice --operation Read --operation Describe --operation DescribeConfigs --topic TOPIC
# На подписку в группе CONSUMER_GROUP:
./kafka-acls.sh --bootstrap-server kafka:9092 --command-config ./sasl-scram.properties --add --allow-principal User:alice --operation Read --operation Describe --group CONSUMER_GROUP
```

#### Links:
- [Use Access Control Lists (ACLs)](https://docs.confluent.io/platform/current/security/authorization/acls/overview.html)


