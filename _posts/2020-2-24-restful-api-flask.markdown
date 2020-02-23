---
title: "Building a RESTful API with Python and Flask"
layout: post
date: 2020-3-24 12:00 PM
tag:
- api
- python
- flask
- web
- system design
hidden: false # don't count this post in blog pagination
description: "Walkthrough guide to building a simple RESTful API with Python and Flask"
category: blog
author: randykinne
externalLink: false
---

*Table of Contents*

**What is an API?**
API stands for *Application Programming Interface* and it refers to the way two software applications communicate with each other. 

Companies that build SaaS (Software as a Service) products often build internal and external APIs. While internal APIs are often used for different parts of a single application to communicate, external APIs allow third-party developers to use parts of the service in their own applications without exposing internal code and data to the world. 

**What is a RESTful API?**
REST stands for *Representational State Transfer*. It's defined as an architectural style that standardizes the way computers communicate with each other. For an API to be considered RESTful, it must meet the following criteria:
- **Client-Server Model**: There should be a client that uses the API and a server that offers a service. By separating them, the system improves portability across multiple platforms and reliability by simplifying server components. 
- **Stateless**: Each request by the client must contain all information required for the API to serve the request. In other words, the server must not store any data required to serve subsequent requests to the client.
- **Cacheable**: [Caching](https://en.wikipedia.org/wiki/Cache_%28computing%29) resources and requests is a good way to boost the performance of an application. Implementing caching is up to the developers, but the server should always indicate whether a served request is cacheable or not.
- **Layered System**: Multiple servers can serve the API without the client interacting with the system differently. Designing an API to have multiple layers reduces the [coupling](https://en.wikipedia.org/wiki/Coupling_%28computer_programming%29) supporting the end goals of a system being highly reliable, highly scalable, and highly maintainable.
- **Uniform Interface**: Using a uniform interface, system architecture is simplified and 
