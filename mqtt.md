# MQTT Introduction
MQTT works on [publisher/subscriber pattern](https://realtimeapi.io/hub/publishsubscribe-pattern/). In a pub/sub connection, two clients i.e. publisher and subscriber, never contact directly and instead communication between them is handled by a third party. In MQTT we call this third-party as Broker.

## How MQTT protocol works 
* To communicate with each other, publisher and subscriber only needs to know IP and port of the broker. This removes dependecy of each client knowing address of other client.
* Even though most messages are delivered on the go, Mqtt also supports storing of messages. If desired, broker can store these messages when a client is not online. For this client needs to connect using a persistent session with [QOS](https://assetwolf.com/learn/mqtt-qos-understanding-quality-of-service) greater than zero.
* MQTT mostly works in an synch manner which results in no lagging in delivery.
* MQTT uses subject based filtering and broker uses "Topics" to decide on which subscriber is to recieve which message.

## Client, Broker and Connection

![Mqtt protocol](https://www.dataweek.co.za/articles/Dataweek%20-%20Published%20by%20Technews/k4588.png)

### Client
* In MQTT, both publishers and subscribers are clients.
* A client can act both as a pub/sub (name only signifies sending and recieving party).
* Ease of implementation of MQTT client is what makes it perfect to use.
* Example : micro-controller, pi, AWS server running on MQTT, etc.
* Different languages and client libraries are available [here on wiki](https://github.com/mqtt/mqtt.github.io/wiki/libraries).

### Broker
* Broker acts as a mediator between publisher and subscriber.
* MQTT brokers can handle thousands of messages at a time due to its simlified architecture. Here is a comparison between [Mqtt and Http protocols](https://medium.com/mqtt-buddy/mqtt-vs-http-which-one-is-the-best-for-iot-c868169b3105)
* Work done by broker :
	* Recieve all messages
	* [Filter](https://subscription.packtpub.com/book/application_development/9781787287815/1/ch01lvl1sec9/working-with-message-filtering) these messages
	* Find out subscribers to a particular topic related to message
	* Send these message to those subscribed clients.
* Broker can also hold missed messages of persisted clients. 
* Broker is responsible for handling authentication and authorization.

### MQTT Connection
* MQTT broker and clients both are TCP/IP based.
* Connection is always between one broker and one client.
* Some important parameters needed for the connection :
	* "clientId" : Id should be unique per client and is needed to maintain state of the client. This will help to deliver new messages to the client which were not delivered to it when it was offline.
	* "cleanSession" : Equals to false, if you want tp establish persistent session and store these subscriptions.
	* "username/password" : sent in plain text of not encrypted.
	* "willMessage" : Message to be sent by the disconnecting client to all clients of that topic in case it gets disconnected.

#### Need for clientId
ClientID is needed to identify an application mainly when a client is using persistent subscriptions. When these subscriptions are offilne, broker creates a backup of messages been delivered to them and when such applications come on-line again, a messaging provider has to identify these subscriptions and there messages. Broker then compares the persisted clientId across clients that came online and delivers messages to them.

### Publish/Subscribe in MQTT
* A MQTT Publisher client can start publishing messaged as soon as it connects to the broker. Each message will contain a topic and payload to be sent.
* A MQTT Subscriber are the ones that receives messages that are published to a particular topic.


### Topics
* Topic is a string that broker uses to filter messages that needs to be sent to the subscribers.
* Topics consist of topic levels separated by a "/". Example :
```
"home/ground/room/temp"
```
* Properties :
	* Must contain atleast 1 character
	* Can contain empty spaces
	* "/" is a valid topic name

"MQTT Topics" is an interesting concept. [Click here](http://www.steves-internet-guide.com/understanding-mqtt-topics/) for further information or read [AWS guide on Topic](https://docs.aws.amazon.com/iot/latest/developerguide/topics.html).

### Relevant Reads
Apart from above mentioned details, there are few more topics that needs your attention :
* [Quality of Service](https://assetwolf.com/learn/mqtt-qos-understanding-quality-of-service) : QoS is a key feature of the MQTT protocol. QoS gives the client the power to choose a level of service that matches its network reliability and application logic. Because MQTT manages the re-transmission of messages and guarantees delivery (even when the underlying transport is not reliable), QoS makes communication in unreliable networks a lot easier.
* Persistent Session in MQTT : Re-subscribing every time the connection is interrupted is a burden for constrained clients with limited resources. To avoid this problem, the client can request a persistent session when it connects to the broker. Persistent sessions save all information that is relevant for the client on the broker. Read : [AWS Doc](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt-persistent-sessions.html), [IBM doc](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/tt60370_.htm).
* Last Will : This basically is a message that will notify other clients subscribed to a particular topic that this client has los connection. [HiveMQ Read](https://www.hivemq.com/blog/mqtt-essentials-part-9-last-will-and-testament/).



# Setting up MQTT
I will be using Mosquitto MQTT Broker and Paho as a client in Java code on a linux system. Setting up Mosquitto in linux is pretty easy : 

## Setting up Mosquitto Broker
In your linux terminal, run following command :
```
sudo apt-get update
sudo apt-get install mosquitto
```

This will automatically start mosqutto as a service. If it doesnt, then run command :
```
sudo systemctl start mosquitto
```

## Setting up Mosquitto client and testing it
* Run in terminal to install client :
```
sudo apt-get install mosquitto-clients
```

* In this terminal window, subscribe a client to topic "test/topic" :
```
mosquitto_sub -t "test/topic"
```

* In another terminal window, create a publisher and publish some message and you will see this message appear in subscriber terminal.
```
mosquitto_pub -m "You know nothing snow" -t "test/topic"
```

## Securing with Password
* Mosquitto provides a password generation utility : 'mosquitto_passwd'. Run following command :
```
sudo mosquitto_passwd -c /etc/mosquitto/passwd <username>
```

* This wil prompt you to enter a password :
```
sudo mosquitto_passwd -c /etc/mosquitto/passwd user
Password: <password>
```

* Create a configuration file pointing to this above created password file :
```
sudo gedit /etc/mosquitto/conf.d/default.conf
```

* This will open this empty "default.conf" file. Save following detail in this file and restart mosquitto :
```
allow_anonymous false
password_file /etc/mosquitto/passwd
```
```
sudo systemctl restart mosquitto
```

* Now you will only be able to connect to broker using respective username and password :
```
sudo mosquitto_sub -t "test" -u "dave" -P "password"
```
```
sudo mosquitto_pub -t "test" -m "King in the north" -u "user" -P "<password>"
```

* In case you try to connect without or incorrect username/password, you  will be thrown an error :
```
Connection Refused: not authorised.
Error: The connection was refused.
```

> Reference : [Vultr doc on setting up](https://www.vultr.com/docs/how-to-install-mosquitto-mqtt-broker-server-on-ubuntu-16-04)


<!--

# MQTT Code
Tech-stack for this :
```
* Springboot
* Mosquitto Broker
* Paho Client
* Linux system
```

1) Broker setup and configuration
2) Client in Spring code

## Inbound vs outbound channels
* Channel adapters are for one-way integration (gateways are bidirectional).
* Concretely, inbound adapters are at the beginning of a flow, outbound adapters terminate a flow. Flows are typically rendered (and conceptually thought of as flowing from left to right). Here -> represents a channel : 

> inbound-c-a->someComponent->someOtherComponent->outbound-ca

* There are two types of inbound channel adapters:
	* MessageProducers are termed "message-driven" i.e. they unilaterally produce messages in a completely asynchronous manner, as soon as they are started; examples are JMS message-driven adapter, TCP inbound channel adapter, IMAP Idle (mail) channel adapter, etc.

	* MessageSources on the other hand are polled - a poller with some trigger causes the framework to ask the source for a message; the trigger can be on a fixed rate, cron expression etc. Examples are the (S)FTP adapters, Mail inbound adapter (POP3. IMAP).

	Examples of outbound adapters are Mail outbound adapter (SMTP).

* Examples of outbound adapters are Mail outbound adapter (SMTP).

* Gateways are two-way (request/reply).

Inbound gateways are where some external system sends a request and Spring Integration replies.

Outbound gateways are where Spring Integration makes the request and some external system replies.


-->