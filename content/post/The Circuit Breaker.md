+++
date = "2014-05-07T11:00:01+01:00"
draft = false
title = "Fault tolerance"

+++

One of my favourite talks from Qcon this year was Fault tolerance made easy by Uwe Friedrichsen. Uwe's talk about fault tolerance had a big impact on me (a similar presentation can be seen [here](https://youtu.be/lc_APy75e6o) and I implore you to watch it).

Uwe's presentation was largely based around the book Release It! which I have since picked up to read myself. I am only half way through and already I would state this is in my top 3 professional books.

[![Purchace Release It!](http://ws-eu.amazon-adsystem.com/widgets/q?_encoding=UTF8&ASIN=0978739213&Format=_SL160_&ID=AsinImage&MarketPlace=GB&ServiceVersion=20070822&WS=1&tag=marblo03-21)](http://www.amazon.co.uk/gp/product/0978739213/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=0978739213&linkCode=as2&tag=marblo03-21)

The key thing from both the talk and the book is that systems will fail and that they will perform very differently in production compared to how they performed in a functional or a load test environment. The emphasis is therefore on how build our systems to react to that failure in sensible ways. There are patterns and anti-patterns described in the books. The 2 taster examples below are simple ones to give you a quick flavour...

## The Circuit Breaker

Take a moderatly high throughput environment ~500,000 requests per minute (around ~8,000 requests per second) to the database...

Your database is down. How does your application react? Well it desperately and repeatedly attempts to bludgeon the database into giving it a connection, meanwhile almost every thread on the web server is blocked waiting on failed connection responses from the DB server, by which time you probably don't even have the resources left even to serve an error page.

What if the first bit of code to have received a SQL connection exception decided to "warn" the rest of the code base that the database was erroring? Assuming we get more than n number of exceptions within a period of time why not collectively agree to "break the circuit", then agree to retry again after a period of time?

This gives the system trying to recover a respite, it gives the application server instant feedback to inform the user (rather than waiting for time outs) and it stops the application server chewing up resources that could be spent serving other applications that could be working.

The database here is just an example this could be anything external to the calling code and I think this pattern should be widely implemented.  

```c#
const int numberOfFailedAttempts = 2;

var policy = Policy
  .Handle<SqlException>(ex => ex.Number == ????)
  .CircuitBreaker(numberOfFailedAttempts,
TimeSpan.FromMinutes(1));

policy.Execute(() => CallDatabase());
```

  

## Limited Retries

Handling transient failures. Take our code calling an external system like a partners API. We fail the first time but want to try again.

```c#
var policy = Policy
              .Handle<DivideByZeroException>()
              .WaitAndRetry(new[]
              {
                TimeSpan.FromSeconds(1),
                TimeSpan.FromSeconds(2),
                TimeSpan.FromSeconds(3)
              });

policy.Execute(() => DoSomething());
```

## Others

I also see value in other patterns like *Shed Load*, Netflix gets clever with this and has ideas of capacity and deliberately sends errors back to users when capacity is breached. The *Fall Back* is where a failure on a response would mean invoking another function like retrieving a value from a cache - the theory being stale data is better than no data when a service is down.

![img](https://camo.githubusercontent.com/ab21cec6ca97aae3479beed850b448d67d311f1e/68747470733a2f2f7261772e6769746875622e636f6d2f4170702d764e6578742f506f6c6c792f6d61737465722f506f6c6c792e706e67)

What tools are there for this? Well the examples I have used above are in a tool called [Polly](https://github.com/michael-wolfenden/Polly) and the daddy of libraries is [Hystrix](https://github.com/Netflix/Hystrix) a Netflix product whose stated goal is:

>
>Hystrix is a latency and fault tolerance library designed to isolate points of access to remote systems, services and 3rd party libraries, stop cascading failure and enable resilience in complex distributed systems where failure is inevitable.
>

There is an immature port to .NET of Hystrix [here](https://hystrixnet.codeplex.com/) and I would love for this to develop further but it doesn't look overly promising at the moment the project appears to be dead.

![img](https://camo.githubusercontent.com/e871b5d002a9699e7a2d9fa0178af5c72f0743e0/68747470733a2f2f6e6574666c69782e6769746875622e636f6d2f487973747269782f696d616765732f687973747269782d6c6f676f2d7461676c696e652d3835302e706e67)

Polly covers a lot of the simpler patterns, it is light weight and is a great tool.

I would love for everyone to consider the book "Release It" as one for their reading list; however, failing that take a minute to study the [slides](http://qconlondon.com/dl/qcon-london-2014/slides/UweFriedrichsen_FaultToleranceMadeEasyAHeadStartToResilientSoftwareDesign.pdf) and check out the documentation for [Hystrix](https://github.com/Netflix/Hystrix/wiki) this stuff really does matter any highly available system.

If nothing else, next time you write a feature ask yourself "what happens when this dependency fails"? because in complex systems failure is inevitable and we need to avoid cascading failures and wasting server resources when it happens.  
