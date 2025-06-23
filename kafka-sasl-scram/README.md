





sasl.mechanism=SCRAM-SHA-256
security.protocol=SASL_PLAINTEXT
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required username="admin" password="admin-secret";


./kafka-configs.sh --bootstrap-server 192.168.1.28:9093 --alter -add-config 'SCRAM-SHA-256=[iterations=8192,password=alice-secret]' --entity-type users --entity-name alice --command-config ./sasl-scram.properties


SERVER=192.168.1.28:9093
READ_TOPIC=test
CONSUMER_GROUP=alice

USER=alice
# READ topic
./kafka-acls.sh --bootstrap-server ${SERVER} --command-config ./sasl-scram.properties --add --allow-principal User:${USER} --operation Read --operation Describe --operation DescribeConfigs --topic ${READ_TOPIC}
# Consumer group 
./kafka-acls.sh --bootstrap-server ${SERVER} --command-config ./sasl-scram.properties --add --allow-principal User:${USER} --operation Read --operation Describe --group ${CONSUMER_GROUP}



