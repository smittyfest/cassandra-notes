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

<p>__Secondary Indexes__. We also find that as we grow, querying secondary indexes becomes much more difficult. If we take our servers and split our users into four different shards, and then we haven't sharded our users on something like State, and we want to do a query "give me all the users in the State of Massachusetts" then we have to hit __all__ of the shards. This is extremely non-performant. If we have 100 shards, we need to run 100 queries. As a result, we end up denormalizing again. So now we store two copies of our users, one by user id and another by State.
<p>__Adding Shards__. Whenever we add shards to our cluster, say we want to double the number of shards and go from four to eight shards, we now have to write a tool that moves everything over. This requires a lot of coordination between development and ops, because there are a lot of edge cases that can come up when you are moving your data around. So you have to think of all of them in your application and coordinate with ops.
<p>__Schema changes__. Managing your schema is more difficult too. If you have 20 shards all with the same schema, you now have to write tools to apply schema changes to all the different shards in your system. All nodes in the system, masters or slaves, need these schema changes. 
#### Introducing High Availability to an RDBMS-based system.
<p>When you introduce replication to this master-slave architecture, you have to decide how to handle failover. Maybe you build an automatic process to perform failover, maybe its a manual process, where someone notices the database has gone down and pushes a button to switch-over to the failover service. If its an automated process, what's going to watch over this automated process to ensure it doesn't go down? In any scenario, automated or manual, you are still going to have downtime. Trying to do this with a relational database over multiple datacenters is a disaster, it is very hard to do. Planning things like OS upgrades is difficult with an RDBMS-based relational system.
### Summary of ways Relational Databases struggle with large volumes of data:
<p>Scalability is painful. Expensive hardware, having to shard, replication that falls behind.
<p>ACID guarantees no longer hold. Database behaves like a key-value store now.
<p>When we want to double the size of a cluster, we have to re-shard. Requires a lot of coordination.
<p>Denormalization. All the third-normal form queries are gone, now we write the data in a bunch of different tables, sometimes we just JSON serialize it, etc.
<p>Multi-Datacenter is a big mess with MySQL or PostgreSQL.

#### Lessons Learned
<p>If we were to take some of the lessons we've learned and try to apply them to a brand-new database, what would we do differently?
<p>Consistency is not practical. ACID consistency probably not practical in a large, distributed system. We give that up.
<p>Manual Sharding and re-balancing is very hard. we push that responsibility to our cluster. Developers don't have to write any special code or tools to handle moving from 10 to 20 nodes.
