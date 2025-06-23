### Пример запуска брокера Apache Kafka в режиме KRaft (без Zookeeper).
Сервис выполняет две роли: брокер и координатор.

Подключение по SSL на порту 9092, подключение без шифрования на 9093, порт координатора кластера 9094.

Для настройки SSL используются PEM сертификаты, вместо JKS. 
> **Чтобы это работало, нельзя использовать подстроку "SSL" в KAFKA_ADVERTISED_LISTENERS, т.к. существует баг конфигурации контейнера в репе кафки (несовместимость с использованием PEM).**

#### Клиентам для подключения достаточно указать:
```properties
security.protocol=SSL
```

#### Для использования с shell командами из дистрибутива (типа kafka-consumer-groups.sh, kafka-topics.sh) необходимо дополнительно указать properties файл, например так:
```bash
./kafka-topics.sh --bootsrap-server kafka:9092 --command-config ./client-ssl.properties --list
```
В этом файле указать настройки подключения: `security.protocol=SSL` 

#### В OffsetExplorer, в настройках подключения:
- вкладка Security, Type = SSL.

Проверено на сертификатах, созданных при помощи mkcert.

> В качестве примера использован ресурс:
> https://github.com/codingharbour/kafka-docker-compose/blob/master/kafka-ssl/docker-compose.yml
