## 1. Web Server

Web servers receive traffic from both mobile and web applications. They are responsible for handling tasks such as returning web pages, performing data requests, and executing business logic.

## 2. Domain Name System (DNS)

Domain Name System (DNS) are usually third-party services in which our application's URL is converted to our web server's IP. It is basically a mapping server that converts user-readable URLs into their respective IPs.

In order to access a server the request flow goes like this:

1. The user-typed URL goes through the DNS, which returns the IP
2. The IP is then used to access the web server directly
3. The web server returns the HTML page or JSON response

## 3. Databases

Usually, relational databases are a easy go-to solution when we need to add a database tier to our application. However, a few use cases where a non-relational database might be recommended are the following:

- Database request requires really low response times
- The data is unstructured (i.e. there are no relations between each type of data)
- You need to store massive amounts of data

## 4. Vertical Scaling vs. Horizontal Scaling

### Vertical Scaling

Vertical scaling means improving our server machines' hardware (CPU, RAM, etc.). It is recommended when traffic is not the problem, but operational performance is.

It comes with a serious limitation, since hardware has a upper performance limit of current technology, and it is usually very costly.

### Horizontal Scaling

Horizontal scaling means increasing our server machines' number. It is usually the recommended solution for large applications, not only because of cost, but because it prevents system failuer (i.e. more machines are available if one goes offline).

## 5. Load Balancer

A load balancer is a service that evenly distributes incoming traffic among multiple web servers. It is used as a intermediary between the user layer and web servers layer: Instead of delivering a web server's IP, the DNS gives us the load balancer's, which then communicates with a web server through a private IP, which can only be accessed by machines in the same network.

In other words, the advantages of a load balancer are the following:

1. **Security:** Our web servers are now unreacheable by the common users.
2. **Failover:** Traffic is redirected to a healthy server if one goes down.
3. **Scalability:** More servers can be allocated if traffic grows rapidly.

## 6. Database Replication

With load balancers, we've added scalability and redundancy/failover support to our web tier. Moving to our data tier, we can do the same thing by applying the replication technique.

Database replication means to have multiple copies of the same database, usully by having a original, master copy and multiple slaves copies. Since read operations are usually more numerous than write ones, generally speaking:

- The master receives all write requests (which include inserts, deletes, and updates) and always maintain the most up-to-date version of the database.
- The slace copies replicates the data in the master copy and are responsible for all read requests.

The advantages of the master-slave model are:

- **Performance:** Write and multiple read requests can work in parallel, allowing our system to handle a higher volume of requests.
- **Reliability/Availability:** By having multiple database servers, we are not only safe against data loss, but also against our database not being available to our users.

If a slave database goes offline, read operations only need to be redirected to a healthy one. However, if the master database goes offline, a slave is assigned as the new master and all write requests are directed to it and since its data is not guaranteed to be up-to-date with the previous master, data recovery methods will have to be performed (there are models such as multi-masters and circular replication that circumvent this scenario).

## 7. Cache

Cache is temporary storage layer which stores the result of recent data requests. It's most common logic, read-through cache, works as follows:

1. The web server request a certain data
2. If the data exists in the cache it is returned quickly
3. If it doesn't, the cache requests the data from the database, stores it for a limited amount of time for future requests, and returns it.

It's not only faster to obtain data from a cache (since it skips the underlying database fetching logic), but it also significantly decreases the traffic load from the database. Having a separate cache tier also allows us to scale it independently.

Cache is a good design decision if data is read frequently AND modified infrequently, since data in the cache might not be up-to-date with data in the database (that is why database-cache synchronization consistency is also a worry). Also, keep in mind the issue of **single point of failure (SPOF)** having a single cache serve might present, since it is now the middleman between the web and data tiers, it going offline breaks our entire application.

The last thing to keep in mind when working with cache is the eviction policy, that is how we handle the removal of data when we reach the server capacity. Some of the common policies are, of course, least-recently-used (LRU), least-frequently-used (LFU), and first-in-first-out (FIFO).

## Content Delivery Network (CDN)

Content delivery network is a service used to keep static assets (such as images, videos, and JavaScript files) closer to the end-user. By doing so, it makes applications load them significantly faster, because the physical latency lower.

A CDN service keeps copies of our static assets across multiple servers on demand (i.e. a local cache). When a user visit our website, for example:

1. The CDN server closest to them check if the necessary assets are stored in its cache.
2. If it is, it quickly returns the assets
3. If it isn't, it requests the assets from the an origin (e.g. a web server or an online storage like Amazon S3)
4. It stores the assets in its cache (the origin also provides how long the asset should be cached for, a time-to-live or TTL)
5. Returns the assets back to the user

CDN services are a variation of cache run by third-party companies. So the consideration of highly-read but lowly-modified data still applies. Other considerations to take are:

- **Cost:** CDN companies charge per request so the performance benefits of using one need to be taken into consideration.
- **Fallback:** What happens if the service goes offline? There needs to be a logic in place to request data from the origin if this happens.
- **Invalidation:** Does the service provide ways to invalidate (i.e. remove) data stored in its cache, such as API methods or object versioning?

With CDN, static assets are no longer served by web servers (but they might still be stored in them) and, as with cache, it lightens the traffic load on the data tier.
