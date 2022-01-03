---
title: "TIL: About Linux IP Command"
layout: post
date: 2020-12-7 11:00 PM
tag:
- blog
hidden: false # don't count this post in blog pagination
description: "Today I learned about Linux IP command"
category: blog
author: randykinne
externalLink: false
---

 **ip on Linux**

TIL about the Linux `ip` command and how to use it to configure network interfaces.
 
 **Why?**

For context, we have a service that communicates with an external service over HTTP. After restarting the service, we noticed that the server failed to communicate with the external service, returning `Connection refused`. The external service was functioning correctly and other services had no issues connecting.

```
$ curl external-service.com:5984
curl: (7) Failed connect to external-service.com:5984; Connection refused
```

To find the current machine's IP, we could use the `ip` tool.

```
ip [OPTION] OBJECT {COMMAND | help}
```

In our case, we used `ip a` (or `ip addr`) to display all network devices.

![img](https://www.cyberciti.biz/media/new/faq/2012/01/ip-command-show-address-ethernet-interface.png)

In the image above, various network interfaces are displayed as well as their associated addresses. 
