# Relational Databases

## Small Data 
<p>Data in text files or a small database like sqlite3. <p>Small utilities are used to analyze
 the data. These are usually unix tools such as Sed and Awk. Maybe you whip up a Python or Ruby script for data analysis. In general its a one-off script that doesn't need concurrency because the data is not being shared by anybody.
<p>The data might reside on your laptop and a batch might take maybe 30 seconds to run if its a really big file or sqlite3 database. 

## Medium Data
<p>Fits on a single machine, using an RDBMS like PostgreSQL or MySQL. Typically supports hundreds of concurrent clients. <p>These relational databases provide ACID Guarantees - Atomicity, Consistency, Isolation, Durability.
<p>Typically scales vertically. More expensive hardware, more main-memory, or faster processors.

## High Availability
<p>How do relational databases perform when scaling for high availability?
#### ACID no longer applies.
<p>For example, we may have a scenario where we have a single MySQL master node and we have a client communicating with that master node. We have a read-heavy workload, so we decide to add replication.
<p>One of the things that is important to know about this replication is that the data is replicated __asynchronously__. This leads to the behavior known as __replication lag__.
<p>What happens is when the client decides to write to the master, it takes a little while for the written data to propagate over to the slave node. If the client decides to do a read that gets routed to the slave node before the data is replicated, the client will get __stale data__.
<p>We have lost our consistency completely within the scope of our database. The ACID guarantees that we built our application around are no longer valid. None of the operations that we perform now are performed in isolation and they are definitely not atomic. We have to re-code our application to account for replication and __replication lag__.
<p>Another performance issue with relational databases is queries with unpredictable performance, sometimes generated behind the scenes by ORMs. Large queries with lots of JOINs, etc. Crazy queries like this can essentially lock the system up while they execute, due to having to process the query, execute disk seeks, etc.
<p>Queries like this can cause lots of problems. They are a side-effect of using __third-normal form__ for data-modeling.
<p>One way to alleviate this problem is through __denormalization__. We create a table specifically geared towards answering that complex query. At write time we denormalize and write the data into this table so at read-time we can do a simple __select *__ or other very simple query that doesn't have a lot of complicated JOINs in it. That means that now we have duplicated copies of our data, violating third-normal form.

#### Sharding
<p>As we continue to scale our application, one of the next things we have to do is implement __sharding__. Sharding is when you take your data and, instead of having it all in one database and one master, you split the data up into multiple databases. This works ok for a while, but there are some problems.

<p>Data is all over the place. Even if you are relying on a single master to handle all of your OLAP queries, you can't do it anymore. All of your JOINs, Aggregations, etc. are gone. You have to keep building denormalized views of your data in order to answer queries efficiently.
