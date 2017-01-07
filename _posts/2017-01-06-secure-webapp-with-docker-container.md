---
title: Secure web applications with docker container
date: 2017-01-06 22:10:00 +0000
categories: [technology]
tags: [docker]
author: Chun
---

Secure Web Applications with Docker Container
============================================

Many open source web applications doesn't provide out-of-the-box authentication and authorization features. For example, [Elasticsearch](https://www.elastic.co/products/elasticsearch) expose HTTP service on port 9200/9300 by default and allows anyone to access the service through these ports.

Traditional solutions to this problem include:
- Implement cusomized code within the application to support authentication and authorization.
- Update firewall configuration to block remote access to the public ports, and put a reverse proxy with authentication and authorization over the application.

These approaches both involve a fair amount of work and are intrusive/not flexible.

With Docker, we now have more elegant way to address security requirements of web applications in a none intrusive / resuable manner:

> Run web application with Docker container in private virtual network.
> Expose service through port mapping on a reverse proxy container.

![Docker Network](https://docs.docker.com/engine/userguide/networking/images/network_access.png)

Let's take a step by step look at this approach.

Prerequisites:
-------------

- A linux VM which has Docker engine installed and access to internet
- Able to download Docker images from DockerHub
- An Github account (for using Github OAuth feature)

Create named docker network
---------------------------

Docker use to provide `--link` option for connecting one container to another, allowing applications running inside the 2 containers to communicate with each other through network. With recent upgrade, this feature is now replaced by the **user-defined network**. Hence, let's first create a named network in which we will run all the collaborating containers in his example.

```
$ docker network create --driver bridge dockernetwork1
f72b24259f4a773f43a664e2f30a2283e6a97405ecc4e38aa72a0ec727cab2c4

$ docker network inspect dockernetwork1
[
    {
        "Name": "dockernetwork1",
        "Id": "f72b24259f4a773f43a664e2f30a2283e6a97405ecc4e38aa72a0ec727cab2c4",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```
Here we are using the `bridge` driver which allows port mapping to the docker host's physical network.

Run target application in Docker network
----------------------------------------

[portainer](http://portainer.io/) is a web based docker management tool. It exposes HTTP service on default port 9000. We are going to use it as an example and see how we can protect it from public access.


