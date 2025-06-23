### Пример запуска брокера Apache Kafka в режиме KRaft (без Zookeeper).
Сервис выполняет две роли: брокер и координатор.

Подключение SASL_PLAINTEXT на порту 9093, порт координатора кластера 9094 (также SASL_PLAINTEXT).

В файле broker_jaas.conf указываются серверные креды для авторизации клиентов.

> **Данный способ не предполагает добавление пользователей в runtime!**\
> **Пользователи указываются в файле broker_jaas.conf, который подключается в KAFKA_OPTS**  
> ```-Djava.security.auth.login.config=``` 

В env KAFKA_LISTENER_NAME_CONTROLLER_PLAIN_SASL_JAAS_CONFIG указываются клиентские креды для подключения контроллера.\
KAFKA_SUPER_USERS имена суперпользователей, можно через запятую: User:admin1,User:admin2

#### Клиентам для авторизации подключения достаточно указать:
```properties
# Пример sasl_plain.properties
sasl.mechanism=PLAIN
security.protocol=SASL_PLAINTEXT
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username=admin password=admin-secret;
```

#### Для использования с shell командами из дистрибутива (типа kafka-consumer-groups.sh, kafka-topics.sh) необходимо дополнительно указать properties файл, например так:
```bash
./kafka-topics.sh --bootsrap-server kafka:9092 --command-config ./sasl_plain.properties --list
```
В этом файле указать настройки подключения указанные выше.

#### В OffsetExplorer, в настройках подключения:
- вкладка Security, Type = SASL Plaintext
- вкладка Advanced, SASL Mechanism = PLAIN
- вкладка JAAS Config: ```org.apache.kafka.common.security.plain.PlainLoginModule required username="admin" password="admin-secret";```
