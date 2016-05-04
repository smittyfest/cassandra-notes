# Basic Cassandra Internals

#### The Write Path
When you send a write to a Cassandra cluster, all nodes in the ring are equal so any node could service that write request that you send in. Whichever node you happen to get routed to for that request is the __coordinator node__.

#### The Read Path

