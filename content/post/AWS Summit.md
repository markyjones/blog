+++
date = "2016-07-07T11:00:01+01:00"
draft = false
title = "AWS Summit 2016 London"

+++

Last week I attended the AWS Summit in London Excel. The day was a bit of a mixed bag with a fairly slow start in the keynote by Dr. Werner Vogels (Chief Technology Officer, Amazon.com) but some useful bits in the free Hands On session and the breakout sessions later in the afternoon.

**Keynote**

As alluded too, the Keynote was more of an overview of the AWS  product set punctuated with customer success storys. It was the success story's that I found the most interesting an example of which was a showcase application called [teletext.io](https://teletext.io/) 

Point one, teletext.io it is completely "severless", as they say

> Our API is powered by the AWS API Gateway, Lambda functions and DynamoDB only. We don't operate servers. Not even one.

Point two, (on a non AWS/Serverless related note) this is an exciting product. I have being playing around a lot with Static Site Generators (look at [staticgen.com](https://www.staticgen.com/)) and have even moved my own blog over to [hugo](https://gohugo.io/) hosted (for free) on GitHub Pages. Whilst there are a few CMS style tools for these SSG's like [forestry.io](https://forestry.io/), teletext.io offers localisation and some nice inline editing capabilities. Whilst [Typora](https://www.typora.io/) and Hugo are working well for me at the moment I may try have a play with the free usage tier of teletext.io.

Other notes I made included an un-credited (I think) guide on whether you are ready for Micro Services… in a slide entitled "Some Signs You Are Not at Microservice Level Yet" which was food for thought.

> * Different services do coordinated deployments.
> * A change in one service has unexpected consequences of requires a change in other services.
> * Services share a persistence store.
> * You cannot change your service's persistence tier without anyone caring.
> * Engineers need intimate knowledge of the designs and schemas of other teams' services.

I was reminded of a great quote - [Gall's Law](https://en.wikiquote.org/wiki/John_Gall) - The Power of Simplicity...

> "A complex system that works is invariably found to have evolved from a simple system that worked. A complex system designed from scratch never works and cannot be patched up to make it work. You have to start over, beginning with a working simple system."

The Driver and Vehicle Standards Agency [DVSA](https://www.gov.uk/government/organisations/driver-and-vehicle-standards-agency) did a talk too talking about some ways they solved their challenges of migrating a legacy PFI purchased MOT solution onto a in house developed application hosted on AWS. Given my wife has recently begun work at HMRC we have been reading a lot about the [Government Digital Service](https://gds.blog.gov.uk/category/gds/) and the progressive approach [Mike Bracken](http://mikebracken.com/) and teams have introduced. Key takeaways from the DVSA talk was about:

- Focus on an MVP first release.
- Embedding Agile Ways of working
- Multi Vendor, Blended Agile Teams
- Internal Service Ownership
- (Yeah and it was a "Cloud based scalable solution")

**AWS Kinesis**

My first breakout session was about [AWS Kinesis](http://docs.aws.amazon.com/streams/latest/dev/introduction.html) which featured a customer presentation from the CEO of [jampp](http://jampp.com/) who had moved from a Kafka driven architecture to a Kinesis driven one. It was a good session to get a feel for the capabilities of Kinesis. 

I made notes about the [Kinesis agent](http://docs.aws.amazon.com/firehose/latest/dev/writing-with-agents.html) as a potential option to ship logs from our servers and services and send to AWS ElasticSearch instances, the Kinesis analytics also seemed interesting being about to run realtime queries against the data streams to create wallboards etc.

The throughput jampp was putting through their stack was genuinely very large (many 000/s), the cost comparison to IaaS style Kafka was very favourable to AWS Kinesis and the presenters recommendations on getting the most from Kinesis were interesting. 

The presenter covered aspects like how best to distribute data across the Kinisis shards evenly, picking a protocol for the streams ([MessagePack](http://msgpack.org/index.html) was jammp's preference), increasing the retention period, and integrations with a whole host of technologies I am off to google(!) e.g. [PrestoDB](https://prestodb.io/), [Parquet](https://parquet.apache.org/), [Airflow](http://airbnb.io/projects/airflow/), [AirPal](https://github.com/airbnb/airpal), [Jupiter notebooks](http://jupyter.org/) etc.

**Hands on lab**

All attendees had an opportunity to complete a hands on lab having booked it in. The lab was provided by [qwikLAB](https://qwiklabs.com/) which have a very cool set up. You essentially get given an on demand functional AWS account for the purpose of completing a range of exercises. I chose to complete the "Serverless Architectures using Amazon S3 and Amazon SNS with AWS Lambda" course. 

Overall it was rather good, the material was interesting and got you to traverse a good range of the AWS console, CLI and EC2 features. It did highlight how limited some of the AWS Lambda debugging tools are and the relatively slow development feedback loop lambda has. Some of the steps in the lab were out of date as AWS have altered labels and layout in places but it was easy enough to figure out the way around. I would recommend some of the quikLAB courses for people if they are interested, if like me you often fear of accidental costs on your own AWS instances following tutorials these are a good way to set an absolute celling on costs.

**Final Breakout Session**

Covered ECS and containerisation. Some interesting talks about how to carry out Blue/Green deployments using ECS and some of the ways to implement service discovery in ECS. They also introduced [Empire](https://github.com/remind101/empire) which is an Open Source PaaS built on top of ECS with a Heroku compatible API which is very interesting and worthy of some further research.

**Summary**

Would I go again? Yes, but I may not rush in for the morning keynote next time and schedule the breakout sessions a little more wisely as all my selections ended up overlapping.