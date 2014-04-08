---
layout: post
title:  "Our Platform"
date:   2014-04-07 15:40:42
categories: architecture
author: "Nicolas Grasset"
---

One of the most helpful types of blog posts I read every now and then from larger startups is a description of how they are built, and how they are working. Here at Lifesum, we spent some time making sure our technology stack would be easy to work with while being scalable for today and the next couple of years.

In 2013, we rebuilt all of our applications on the backend, Android and iPhone. This post will describe what happened on the backend. We moved to AWS on the way, because it is just our favorite way to scale on the fly without knowing about all the technologies we would ultimately end up with.

## Ubuntu + Python + Django

First off, we had to upgrade our backend. Built on ASP/C# .NET, the code was not taking advantage of newer frameworks, and we needed to be able to hire a team to contribute in parallel to the product. We decided to move away from .NET for 3 reasons.

### Ubuntu

First off, Windows servers just isn't supported the same way by the open source community, so we wanted to move to Linux with Ubuntu. Whether it is caching libraries, database connectors, MQ solutions or OAuth libaries, .NET is simply not as much on the forefront of development as Python, Ruby or PHP.

We switched to Ubuntu LTS versions, trying to keep the same setup for all instances, making it easier to administrate production servers. Not all services need the same type of instances, but they all run Ubuntu very well.


### Python

We needed, and still need, to iterate fast and with quality code. For this reason, I excluded a switch to Java or Scala which would be great languages but too early for our small team given that our product was about to change many times before getting a clear shape. We needed quick scaffolding and prototyping possibilities.

I had several years of PHP experience, in production at Yahoo! and in early stage startups, but recently adopted Python which is a very clean and easily maintainable languages: great deployment tools, perfect unit test support, high quality frameworks and a library for everything. 

The main reassuring factor for this choice was our location: Stockholm is a Python city. There are many developers in many languages, but just like Spotify, Tictail, Fyndiq, FundedByMe and others, Python is probably powering more startups here than any other languages, so we should be able to more easily find talents with production experience than with Ruby or PHP.


### Django

Finally, while framework often add a lot of unnecessary noise to your code, I decided I (being alone on the backend then) was too novice in Python to re-invent the wheel, and Django was already powering Instagram and my friends at FundedByMe as well as many other great services.

Django comes with a good DB support with an efficient ORM (which can also be replaced by arguably better ones), a good framework structure close to MVC and a killer-feature: the Django Admin. With built-in support for cache backends, DB, web templates and user authentication, I thought we could always extend it to simplify later with more experience, but it'd be a great tool to start with. It was!



## One application, two tracks

Most of our usage is through (private HTTP) APIs that were re-designed in the process, but we also have a website. Both were built in Django to share components and simplify deployment.

### HTTP requests

Communication is handled most often through HTTP requests, on the web or on mobile APIs. Some of our APIs are RESTful and built with Tastypie but most others are custom built on top of Django views, returning JSON. 

### Celery + RabbitMQ

Our response time is on average below 50ms; for everything that takes a little longer to run, we use asynchronous processes with celery workers fed by a RabbitMQ queue of tasks. 40% of our code runs in these tasks, so celery workers are deployed alongside our web workers (nginx+gunicorn) and this way scale the machine very simply.

### One application

All tasks, API, website and management commands run in the same app. This way we can scale everything together and any code written can take advantage of all the same tools (email, push, caching, search, ...) and models (users, payments, food, diary, ...).



## Scaling with Amazon

Amazon Web Services are the engineer we did not hire in our team! With EC2 and auto-scaling, our application adapts to the amount of users at any given time of the day, and self-repair in case of hardware failure. Here is a breakdown of the services we use:

* EC2 - Production instances auto-scaling every hour. They can go live within 30sec, and will wait 20min to shut down
* Elastic Load Balancer - This simple service is facing the world and acting as a simple load balancer
* Redis on Elasticache - Caching makes most applications faster. We cache at the application level on a Redis cluster for better fail-over
* MySQL on RDS - Less common than PostGres, we use our legacy MySQL because it works; RDS manages backup, upgrades and fail-over.
* Route 53 - All of our DNS rules are handled by AWS, some of them a little more dynamically than would otherwise would be possible
* Cloudfront + S3 - Storing and serving the few photos we store is done easily through S3 behind Cloudfront

### One-command deployement

We are currently only deployed in one region (Ireland) since our users are mostly in Europe. Deployment is a one-click operation and monitoring is done via NewRelic and Sentry, but this enough for another blog post.


![Lifesum platform]({{ site.url }}/assets/infrastructure-2014.jpg)


We are looking for new talents to help us make this stack better, so checkout our openings: [http://jobs.lifesum.com/jobs](http://jobs.lifesum.com/jobs)