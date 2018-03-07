# Client Testing

Once you have Zookeeper and Kafka deployed, you'll want to test you can produce and consume messages with an authenticated client. Here's how.

For testing purposes, so I can resolve the Mesos DNS names of the Broker endpoints, I use one of the masters and setup a testing directory in `/tmp/kafkatest`

In here needs to go the right `keytab, krb5.conf, client-jass.conf, client.properties` along with a generated `keystore` and `truststore` and then you need to launch a container with the `kafka-console-producer` and `kafka-console-consumer` and use them.

Or you automate all of that so you can just get on and produce and consume messages to validate it's working.
```
make setup-client-test
```

That does all of the above and SCPs all the assets to your master. Now as ever, my testing harness is based on AWS, so ymmv on your environment, amend the playbook as required.

Once the playbook's completed successfully you can run your test
```
ssh <ssh_user>@<master IP>
cd /tmp/kafkatest
./<kafka_cluster_identifier>-docker-kafka-client.sh
cd /tmp/kafkatest
./<kafka_cluster_identifier>-kafka-console-producer.test.sh
```

Kerberos debug testing is enabled with `echo $KAFKA_OPTS` so you're going to see a lot of messages spinning by. Unless you see obvious error messages like `password not found in database` or `could not construct producer` you're good and the message will be have written to the topic `securetest`

Now consume the message back
```
./<kafka_cluster_identifier>-kafka-console-producer.test.sh
```

Again you're going to see a lot of Kerberos debug chatter spin by, what you want to see is
```
This is an SSL test at Wed Mar  7 12:08:22 UTC 2018
```

Which constitutes success.