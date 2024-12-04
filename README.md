# Sponsored Agent System

This diagram, while it may seem excessive for the requirements, contains things you likely already have -- logging, monitoring, alerting, dashboards. I put it all in because monitoring your system is essential and needs to be a part of the initial plan. Note that this diagram is not a full diagram with availability zones and vpcs. Because most of the managed AWS services are already redundant across availability zones, it would just make things unreadable.

# General Philosophy
There were a number of factors in my thought process:
 1. Use AWS services where possible; why manage the control plane yourself when you can have experts do it.
 2. Use AWS services even when they wouldn't be the best choice simply because I have icons for them and I'm supposed to do this in around 2 hours. 

# Key Architectural Decisions
## Kubernetes
This isn't the right solution for everyone. It is complex and has a fractured ecosystem. That said, I will save my last company $1M over the next five years. It's also a great skill for a DevOps engineer to have. The other very popular (but more expensive) route is Serverless with something like AWS Lambda (FaaS).

## Network Load Balancer
Normally one would use an Application Load Balancer which routes at layer 7 and let AWS handle the http routing. In this case I would recommend an nginx Kubernetes Ingress instead for layer 7 routing. It has much more configurability and logging.

## DocumentDB
These pages are perfect for a document database (NoSQL). This one speaks the MongoDB language but has better performance. The schedule will be part of the JSON document for the Community Page.

## No Redis Cache
DocumentDB is so fast that a cache is unnecessary.

# The User Path

 1. The user connects to the internet and uses DNS to find the IP address of the nearest edge cache.
 2. The cache returns the document if it has it, otherwise it contacts the Network Load Balancer
 3. The NLB looks up the IP address of the service
 4. The NLB connects to the appropriate Kubernetes Node based on IP
 5. The Kubernetes Nodes are always in contact with the Kubernetes Control Plane
 6. The Kubernetes Nodes connect to DocumentDB

# The Other Stuff

 - **CloudWatch** Metrics collection built into AWS. It is always on and monitors everything. It is also not very user friendly. It's bad enough that I often prefer an external vendor like Datadog. But I didn't have a logo for Datadog.
 - **CloudWatch Alarms** You need something to let you know when the metrics get bad. Again, I'd prefer an outside vendor.
 - **AWS Client VPN** This one depends on the amount of usage. For very little usage, it's incredibly cheap. If it's going to get significant usage, you'd be better off with another vendor. This is great as a backup VPN.
 - **OpenSearch** With AWS managing the control plane, Elastic Search has finally become stable. That said, there are a lot of logging solutions out there each with pro's and con's.
 - **OpenSearch Dashboards** It is often times necessary to generate metrics from logs and display that graphically. 
