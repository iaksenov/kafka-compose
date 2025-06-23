### Пример запуска брокера Apache Kafka в режиме KRaft (без Zookeeper).
Сервис выполняет две роли: брокер и координатор.

Подключение SASL_PLAINTEXT на порту 9093, порт координатора кластера 9094 (также SASL_PLAINTEXT).\
Механизм хеширования SCRAM-SHA-256 (SCRAM-SHA-512 также поддерживается).

> **Данный способ позволяет при помощи CLI команд добавлять/удалять пользователей в runtime.**

Перед запуском кластера необходимо выполнять добавление SCRAM кредов на каждом контроллере.\
(см. command в docker-compose.yml)

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

#### В OffsetExplorer, в настройках подключения:
- вкладка Security, Type = SASL Plaintext
- вкладка Advanced, SASL Mechanism = SCRAM-SHA-256
- вкладка JAAS Config: ```org.apache.kafka.common.security.scram.ScramLoginModule required username="admin" password="admin-secret";```

#### Links:
- [Use SASL/SCRAM authentication in Confluent Platform](https://docs.confluent.io/platform/current/security/authentication/sasl/scram/overview.html#use-sasl-scram-authentication-in-cp)
- [Use Access Control Lists (ACLs)](https://docs.confluent.io/platform/current/security/authorization/acls/overview.html)


