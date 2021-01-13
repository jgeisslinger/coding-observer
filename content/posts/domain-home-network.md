---
title: "Domain Name for your Home Network"
date: 2021-01-12T17:09:57+01:00
draft: true
comment: true
categories: "Network"
tags: [DNS, homenet, network, domain, static ip]
Javascript: false
NodeJS: false
PowerApp: false
Network: true
Misc: false
discussionId: domain-home-network
---

In the world of smart home and IOT (Internet of Things) it is more and more required to access your local devices from remote. If you would like to check your security camera while on vacation, access your local file storage or any other service that is hosted at home. But non-business accounts normally facing the issue that they don't get fixed IP addresses from their provider. The internet provider regulary updates the endpoint IP address so that remote access is no longer working.

## Dynamic DNS 

The answer to overwhelm this issue calls **Dynamic DNS**. With a DynDNS service we use a fixed domain name where the endpoint IP address gets dynamically refreshed once the internet provider assigned a new one. With this extra step in the infrastructure we don't use an IP address anymore to access our devices but on the other hand gain the full advantage of a fixed IP which is normally only possible with way more expensive business accounts.

## Setup Domain Name

I tested several providers out there. Many of them are not user friendly, others way too expensive. In the end I selected (in my personal view) one of the best providers available: deSEC.[^1] deSEC is a German non-profit organization based in Berlin with the full focus on privacy and open source. They provide a lot of options and features that make them one of the best-in-class services. And it's free to use!

Go on the deSEC homepage and select "Create Account" to start.
 


## IP Update with Ubuntu





[^1]: You can check out the provider here: https://desec.io 