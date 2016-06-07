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
