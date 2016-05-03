# Basic Cassandra Internals

#### The Write Path
When you send a write to a Cassandra cluster, all nodes in the ring are equal so any node could service that write request that you send in. Whichever node you happen to get routed to for that request is the __coordinator node__. It does the coordination with the rest of the nodes in the cluster on behalf of your query. This is not a special node type, just for that given request its the one that is going to do the coordination.
<p>Once a write request hits an individual node two things happen.
<p>The first thing that happens is that write or mutation gets written to the __Commit Log__. The __Commit Log__ is an __append-only__ data-structure that does very-fast sequential-io even on HDDs, and once its written to the comit log and we have that durability the next thing Cassandra does is it merges that mutation up into an in-memory representation of your table, which is called a __Memtable__.
<p>Once it does that it can respond back to the coordinator "yep, we have your data" and the coordinator relays this response back to the client.
<p>Cassandra has very fast writes, and is good at ingesting lots of data really quickly. Part of this is because of the simple write path.
<p>Every time you write to Cassandra, every column value that you write gets a timestamp. This is important for how SSTables work and for Compaction. As data gets written to the __memtables__ eventually we start to run out of main-memory, so eventually we have to take the memtables and flush them to disk. Cassandra does this behind-the-scenes asynchronously, with a whole bunch of very-fast sequential-io where it takes the in-memory representation memtable and serializes it to disk into something called an __SSTable__.
<p>Cassandra does not do any updates in-place or deletes in-place.

#### The Read Path

