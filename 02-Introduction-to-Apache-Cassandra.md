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
<p>Data is replicated to multiple servers, and all those servers are equal. There are no masters or slaves or replica sets.
<p>No configuration servers, no zookeeper.
<p>Data is partitioned around the ring via the __partition key__.
<p>Data is replicated to __replication_factor__ nodes, where replication_factor is the number of replicas we wish to store, which is configurable.
<p>All nodes hold data and can answer queries, both reads and writes.
<p>Location of data on the ring determined by the __partition key__.
### Brewer's Cap Theorem
During a network partition (when computers can't talk to eachother) either between datacenters or on a single network, you can either get consistency or high availability, but not both. So if two machines can't talk to eachother, and you do a write to them and you have to be completely consistent, the system is going to appear as if it is down. So if we give up consistency we can have high availability. This is what Apache Cassandra chooses, __Availability__ and __Partition Tolerance__. In most applications this is way better than downtime.
<p>From datacenter to datacenter it is completely impractical to be consistent across datacenters. We want to __asynchronously__ replicate our data from one datacenter to another because it just takes way too long to get data from the U.S. to Asia or vice-versa. We are limited by the speed of light, and this is something we are just never going to get around. This is why Apache Cassandra chooses Availability and this is how it affects Consistency.
<p>Apache Cassandra does not impose any one Fault-Tolerance model on you, so some of the dials it provides give you a sliding scale between consistency and high availability.
#### Replication Factor
<p>How many replicas or copies of data should there be within your cluster. A typical production replication factor is typically three. When you write data to a node 'A', a replica is sent over to nodes 'B' and 'C' as well.
