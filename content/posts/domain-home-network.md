---
title: "Domain Name for your Home Network"
date: 2021-01-12T17:09:57+01:00
draft: false
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


1. [Dynamic DNS](#dynamic-dns)
2. [Setup Domain Name](#setup-domain-name)
3. [IP Update with Ubuntu](#ip-update-with-ubuntu)

## Dynamic DNS 

The answer to overwhelm this issue calls **Dynamic DNS**. With a DynDNS service we use a fixed domain name where the endpoint IP address gets dynamically refreshed once the internet provider assigned a new one. With this extra step in the infrastructure we don't use an IP address anymore to access our devices but on the other hand gain the full advantage of a fixed IP which is normally only possible with way more expensive business accounts.

## Setup Domain Name

I tested several providers out there. Many of them are not user friendly, others way too expensive. In the end I selected (in my personal view) one of the best providers available: deSEC.[^1] deSEC is a German non-profit organization based in Berlin with the full focus on privacy and open source. They provide a lot of options and features that make them one of the best-in-class services. And it's free to use!

Go on the deSEC homepage and select "Create Account" to start. You will get to the landing page to enter your data and domain settings you would like to create.

![Sample Data](https://s3.eu-central-1.wasabisys.com/coding.observer/posts/domain-home-network/post-1.png)
 
Enter your email address and your subdomain-Name that you would like to connect. At this stage you must use a subdomain of dedyn.io but there is a possibility that you can use custom domains if necessary. For private purposes it should be usable to go with the subdomain option.

After you verified the captcha your registration is confirmed with the welcome post.

![Confirmation](https://s3.eu-central-1.wasabisys.com/coding.observer/posts/domain-home-network/post-2.png)

After a couple of seconds you should receive a welcome mail where you get the confirmation link to activate your DynDNS. You should not click on the link at it seems the hyperlink is not working correctly. Instead take care to copy paste the complete link including the complete verification string. (Note: I really dont know why they choose such a long verification string. I will show in one later posts how to setup a verification route with NodeJS which is way more efficient).

![Mail](https://s3.eu-central-1.wasabisys.com/coding.observer/posts/domain-home-network/post-3.png)

After you clicked the confirmation link you will get the status page of your subdomain. Here you can find your individual authorization token. Save this token on a secure place you will need it later. In addition this page will show you the status of your subdomain. Because we have not setup any IP link between the subdomain an our home network it will just show a notification.

![IPstatus](https://s3.eu-central-1.wasabisys.com/coding.observer/posts/domain-home-network/post-4.png)

You can also bookmark the page for later, so you can always check the status of your subdomain and verify if IP refresh works.

## IP Update with Ubuntu

Now we need to setup an IP refresh so that our subdomain will always listen to the current IP address our internet provider assigned to our home account.

There are several ways to do this refresh. One option would be to configure your router to perform a regular refresh by calling a specific URL with password and username. I will not go into detail of this solution as it is very specific per router and you propably find guidelines on the vedor's page.

The more professional way is to update the IP address with a home server. For good reason deSEC provides an API access to the refresh service. For that you can use any server that allows SSH connection so you can setup the refresh process. In this example I use a standard Ubuntu server that normally acts as a development server in my environment. But the procedure is the same for e.g. raspberrys. 

Login to your server using SSH and update your repo.

```bash
sudo apt-get update 
```

Now we are installing a refresh service that performs an IP update on a regular basis to deSEC called ddclient. We will install it together with curl that we use for API requests.

```bash
apt install curl ddclient -y
```
After the package has been downloaded you need to go through the install procedure. Here you first select the dynamic DNS provider. In case of deSEC you must select "other".

![Step1](https://s3.eu-central-1.wasabisys.com/coding.observer/posts/domain-home-network/post-5.png)

As update server you enter the regular update server of deSEC. If you want to update via your IPv6 address you can also use the specific Ipv6 server. 

```bash
update.dedyn.io

update6.dedyn.io
```
As update protocol you select 

```bash
dyndns2
```
For the username you enter the username provided on your status page. This is basically the subdomain you selected. For me it would be:

```bash
codingobserver.dedyn.io
```
Now you enter the same way the password that was provided to you. Normally this contains a long string. Re-enter to confirm the password.

In the last step you specify the interface where the dynamic DNS service should be used. This is basically the interface the server is connected to your router. In most scenarios this would be eth0 if cable connected or wlan0 if connected via wireless lan. 

If you dont know your interface you can check it via 

```bash
ip link show
```
Confirm your entries by typing in your subdomain that you want to link.

Now we extend our entries a bit because most of the servers cannot get the public IP directly on their interfaces as they are behind routers, firewalls or other infrastructure. So we open the config file in a text editor like nano

```bash
 nano /etc/ddclient.conf
```
Now we edit the file and replace the **use** line with the following lines to get our IP from calling the URL and use always SSL for all communication.

```bash
use=cmd, cmd='curl https://checkipv4.dedyn.io/'
ssl=yes
```
The complete config should now look like this
```bash
protocol=dyndns2
use=cmd, cmd='curl https://checkipv4.dedyn.io/'
ssl=yes
server=update.dedyn.io
login=codingobserver.dedyn.io
password='YOUR KEY'
codingobserver.dedyn.io
```


If you use nano you can now save with ctrl+o and close with ctrl+x

Try the service with the following command

```bash
ddclient -force
```
There should be a success message.

```bash
SUCCESS:  updating codingobserver.dedyn.io: good: IP address set to XXXXXXXXX
```

Now move back to the deSEC status page and check your if your IP address shows up. Now you can start using your services from remote.

![Confirm](https://s3.eu-central-1.wasabisys.com/coding.observer/posts/domain-home-network/post-6.png)

Please remember that your home infrastructure is now open to the world. You have to secure it with proper firewalls to avoid any exploit. 

[^1]: You can check out the provider here: https://desec.io 