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

> Run web application with Docker container in private virtual network 
> Expose service port through reverse proxy with authentication enabled

Let's look at how this is achieved step by step.

