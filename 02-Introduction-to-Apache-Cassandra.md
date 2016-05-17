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
#### Replication-Factor
<p>How many replicas or copies of data should there be within your cluster. A typical production replication-factor is typically three. When you write data to a node 'A', a replica is sent over to nodes 'B' and 'C' as well.
This replication of data is performed automatically by Apache Cassandra. You pick the number of servers to hold replicas and data is always replicated to each replica.
<p>You set the __replication-factor__ when you configure a __keyspace__. A __keyspace__ in Apache Cassandra is essenetially a collection of tables. It is very similar to a schema in Oracle or a database in MySQL or Microsoft SQL Server.
<p>If a machine goes down during replication, the missing data is replayed via Cassandra's __Hinted Handoff__ mechanism. Replication happens asynchronously, and if a machine goes down, whatever node you happen to be talking to at that time stores a __hint__, and when that node comes back up and re-joins the cluster, all the writes that the down node missed are replayed back to it in the form of these __hints__.
#### Consistency-Level
Consistency-Level can be set on any given read or write request that you do as a developer from your application talking to Apache Cassandra.
<p>Consistency-Level means how many replicas do I need to hear from when doing a read or a write for that read or that write to be considered successful. So if I'm doing a read, how many replicas do I need to hear from before Cassandra gives the data back to the client, or if I'm doing a write, how many replicas need to confirm that they have received the data and written it to disk before Cassandra replies back to the client and says "yep, got your data".
<p>The two most popular __consistency-levels__ are __ONE__ and __LOCAL_QUORUM__.
Consistency-level of one means one replica. Client writes to the cluster, the 'A' node gets its copy, and since we're writing at a consistency-level of one, we can acknowledge right back to the client immediately "yes, we got your data". Just because you write with a consistency-level of one __does not__ mean that Cassandra isn't going to honor your __replication-factor__ still.
<p>The other popular consistency-level used a lot is __QUORUM__. __QUORUM__ means a majority of replicas. So in the case of replication-factor of three, the quorum would be two out of three for a 51% or greater quorum. A client writes to the 'A' node. The 'A' node gets a copy, the 'B' node gets a copy and responds, and at that point once we have 2 out of the 3 replicas that have responded, we can acknowledge back to the client "yes, we have your data". The replication-factor is still honored, we're just not waiting on that extra node to reply before we acknowledge back to the client.
<p>Consistency-level impacts how fast you can read and write data. If you use a lower consistency-level like one, if you are only waiting on a single server you're going to be able to read and write data very quickly. If you're using a higher consistency-level it will be much slower to read and write data.
<p>Consistency-level will also affect your availability. If you choose a higher consistency-level where more nodes need to be online and you need to hear from them in order to acknowledge reads and writes, you are going to be much less available, and less tolerant to nodes going down. However, if you choose a lower consistency-level like one, you are going to be much more highly-available, you can withstand two nodes going down in the example of a replication-factor of three and still be able to do reads and writes in the cluster.
<p>Apache Cassandra lets you set the consistency-level for every query you do. It doesn't impose one model, you as the developer get to choose which consistency-level is appropriate for which parts of your application.
#### Multiple Datacenters
Because we get asynchronous replication, its very easy to do multiple datacenters. So when we do a write to a single datacenter, we get to specify our consistency-level as __ONE__ or __QUORUM__, and when we do multiple datacenters we might specify __LOCAL_ONE__ or __LOCAL_QUORUM__.
