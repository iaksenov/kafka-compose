### Пример запуска брокера Apache Kafka в режиме KRaft (без Zookeeper).
Сервис выполняет две роли: брокер и координатор. \
Разные адрес:порт для внутренних (PLAINTEXT) и внешних (SSL) подключений. 

Подключение:
- SASL_SSL, b0.kafka.local:9092 (по-умолчанию ssl.enabled.protocols=[TLSv1.2, TLSv1.3])
- SASL_PLAINTEXT, 192.168.1.28:9093
- контроллер кластера 9094 (также SASL_PLAINTEXT).

Механизм хеширования SCRAM-SHA-256 (SCRAM-SHA-512 также поддерживается).

> **Шифрованное подключение только по порту 9092.** \
> **Данный способ позволяет при помощи CLI команд добавлять/удалять пользователей в runtime.**

Перед запуском кластера необходимо выполнять добавление SCRAM кредов на каждом контроллере. \
(см. command в docker-compose.yml)

#### Пример sasl-ssl.properties для внешних клиентов:
```properties
security.protocol=SASL_SSL
sasl.mechanism=SCRAM-SHA-256
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="admin" password="admin-secret";
# Если сертификат сервера подписан корневым сертификатом официального удостоверяющего центра, который есть в JDK, то его указывать тут не надо. 
ssl.truststore.location=cert/rootCA.pem
ssl.truststore.type=PEM
```

#### Добавление нового пользователя alice:
```bash
./kafka-configs.sh --bootstrap-server b0.kafka.local:9092 --alter -add-config 'SCRAM-SHA-256=[iterations=8192,password=alice-secret]' --entity-type users --entity-name alice --command-config ./sasl-ssl.properties
```

#### Добавление прав пользователю alice:
```bash
# На чтение из топика TOPIC:
./kafka-acls.sh --bootstrap-server b0.kafka.local:9092 --command-config ./sasl-ssl.properties --add --allow-principal User:alice --operation Read --operation Describe --operation DescribeConfigs --topic TOPIC
# На подписку в группе CONSUMER_GROUP:
./kafka-acls.sh --bootstrap-server b0.kafka.local:9092 --command-config ./sasl-ssl.properties --add --allow-principal User:alice --operation Read --operation Describe --group CONSUMER_GROUP
```

#### В OffsetExplorer, в настройках подключения по SSL:
- вкладка Security, Type = SASL SSL
- вкладка Advanced, SASL Mechanism = SCRAM-SHA-256
- вкладка JAAS Config: ```org.apache.kafka.common.security.scram.ScramLoginModule required username="admin" password="admin-secret";```
> **В OffsetExplorer 3.0.3 нет возможности указывать PEM сертификаты!** \
> Таким образом, если сертификат сервера отсутствует в цепочке доверия сертификатов в java, то понадобится корневой сертификат в truststore jks.

#### Links:
- [Use SASL/SCRAM authentication in Confluent Platform](https://docs.confluent.io/platform/current/security/authentication/sasl/scram/overview.html#use-sasl-scram-authentication-in-cp)
- [Use Access Control Lists (ACLs)](https://docs.confluent.io/platform/current/security/authorization/acls/overview.html)


