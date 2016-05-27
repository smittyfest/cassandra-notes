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
Apache Cassandra runs as a cluster that can be thought of as a giant hash-ring.
