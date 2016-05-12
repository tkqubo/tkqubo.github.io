---
title: Reading "Reactive Programming with Scala and Akka" - Chapter 1
lang: en
date: 2016-05-12 10:00:00
tags:
  - Scala
  - ReactiveProgramming
---

Last week I held a small reading club to read <a  href="http://www.amazon.com/gp/product/1783984341/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1783984341&linkCode=as2&tag=tkqubo-20&linkId=3DD3AV3FBDD6745N">Reactive Programming with Scala and Akka</a><img src="http://ir-na.amazon-adsystem.com/e/ir?t=tkqubo-20&l=as2&o=1&a=1783984341" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />, written by Prasanna Kumar Sathyanarayanan and Suraj Atreya.

<a  href="http://www.amazon.com/gp/product/1783984341/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1783984341&linkCode=as2&tag=tkqubo-20&linkId=JASVNACQ5LY5DXPL"><img border="0" src="http://ws-na.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=1783984341&Format=_SL110_&ID=AsinImage&MarketPlace=US&ServiceVersion=20070822&WS=1&tag=tkqubo-20" ></a><img src="http://ir-na.amazon-adsystem.com/e/ir?t=tkqubo-20&l=as2&o=1&a=1783984341" width="1" height="1" border="0" alt="" style="border:none !important; margin:0px !important;" />

The book contains a lot of exciting topics about Scala, Functional Programming, Reactive Programming, and Akka.  Thus highly recommended for anyone with Scala love.

This time we read chapter 1, "Introducing Reactive Programming".  And the below is what we've learned from there.

{% asset_img me.jpg %}

## Contents

This chapter largely contains these three parts, briefing the concept of reactive programming.

- What is Reactive Programming?
- Other stuffs around Reactive Programming
- Actual use case of Reactive Programming

## So what is Reactive Programming?

(Although this chapter is named *"Introducing Reactive Programming"*, it rather seems to be explaining about Reactive Systems, or, Reactive Architecute?)

Core ideas that support Reactive Programming are, according to the book:

- Responsive
- Resilient / fault tolerant
- Scalable
- Message-driven

The book illustrates these words one by one, among which I appreciated the explanation of the *resilience*, where the author express *a failure as a first-class event*.  This is more concrete and easy-to-understand rephrase of Akka's "let it crash" philosophy.  Yes, we have to handle failures gracefully, instead of trying to kick out or suppress them, as if they are non-existent.

For those 4 RP pillars above, these materials would be of great help

- [The Reactive Manifesto](http://www.reactivemanifesto.org/)
- [The Reactive Manifesto by Stefano Baghino](https://speakerdeck.com/stefanobaghino/the-reactive-manifesto) 

From them, we could additionally learn the difference between *scalable* and *elastic*.

*Scalability* itself, for example, can be achieved simply by engineer's manually re-writing a config file and adding one new server to the load balancer.  But to achieve the *elasticity*, those configuration must be done dynamically and automatically.  (see ["Elasticity" in Glossary](http://www.reactivemanifesto.org/glossary#Elasticity) of the Reactive Manifesto)

<style>.gutter { display: none; }</style>
``` bash So something like this?
           +-                    +--------------+
      Goal |              +----> |Responsiveness| <-----+
           +-             |      +--------------+       |
                          |             ^               |
           +-       +-----+----+        |       +-------+-------+
           |        |Elasticity| <------------> |  Resilience / |
           |        +----------+        |       |Fault tolerance|
           |              ^             |       +---------------+
Principles |   +--------> |             |               ^
           |   |          |             |               |
           |   |    +-----+-----+       |               |
           |   |    |Scalability|       |               |
           |   |    +-----------+       |               |
           +-  |          ^             |               |
               |          |             |               |
           +-  | +--------+-------------+---------------+---------+
    Method |   +-+     (Akka) Message−Driven architecture         |
           +-    +------------------------------------------------+
```


## Around Reactive Programming

Functional programming, asynchronous programming, data streams, microservices, and RESTful services...  Those terminologies are too much popular recently and I can hardly imagine a day without using those jargons in software engineers' conversation.  But how are they related to Reactive Programming?

In this multicore processor era, asynchronous programming paradigm is vital for creating reactive systems since single-threaded programming model cannot fully utilize all the cores.  Side-effect-free (pure) functional programming can make asynchronous programming easier.

And from the software architecture perspective, when adhering to the principle of the Reactive Programming, the system can eventually form a microservice-like shape because microservice architecture too emphasize a system's modularity, scalability, maintainability and resilience.  So in a sense, microservice architecture is sort of a realization of reactive architecture.  And RESTful interface can be very handy universal language for interaction between those modularized services.

## Use cases

The book introduces two reactive applications:

- IoT
- Tumblr's architecture

One of the participants made an interesting remark on IoT that IoT devices themselves can construct a dense population as an Internet-connecting entities like us human beings.  Think about that with the outrageous growth of the Internet users described in the beginning of this chapter, the amount of data flowing back and forth on the Internet will no doubt continue to increase.  To tackle with this problem, Reactive Programming must play the most important role in software engineering.

## A thought on reading technical book in foreign language in a reading circle

As a non-native English speaker, I always find difficulty reading through technical materials written in English to the end.  But when we have somebody to study with, we can help each other and learn from each other, including outside the scope of the book.

Thanks to this book, with catchy title and contents suitable for both beginners and intermediates of the Scala language, I felt that this very first time was successful.  I hope we can enjoy the following chapters, too.

## Others

### For those interested in the book:

[PACKT publishing](https://www.packtpub.com/) is offering [a subscription plan](https://www.packtpub.com/books/subscription/packtlib), where you can read any software books published by PACKT at a very reasonable price each month.  On top of that, yearly plan can include monthly tokens to **freely** download any one digital book!  I will strongly recommend applying for this plan.

https://www.packtpub.com/packtlib

### For those interested in software development with Scala:

Oneteam is now hiring server-side engineer.  Please visit [HERE](https://one-team.com/recruit/)

