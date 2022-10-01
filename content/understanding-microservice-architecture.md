---
title: "Understanding microservice architecture"
date: 2020-06-07T21:20:54+01:00
categories: ["Software Engineering"]
tags: ["microservices", "devops", "backend", "software architecture"]
images: ["/microservices-architecture.png"]
draft: false
---

{{< figure src="/microservices-architecture.png" alt="microservices-architecture" >}}

Microservices is a buzz word in the world of Software Engineering architecture. It has completely changed the way we build our applications nowadays. But what the heck are microservices? Microservice is simply an architectural approach based on building an application as a collection of small services. Using microservices means building applications from loosely coupling services.

Still confused? Let me give you an instance. We can decide to build an e-commerce application that adopts the microservice architecture. We could build different parts of our application like payment system, review system, authentication system and guest workflow, all as a separate independent service. These systems works independently of each other and can be managed separately.

Let me give you another instance. Suppose we want to use NodeJS to build a web application which provides a collection of online tools like image processing tool, video processing tool, real-time communication tool and data processing tool. All these tools make up our complete application. Now we want to adopt microservice architecture in building this application. What do we do? We can select the best language and technology for each tool. NodeJS is not best suited for CPU-intensive operations like image/video processing due to being single threaded by default. 

For this reason we may choose to use Golang to build our image/video processing tool because it is a pretty fast compiled language. We can now decide to use NodeJS for building our real-time communication and data processing tool. Awesome right? All these tools are independent of each other and are built separately using different languages. After building these tools we need to integrate it together to form a single application by making sure they communicate properly through a well defined interface.

Before now Software Engineers make use of monolithic approach in building applications. Monolithic approach means our applications are tightly coupled. They are just a single unit and every part of the application is in one code base. Monolithic architecture is better if your application is simple, and is written in same language.

Microservice approach gives developers flexibility, simplicity and scalability in building complex applications. That's why a lot of company like Google, Uber, Amazon, Spotify, Netflix, SoundCloud e.t.c. has adopted this approach in building their applications. 

### Advantages of microservices

There are many advantages of using microservices in building your applications.

#### Flexibility
Using microservices gives a developer the freedom to choose any technology he wants to use in building any part of his application. Teams can choose the best language for the job at hand. One can decide to choose Go for a service and NodeJS for another.

#### Productivity
In this pattern, a developer or team can focus on their own service and avoid getting involves in other services. Bugs are also easier to fix because if a service isn't working well, it's easier to isolate, disassemble and reconfigure them until they do.

#### Scalability
Microservices allows us operate, manage, build and scale our services independently. We can scale each service separately based on the needed resources.

#### Reusability
In microservice architecture, independent parts of our applications can be reused to build another system or an addition to another feature. This saves developers a lot of time as you don't need to build from scatch.

#### Agility
Microservice helps teams work in an agile environment. This is because a large team can be split into smaller teams to work on separate parts of an application(services).

### Disadvantages of microservices
It's also worth noting that microservices architecture is not a holy grail. There are also downsides in using microservices.

#### Rigorous testing
Testing a monolithic system can be simple as everything is in one code base, whereas using microservices means you have to test each part living on other systems or environments separately before testing the entire application. This consumes a lot of time and makes it harder to configure and perform integration testing.

#### Integration/Orchestration
This can be a problem in microservice architecture. Orchestration is the automated configuration, coordination, and management of computer systems and software which ultimately results in a single process of workflow. Integrating our microservices can be quite complex and tedious. A lot of efforts needs to be put in place in making sure each service are as loosely coupled as possible.

#### Flexibility
Yes i listed flexibility as an advantage of microservice architecture, but it can also be a double-edged sword. Building your services with different languages and technologies could make your solution unwieldy and potentially difficult to maintain.

#### Communication
Microservices needs to communicate with each other as efficiently as possible. Communication between each microservices should be carefully built, as one service may depend on a specific set of input to give a desired output.
There could be failures if communications between microservice are not thoroughly built.


### Tools related to microservices

#### Docker
Microservices are usually deployed in containers. Containers are virtual operating systems that act as a packaging for microservices. There are many containers out there but the most popular is [Docker](https://www.docker.com). Docker is a set of platform as a service products that uses OS-level virtualization to deliver software in packages called containers. Containers are isolated from one another and bundle their own software, libraries and configuration files; they can communicate with each other through well-defined channels. We'll discuss about docker in a separate article. Docker allows microservices to be split into even smaller pieces of code and created as Docker images through files called Dockerfiles. These Dockerfiles make linking microservices into a large application much easier.

#### Kubernetes
[Kubernetes](https://kubernetes.io/) is an open-source container-orchestration system for automating application deployment, scaling, and management. It was originally designed by Google, and is now maintained by the Cloud Native Computing Foundation. They are used to host cloud-native applications that are set to scale very quickly. By merging together a group of running containers, Kubernetes creates clusters that can be easily managed. These cluster can be managed in clouds. We'll discuss about Kubernetes in a separate article.


### Conclusion

Microservices are a loosely coupled independent system which communicates with other microservices through a well-defined interfaces using protocols like http. The functionality of these services remain available even when the machine that hosts this service goes down.

It's also worth noting that, with all the downsides of microservice architecture, the presence of a good developer and tools can solve these issues easily.

Using microservices architecture requires rigorous testing and, increase in memory and computing resources.
