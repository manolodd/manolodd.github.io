---
layout: post
title: Installing a Home MQTT Broker (I)
subtitle: Repurposing a Thin Client for MQTT
cover-img: /assets/img/posts/BrokerMQTTDomestico-Destacada.jpg
thumbnail-img: /assets/img/posts/mqtt-broker-thumb.jpg
share-img: /assets/img/path.jpg
tags: [testbed]
author: Manuel Domínguez-Dorado
---

At home I have an MQTT Broker configured on a Dell T310 Tower Server that I generally use when I’m developing applications “seriously.” It supports TLS, authentication and ACL in MySQL, clustering, has a tuned kernel… But I don’t keep it permanently connected to avoid excessive power consumption. However, I’m going to need a broker that’s permanently connected because I have a project underway and I want to use it as an Enterprise Service Bus (ESB). It won’t need to handle much load, so I can use a lighter device. The idea is that the MQTT broker:

*   Doesn’t consume much energy
*   Is silent
*   Is sufficient
*   Provides service over TLS
*   Supports user authentication and ACL

Since I mainly have Linux machines at home, I’d like the broker to run on Linux too, although, as always, I’ll use whatever is necessary. I’m not a purist.

### The Lightweight Device

I’ve always liked to use technology to its full lifespan. We already have enough with planned obsolescence to shorten the life of all the gadgets we accumulate. A few years ago I bought a Fujitsu Futro S450 thin client. After some time using it, I repurposed it as a multimedia center connected to the TV. And now that I no longer need it for that role, I think it will be an ideal device to act as an MQTT broker. When I repurposed it as a multimedia center, I erased the operating system it had (eLux) and installed Lubuntu, which worked perfectly. So I don’t think I’ll have any issues running Ubuntu Server.

![PMI Branch logo](/assets/img/posts/BrokerMQTTDomestico-1-FutroS450-300x236.jpg){: .mx-auto.d-block :}

Its main features are:

*   AMD Sempron TF20 800 MHz processor
*   1 GB DDR2 SODIMM
*   512 MB Compact Flash
*   AMD Radeon X1250 (up to 1920×1200)
*   Gigabit Ethernet
*   Passive cooling
*   18 W consumption at full operation

It’s a pretty decent device for many things and can be found today on eBay for €10, so there’s no excuse.

### Upgrading the Fujitsu Futro S450

Except for the Compact Flash capacity (512 MB), I think the factory specs would suffice; but I’ll upgrade the RAM anyway since it’s reasonably priced. So the first step is to open it and replace the Compact Flash with one of greater capacity and transfer rate.

![PMI Branch logo](/assets/img/posts/BrokerMQTTDomestico-1-PlacaBaseFutroS450-300x187.jpg){: .mx-auto.d-block :}

I bought a 4GB one from Kingston (€11, more than the second-hand Futro on eBay, haha). And the upgrade is very simple: remove a couple of screws, open the case, take out the 512 MB Compact Flash from its socket and insert the new 4 GB Compact Flash. Upgrade complete.

![PMI Branch logo](/assets/img/posts/BrokerMQTTDomestico-1-CP-4GB-300x225.jpg){: .mx-auto.d-block :}

The same goes for the memory. The module is inserted in its corresponding socket and you just have to remove it and insert the new module. In this case, I installed a 2GB module to double the Futro’s memory. I bought it for €14 on Amazon.

![PMI Branch logo](/assets/img/posts/sodimm-300x138.jpg){: .mx-auto.d-block :}

While the case was open, I decided to remove the processor’s heatsink to take a look. The part number engraved on it (AMGTF20HAX4DN) indicates it’s an AMD Athlon 64 TF20, not a Sempron. But I won’t dwell on it. I closed everything back up and… voilà, the device is ready. During boot, the Futro detected the Compact Flash as a new 4GB unit and the 2GB memory. So I’ve already updated all the hardware I wanted and needed.

On the other hand, at home I have a 16GB Sandisk Cruzer USB memory that I might add to have some swap memory or to mount an extra partition. It’s not really necessary, but since I have it in a drawer, I might put it to use. I’ll decide at the time of installation 😊

![PMI Branch logo](/assets/img/posts/sandisk-cruzer-fit-16gb-usb-300x300.jpg){: .mx-auto.d-block :}

If there are no problems—and I hope there aren’t because I have little time—the steps should be:

*   Install Ubuntu Server 16.04 and configure SSHD
*   Install the MQTT broker and necessary libraries
*   Create and configure the certificates correctly
*   Start the MQTT broker

I’ll keep posting updates in the coming days as I find time to work on it, because lately I’m barely keeping up 😊

