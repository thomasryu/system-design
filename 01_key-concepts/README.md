## 1. Web Server

Web servers receive traffic from both mobile and web applications. They are responsible for handling tasks such as returning web pages, performing data requests, and executing business logic.

## 2. Domain Name System (DNS)

Domain Name System (DNS) are usually third-party services in which our application's URL is converted to our web server's IP. It is basically a mapping server that converts user-readable URLs into their respective IPs.

In order to access a server the request flow goes like this:

1. The user-typed URL goes through the DNS, which returns the IP
2. The IP is then used to access the web server directly
3. The web server returns the requested HTML page or JSON response

## 3. Databases

Usually, relational databases are a easy go-to solution when we need to add a data tier to our application. However, a few use cases where a non-relational database might be recommended are:

- Database request requires really, really low response times
- The data is unstructured (i.e., there are no relations between each type of data)
- You need to store massive amounts of data

## 4. Vertical Scaling vs. Horizontal Scaling

### Vertical Scaling

Vertical scaling means improving our server machines' hardware (CPU, RAM, etc.). It is recommended when traffic is not the problem, but operational performance is. It comes with a technological limitation, since hardware has a upper performance limit of how far current technology. It is also usually very costly.

### Horizontal Scaling

Horizontal scaling means increasing our server machines' number. It is usually the recommended solution for large applications, not only because of cost, but because it prevents system failure, because there are more machines available if one goes offline.

## 5. Load Balancer

A load balancer is a service that evenly distributes incoming traffic among multiple web servers. It is used as a intermediary between the user tier and web tier: Instead of delivering a web server's IP directly, the DNS returns the user the load balancer's, which then chooses a web server and communicates with it through a private IP (meaning it can only be accessed from machines on the same network).

In other words, the advantages of a load balancer are the following:

1. **Security:** The web servers become unreacheable by the common users.
2. **Failover:** Traffic is redirected to a healthy server if one goes down.
3. **Scalability:** More servers can be allocated if traffic grows rapidly.

## 6. Database Replication

With load balancers, we've added scalability and redundancy/failover support to our web tier. Moving to our data tier, we can do the same thing by applying the replication technique. Database replication means to have multiple copies of the same database, usually by having an original, master copy and multiple slaves copies. Since read operations are usually more numerous than write ones, generally speaking:

- The master receives all write requests (which include inserts, deletes, and updates) and always maintain the most up-to-date version of the database.
- The slaves replicates the data in the master copy and are responsible for all read requests.

The advantages of the master-slave model are:

- **Performance:** Write and (multiple) read requests can happen in parallel, allowing our system to handle a higher volume of requests.
- **Reliability/Availability:** By having multiple database servers, we are not only safe against data loss, but also against our database not being available to our users.

If a slave database goes offline, read operations only need to be redirected to a healthy one. However, if the master database goes offline, a slave is assigned as the new master and all write requests are directed to it. Since this new master's data is not guaranteed to be up-to-date with the previous one, data recovery methods will have to be performed to ensure reliability (there are models such as multi-masters and circular replication that circumvent this problem).

## 7. Cache

Cache is temporary storage layer which stores the result of recent data requests. It's most common logic, read-through cache, works as follows:

1. The web server request a certain data
2. If the data exists in the cache it is returned quickly
3. If it doesn't, the cache requests the data from the database, stores it for a limited amount of time for future requests, and returns it.

It is significantly faster to obtain data from cache, because it skips all the overhead of databases having to perform fetching operations that might go through their disk data (cache is all stored in memory, databases mix memory and disk due to cost, disk is slower than memory). Not only that, the existence of a cache tier lightens the traffic load on the data tier and allow both to scale independently.

Some things to consider when adding a cache tier are:

- **How is data being read?:** Cache is a good design decision if data is read frequently AND modified infrequently. Modifying data too frequently might cause inconsistencies between database and cache, specially while keeping them across multiple regions (which makes synchronization an ordeal).
- **How long data stays in cache?:** Storing data for too long in cache might easily reach its capacity, while storing it for too short might nullify its performance advantages.
- **Single point of failure (SPOF):** Having a single cache server as the gateway between the web and data tiers presents a vunerability in your system if it goes offline. It is recommended to not only have multiple cache servers accross multiple data centers but also allocating a little more memory to each cache server than necessary to provide a buffer as usage increases.
- **Eviction policy:** What happens when a cache server reaches its capacity? Some of the common policies are, of course, least-recently-used (LRU), least-frequently-used (LFU), and first-in-first-out (FIFO).

## Content Delivery Network (CDN)

Content delivery network is a service used to keep static assets (such as images, videos, CSS, and JavaScript files) closer to the end-user. By doing so, it makes applications load them significantly faster, because the physical latency is lower.

A CDN service works similarly to a cache tier for static assets. When an user visits our website, for example:

1. The CDN server closest to them checks if the necessary assets are already stored in its cache
2. If it is, it quickly returns the assets.
3. If it isn't, it requests the assets from the an origin, which can be a web server or an online storage like Amazon S3.
4. It then stores the assets in its cache (the origin also provides how long the asset should be cached for, a time-to-live or TTL).
5. And returns the assets back to the user.

Due to its similarities to the cache tier, many of the considerations are similar. Since it is a third-party service, some others are:

- **Cost:** CDN companies charge per request so the performance benefits of using one need to be taken into consideration.
- **Fallback:** What happens if the service goes offline? There needs to be a logic in place to request data directly from the origin if this happens.
- **Invalidation:** Does the service provide ways to invalidate (i.e. remove) data stored in its cache, such as API methods provided by them or object versioning?

With a CDN, static assets are no longer actively served by web servers (they are still stored in the database and might still be served when not in cache) and, as with a cache tier, it further lightens the traffic load on the data tier.

## Stateless Web Tier

In order to scale our web tier horizontally, we have to move state data out of it.

State data is data that is kept in the server between requests (e.g., the user's session data or shopping cart data). Keeping this type of information in the web tier requires requests coming from the same user to be routed to the same web server, otherwise data is lost. Load balancers are capable of doing this using what's called sticky sessions, however it causes an overhead in the connection between user and server.

Keeping state in the web tier also causes problems when adding more servers or preventing system failure, because adds other criteria than traffic volume when distributing or redistributing users in both scenarios.

The solution i to keep a separate session storage tier which can be accessed by all servers. It is basically a persistent database (relational or non-relational) that allows the only criteria for web server traffic distribution to be server load, which in turn simplifies horizontal scaling and even enables auto-scaling.

## Data Centers

Data centers allow us to not only have multiple web, database, and cache servers, but also distribute them across multiple regions. Load balancers can then distribute traffic not only based on load, but also on location. Therefore, the biggest advantages of having data centers are, of course, availability/reliability and performance.

Some other considerations when adding data centers are:

- **Data synchronization:** How will data be consistent between each data tiers? A common solution is to keep data replicated among multiple data centers.
- **Test and deployment:** In order to maintain the application quality consistent between regions, it is important to test them. A common solution is having automated deployment and testing tools.

## Message Queue

A message queue is a persistent component **stored in memory** that supports asynchronous communication. It basically works as a buffer between two types of services: producers that publishes messages (orders) and consumers that consume these messages and execute the actions relayed in them. The biggest advantage of message queues is they allow us to decouple producers from consumers: If one of the parties fail, the other can still continue to function as the ability to send and receive messages is not hindered. Decoupling also means we can scale both sides independently.

## Logging, Metrics, Automation

In summary:

- **Logging:** To monitor and log errors in the application in order to solve them.
- **Metrics:** Collect internal data to gain business and health insight of our application.
- **Automation:** Improve the productivity of our application by automating processes such as build, test and deployment.

## Database Scaling

We've already talked about vertical and horizontal scaling of our web tier. Doing so in the data tier is very similar: Vertical scaling presents hardware limits, a hefty cost increase, and risks of single point of failure, so horizontal scaling is the recommended approach here as well.

Horizontal scaling is different than replicating the database because we are not copying and pasting the database into new server machines, we are spreading the same database (which grew too big for a single machine) across multiple ones. This process is called **sharding**.

Although each shard contains the same schema (the same "table"), each of them contains unique data. The challenge then becomes how we define which data is stored in which shard. Usually this is done by something akin to a hashing function, which uses one or multiple values of the incoming data (called sharding keys) and spits out the id of the shard which the data must go to. A simple example of a hashing function is a modulo function (e.g. if we have 4 shards, simply get the ID of the incoming data and perform a _id % 4_ on them).

Sharding is a great horizontal scaling technique, but it also present some obvious challenges:

- **Resharding:** If due to rapid data expansion or uneven data distribution one or more shards reach their capacity, we need to add shards to our distribution and rearrange data. Consistent hashing is usually used to solve this issue, which is a technique that minimizes the number of data entries that needs to be remapped.
- **Celebrity problem:** When a few of the shards are accessed way more than all the others and become overwhelmed with read operations. Allocating shards to "celebrity data" is usually done to solve this issue.
- **Join and de-normalization:** With relational databases, once it is sharded it is costly to perform join operations across multiple shards. To solve this, the database can be de-normalize (i.e., adding redundant copies of the same data on across shards) to improve queries on a single table. De-normalization can have negative side-effects if done poorly because the redundant data might not be up-to-date.
