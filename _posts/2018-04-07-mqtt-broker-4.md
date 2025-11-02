---
layout: post
title: Installing a Home MQTT Broker (and IV)
subtitle: Adding user authentication and access control
cover-img: /assets/img/posts/BrokerMQTTDomestico-Destacada.jpg
thumbnail-img: /assets/img/posts/mqtt-broker-thumb.jpg
share-img: /assets/img/path.jpg
tags: [testbed]
author: Manuel Domínguez-Dorado
---

Apologies again for the long gap since the third part of this series. I’ve been overwhelmed like I haven’t been in a long time. Anyway, here is the fourth and final installment I promised for our home MQTT broker.

In the previous three articles we covered interesting topics:

*   Hardware upgrade of a Fujitsu Futro Thin Client to use it as a home MQTT broker.
*   Installation of Ubuntu Server and the Mosquitto MQTT broker.
*   Configuration of everything necessary for Mosquitto to serve MQTT connections over TLS.

What remained for this last post was the configuration of ACLs and user authentication using the basic mechanisms provided by Mosquitto. Let’s get to it!

***

## User Authentication and ACL (Access Control List)

It’s not the first time I’ve seen people confuse one with the other, so let’s clarify what both concepts are and why we should use them.

### User Authentication

By default, as we left the MQTT broker configured in the third part of this series, any user can access the broker and use it. Access all topics, subscribe, publish, etc. That’s not usually what we want. Instead, it’s normal to tightly control which users have permission to connect to the broker and use its resources. User authentication refers precisely to that.

In my specific case, for the project I need this broker for, I require that no one can access it by default. And there will be three or four users who will be granted permission under certain conditions (which have nothing to do with technical aspects, but rather business ones). Well, Mosquitto provides basic built-in mechanisms to maintain a list of user/password pairs. We’ll learn to configure this mechanism so our broker isn’t wide open.

### ACLs

User authentication allows coarse-grained control over who accesses the broker’s resources. Usually, we first allow certain users to connect, but then we need to ensure not all connected users can access any topic. We need fine-grained control. Although it can be an open system, it’s reasonable, recommended, and common for each user to access only certain topics of the MQTT broker. And also to specify the access mode (read or write, or more accurately, subscribe and publish).

With the ACL mechanism, we can specify things like “User ‘luis’ can subscribe to the topic notifications/servers/server1 and only that one, and cannot publish to any topic” and “User ‘juan’ can publish to the topic notifications/servers/server1 and only that one, but cannot subscribe to any topic.” This way, Juan would be a content publisher and Luis a consumer, both working differently on the same topic.

We’ll see how Mosquitto allows us to do this and what additional options we have, such as using wildcards or parameters.

***

## Enabling and Configuring User Authentication in Mosquitto

To enable user authentication in Mosquitto, as always, we modify some parameters in the `mosquitto.conf` configuration file. Specifically, the parameter:

```text
password_file filepath
```

This specifies the path and filename of the file that will contain the list of user/password pairs allowed to connect with authentication.

Edit `/etc/mosquitto/mosquitto.conf` and add:

```text
allow_anonymous false
password_file /etc/mosquitto/users/passwd
```

Now create the directory and file:

```bash
sudo mkdir /etc/mosquitto/users
sudo mosquitto_passwd -c /etc/mosquitto/users/passwd usuario1
```

Then add more users:

```bash
sudo mosquitto_passwd /etc/mosquitto/users/passwd usuario2
sudo mosquitto_passwd /etc/mosquitto/users/passwd usuario3
```

Reload Mosquitto:

```bash
sudo service mosquitto reload
```

To test:

```bash
mosquitto_sub --cafile MiCA.crt --tls-version tlsv1 --insecure -h 192.168.1.20 -p 8883 -t "UnTopicCualquiera" -v
```

Expected output:

    Connection Refused: not authorised.

Now connect with credentials:

```bash
mosquitto_sub --cafile MiCA.crt --tls-version tlsv1 --insecure -h 192.168.1.20 -p 8883 -t "UnTopicCualquiera" -v -u usuario1 -P passwordusuario1
```

And publish:

```bash
mosquitto_pub --cafile MiCA.crt --tls-version tlsv1 --insecure -h 192.168.1.20 -p 8883 -t "UnTopicCualquiera" -m Hola -u usuario2 -P passwordusuario2
```

***

## Enabling the ACL System in Mosquitto

Add to `mosquitto.conf`:

```text
acl_file /etc/mosquitto/users/acl
```

Create `/etc/mosquitto/users/acl` and define:

```text
user usuario1
topic write usuarios/sinprivilegios/publicaciones/usuario1
topic read usuarios/administradores/publicaciones

user usuario2
topic write usuarios/sinprivilegios/publicaciones/usuario2
topic read usuarios/administradores/publicaciones

user usuario3
topic write usuarios/administradores/publicaciones
topic read usuarios/sinprivilegios/publicaciones/#
```

Reload Mosquitto:

```bash
sudo service mosquitto reload
```

Test:

```bash
mosquitto_sub --cafile MiCA.crt --tls-version tlsv1 --insecure -h 192.168.1.20 -p 8883 -t "usuarios/sinprivilegios/publicaciones/#" -v -u usuario3 -P passwordUsuario3
```

Then publish:

```bash
mosquitto_pub --cafile MiCA.crt --tls-version tlsv1 --insecure -h 192.168.1.20 -p 8883 -t "usuarios/sinprivilegios/publicaciones/usuario2" -m Hola -u usuario2 -P passwordUsuario2
```

Expected output:

    usuarios/sinprivilegios/publicaciones/usuario2 Hola

***

## Tip

User authentication is not problematic. However, topic access control via ACLs can be a headache. Especially with complex topic structures and many users. My advice: disable ACLs while refining your application and enable them gradually once everything works.

***

## Conclusion

With this, I’ve finished the series of articles started months ago. We now have a complete home MQTT broker on a Fujitsu Futro S450 thin client running Ubuntu Server 16.04 and Mosquitto, with TLS-encrypted connections, password-based user authentication, no anonymous users, and strict topic access control. At a laughable cost.

Any questions?

