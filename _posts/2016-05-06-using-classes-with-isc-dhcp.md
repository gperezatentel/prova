---
layout: post
title: Using dhcp classes
tags: [ dhcp ]
---

Today I faced a new problem. There at $work we have a few VLANs with private addresses, in one of those the users plug their Cisco routers and then they do some testing using those Cisco devices as gateways of some other devices (that is, we end up doing double NAT, which works fine btw). 

However, I've been asked to stop providing IP addresses to just those devices. 

My setup is as follows:

    1. There are some devices with fixed IP addresses. I map those addresses using their MAC address. 
    2. There is a pool of addresses used to configure unknown devices. I apply special policies to those devices.

To keep that setup I can't simply remove the pool of addresses, it would do the trick with the Cisco routers but I will loose the ability to autoconfigure unknown devices. Instead what I would need would be to somehow deny those Cisco devices, and keep the other policies.

To do so, I could simply list those that bunch of Cisco devices and deny them all. But that's a real **PITA** and it is not even cool. Instead, what I will use will be the vendor bytes of the MAC address to group them into one dhcp class and deny them with one dhcpd.conf directive. Because all the routers are Cisco they will probably share the first bytes of the MAC address.

Unfortunately I have two sets of Cisco routers, each set has a different MAC prefix, such as:

{% raw %}
        00:26:0b:*  00:15:62:* 
{% endraw %}

First I need to group them into one dhcp class, this class will then be denied by the DHCP server. Well, in fact they will be simply ignored by the DHCP server but the directive that should be used is called **deny**.o

I assumed that I will only dealing with ethernet clients. The DHCP server will prepend the **01** byte to every DHCP request it receives. If we assume we will only be dealing with ethernet client we can simply skip that byte and work with the client's dhcp address.

To group those Cisco address, we need to compare the first bytes of the MAC address against, say, 00:26:0b. If the address matches, then it will be placed into a class, its name can be whatever you want. To do that, we can use the following dhcpd.conf directive:

{% raw %}
        class "ignorats" {
                match if
                        (substring(hardware,1,3) = 00:26:0b);
        }
{% endraw %}

The class gets named **ignorats** (means ignored in catalan). Let's see what this does. It takes, skiping one byte there bytes from the MAC address (remember the 01 byte we could safely skip if were to assume there would only be ethernet clients?) and compares that against **00:26:0b**. If that matches the host is placed into an special dhcp class called **ignorats**.

But, as we previously said, we have more than one MAC prefix to check (remember I had two different sets of routers?). We need to, somehow, compare the hardware address against two or more patterns and if the match place that host into the ignored class. 

Fortunately we can use logic connectors in the **match** directive. We can now transform our class definition into something that would work better:

{% raw %}
        class "ignorats" {
                match if
                        (substring(hardware,1,3) = 00:26:0b) or
                        (substring(hardware,1,3) = 00:15:62);
        }
{% endraw %}

Finally we need to, at last, deny (ignore) those clients. Because every VLAN has a pool of dynamic assigned addresses, I would need to deny those Cisco clients in every VLAN (pool) they can be connected to:

{% raw %}
        pool{
                range 10.0.10.230 10.0.10.250;
                option routers 10.0.10.1;
                deny members of "ignorats";
        }
{% endraw %}

That would do the trick. 

PS: while checking the documentation and searching around I found some people convert the hardware address to a text string to do match. This is quite error prone because the text conversion will convert the MAC address into its shortest version, removing the leading 0 for every byte. Our **00:26:0b** will be converted into **0:26:b**. 

Why would I need to add a text conversion and then use the shortest MAC version in the match directive when I can use a simplier command and use the full MAC address, which is the MAC that appear in every device after all? Up to you, if you want to do that take a look at the **binary-to-ascii** dhcpd.conf operator. But I would certainly discourage its usage (at least if you simply want to check the MAC address of every client).
