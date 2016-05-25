# Introduction to Apache Cassandra

Apache Cassandra is a fast distributed database built forhigh availability and linear scalability.

<p>We want predictable performance so we can get low-latency and guarantee SLA.
<p>We want our cluster to have the same performance as it scales up from five nodes to ten nodes to 1000 nodes.
<p>No SPOF. Apache Cassandra is a peer-to-peer architecutre, so there are no single points of failure, no master-slave, no failover, no leader-election.
<p>Out of the box we can do multi-datacenter. We want to be able to survive the failure of an entire datacenter, Cassandra can do this easily. This is much more difficult with an RDBMS.
<p>Commodity hardware. Apache Cassndra works well with cheap hardware.
<p>Easy to manage operationally as the ring grows.
<p>Not a drop-in replacement for RDBMS. Not the same data-model, you need to design your application around Apache Cassandra's data-model and rules.

#### Concepts
Apache Cassandra runs as a cluster that can be thought of as a giant hash-ring. All nodes in the cluster are equal and each node owns a range of hashes, like a bucket of hashes. 
<p>When you define a data-model in Apache Cassandra one of the things you specify when you create a table is the __Primary Key__, and part of that primary key is the __Partition Key__. 
<p>The __partition key__ is what is used when you insert data into Cassandra. The value of that partition key is run through a __consistent-hashing function__ and depending on the hash value computed we can figure out which bucket or which range of hashes that value fits into and thus __which node__ we need to go talk to in order to distribute the data around the ring.
