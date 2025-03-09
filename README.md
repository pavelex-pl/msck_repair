# msck_repair
The repository contains information to speed up MSCK REPAIR at least 100 times

Working for several customers I see that many of them use parquet tables witht high number of partitions.
Ability to see new data sometimes is very critical. For one of the customers implemented solution below speeded up usual MSCK REPAIR functionality in more than 100 times.

# Hive Metastore
Hive metastore is a database which contains metainformation about tables. It contains all partitions for parquet tables. If such partitions are not present in Hive Metastore then data from the paritions are not visible inside the table even if add new paritions in parquest table location.

# How usual msck repair works
Normal msck repair works in single threaded mode inserting 200 partitions at a time. The way to speed up MSCK REPAIR is to make it parallel.

# How to speed up MSCK REPAIR
Inside Hive Metastore database there is a sequence_table table, which contains information about latest ids inserted into partitions and partitions_params tables and some other tables.
Next MSCK REPAIR functionality first has to increase such ids and then insert partitions. Again, usual way how MSCK REPAIR works - in single threaded way.
The way to speed up it:
  * get all new partitions - need multithreaded storage scan.
  * increase all needed IDs in Hive Metastore
  * fill all tables needed for paritition in multithreaded way.


