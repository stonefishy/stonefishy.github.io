---
title: How to use Paho MQTT Client Lib in Python3
date: 2025-01-06 14:57:57
tags: [MQTT, IoT, Python]
---

The `Paho MQTT client` library for Python is a popular choice for building MQTT applications in Python. Here's how to use it in Python3. `Paho MQTT Client` provides a simple and easy-to-use API for publishing and subscribing to MQTT topics. It supports MQTT 5.0, 3.1.1, and 3.1 protocols.

## Install Paho MQTT Client
To install `Paho MQTT Client` library, you can use the following command:

``` bash
pip3 install paho-mqtt
```

## Prepare the MQTT Broker
Before using the `Paho MQTT Client` library, we need to prepare the MQTT broker. We can use any MQTT broker that supports the MQTT 5.0, 3.1.1, or 3.1 protocols. Here we use the public free MQTT broker: `EMQX`, below is broker information.

> Server：broker.emqx.io
> TCP Port：1883
> WebSocket Port：8083
> SSL/TLS Port：8883
> Secure WebSocket Port：8084


## Import Paho MQTT Client
To use the `Paho MQTT Client` library in the Python code, need to import it first.

```python
from paho.mqtt import client as mqtt_client
```

## Create a MQTT Connection
Before create a MQTT connection, specify the MQTT client id, MQTT broker address, port and mesage topic. We can use the python `random.randint()` function to generate a random client id.

```python
broker = "broker.emqx.io"
port = 1883
topic = "paho/mqtt/test"
client_id = f"client_{random.randint(0, 1000)}"
```

Next, we need to write the `on_connect` callback function in order to connect to the proxy. This function is called after the client successfully connects, we can use the `rc` parameter to check the connection status. Typically, we also create a client object that is also connected to "broker.emqx.io".

``` python
def connect_mqtt():
    def on_connect(client, userdata, flags, reason_code, properties):
        if reason_code == 0:
            print("Connected to MQTT Broker!")
        else:
            print("Failed to connect, return code %d\n", reason_code)
    client = mqtt_client.Client(client_id=client_id, callback_api_version=mqtt_client.CallbackAPIVersion.VERSION2)

    client.on_connect = on_connect
    client.connect(broker, port)
    return client
```

When execute the `connect_mqtt()` function, it will return a `Client` instance that is connected to the MQTT broker, and printed the connection status.

{% image /assets/images/mqtt/mqtt-connected.png, width=800px, alt="MQTT Connected to Broker" %}

## Publish Messages to MQTT Topics
publish messages to MQTT topics, let's create a `publish()` function that will publish a message to the specified topic every second. Using `client.publish()` method, we can publish a message to the specified topic.

```python
def publish(client):
    msg_count = 1
    while True:
        time.sleep(1)
        msg = f"messages: test {msg_count}"
        result = client.publish(topic, msg)
        status = result[0]
        if status == 0:
            print(f"Send `{msg}` to topic `{topic}`")
        else:
            print(f"Failed to send message to topic {topic}")
        msg_count += 1
        if msg_count > 5:
            break
```

{% image /assets/images/mqtt/mqtt-publish.png, width=800px, alt="MQTT Publish Message" %}

## Subscribe to MQTT Topics
To subscribe to MQTT topics, we can call the `subscribe()` method of the `Client` instance. We can define a callback function that will be called when a message is received on a subscribed topic. The callback function `on_message()` will be called when a message is received on the subscribed topic.

```python
def subscribe(client: mqtt_client):
    def on_message(client, userdata, msg):
        print(msg)
        print(f"Received `{msg.payload.decode()}` which sent by `{client._client_id}` from `{msg.topic}` topic")

    client.subscribe(topic)
    client.on_message = on_message
```

{% image /assets/images/mqtt/mqtt-subscribe.png, width=800px, alt="MQTT Subscribe Message" %}


## Run the MQTT Client
To run the MQTT client, we can use `loop_start()` method to run the client in a separate thread, `loop_stop()` method to stop the client loop. We also can call the `loop_forever()` method of the `Client` instance. This method will block the current thread and run the client indefinitely. 

```python
client.loop_start() # start the client loop
client.loop_stop() # stop the client loop

# or
client.loop_forever() # block the current thread and run the client indefinitely
```

## Full Code
Here's the full code that uses the `Paho MQTT Client` library to connect to the MQTT broker, publish messages to a topic, and subscribe to a topic.

### client1.py
```python
import random
import time
from paho.mqtt import client as mqtt_client

broker = "broker.emqx.io"
port = 1883
topic = "paho/mqtt/test"
client_id = f"client_{random.randint(0, 1000)}"


def connect_mqtt():
    def on_connect(client, userdata, flags, reason_code, properties):
        if reason_code == 0:
            print("Connected to MQTT Broker!")
        else:
            print("Failed to connect, return code %d\n", reason_code)
    client = mqtt_client.Client(client_id=client_id, callback_api_version=mqtt_client.CallbackAPIVersion.VERSION2)

    client.on_connect = on_connect
    client.connect(broker, port)
    return client


def publish(client):
    msg_count = 1
    while True:
        time.sleep(1)
        msg = f"messages: test {msg_count}"
        result = client.publish(topic, msg)
        status = result[0]
        if status == 0:
            print(f"Send `{msg}` to topic `{topic}`")
        else:
            print(f"Failed to send message to topic {topic}")
        msg_count += 1
        if msg_count > 5:
            break


def run():
    client = connect_mqtt()
    client.loop_start()
    publish(client)
    client.loop_stop()


if __name__ == '__main__':
    run()

```

### client2.py
```python
import random
import time
from paho.mqtt import client as mqtt_client

broker = "broker.emqx.io"
port = 1883
topic = "paho/mqtt/test"
client_id = f"client_{random.randint(0, 1000)}"


def connect_mqtt():
    def on_connect(client, userdata, flags, reason_code, properties):
        if reason_code == 0:
            print("Connected to MQTT Broker!")
        else:
            print("Failed to connect, return code %d\n", reason_code)
    client = mqtt_client.Client(client_id=client_id, callback_api_version=mqtt_client.CallbackAPIVersion.VERSION2)

    client.on_connect = on_connect
    client.connect(broker, port)
    return client


def subscribe(client: mqtt_client):
    def on_message(client, userdata, msg):
        print(msg)
        print(f"Received `{msg.payload.decode()}` which sent by `{client._client_id}` from `{msg.topic}` topic")

    client.subscribe(topic)
    client.on_message = on_message


def run():
    client = connect_mqtt()
    subscribe(client)
    client.loop_forever()


if __name__ == '__main__':
    run()
```

That's it! You can now use the `Paho MQTT Client` library to build MQTT applications in Python3.
