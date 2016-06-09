# Relational Databases

Small Data - Data in text files or a small database like sqlite3. Small utilities are used to analyze
 the data. These are usually unix tools such as Sed and Awk. Maybe you whip up a Python or Ruby script for data analysis. In general its a one-off script that doesn't need concurrency because the data is not being shared by anybody.
The data might reside on your laptop and a batch might take maybe 30 seconds to run if its a really big file or sqlite3 database. 

Medium Data - Fits on a single machine, using an RDBMS like PostgreSQL or MySQL. Typically supports hundreds of concurrent clients.

ACID Guarantees - Atomicity, Consistency, Isolation, Durability.

High Availability - How do relational databases perform when scaling for high availability?
