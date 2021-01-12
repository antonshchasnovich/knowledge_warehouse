# Structured APIs

## RDD (Resilient Distributed Dataset)

RDD - is the most basic abstraction in Spark. There are three vital characteristics associated with an RDD:

* **Dependencies** - a list of dependencies that instructs Spark how an RDD is constructed with its inputs is required. When necessary to reproduce results, Spark can recreate an RDD from these dependencies and replicate operations on it. This characteristic gives RDDs **resiliency**.
* **Partitions** -provide Spark the ability to split the work to parallelize computation on partitions across executors. In some cases Spark will use locality information to send work to executors close to the data. That way less data is transmitted over the network.
* **Computing functions => Iterator** - computing functions produces an Iterator for the data that will be stored in the RDD.

## Structuring Spark
Express computations by using common patterns such as: filtering, selecting, counting, aggregating, averaging, and grouping. It helps Spark to uptimize used algorithms. Structuring provides next benefits: ***expressivity*** (easy for reading and understanding), ***simplicity***, ***composability*** (Spark can inspect or parse our query and understand our intention, so it can optimize or arrange the operations for efficient execution), uniformity

**Structuring benefits:**
* expressivity - easy for reading and understanding
* simplicity
* composability - Spark can inspect or parse our query and understand our intention, so it can optimize or arrange the operations for efficient execution.
* uniformity