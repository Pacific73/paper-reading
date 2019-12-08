## MapReduce: Simplified Data Processing on Large Clusters

[TOC]



### Abstract

Main operations: Map & Reduce.

Some features:

1. Parallelized
2. data partition
3. crossing-machine scheduling
4. failure handling
5. communication management
6. scalable

### Introduction

Scenario: big data computation.

Map & Reduce -> primitives from functional programming languages.

### Programming model

Map & Reduce

### Implementation

#### Overview

1. Input data is on GFS. Split input data into M pieces.
2. Master copy programs and assign workers.
3. Map worker reads the contents of corresponding input split. Then execute map function.
4. Periodically, buffered pairs are written to local disk, partitioned into R regions. Locations are passed back to master.
5. Reducer gets notified by master and uses RPC to read intermediate data from map worker's local disks. Then reducers does the sorting.

#### Master data structures

Master stores:

1. Task state
2. worker machine identity information
3. propagate locations of intermediate files

#### Fault Tolerance

##### Worker failure

Master pings every worker periodically. (sending heartbeat packets)

If worker doesn't respond, it will be marked as "FAILED". Then map tasks finished by it will be reset back to "IDLE" state hence can be scheduled on other workers. (Because intermediate data is stored on map worker's local disk)

Solution: re-execution.

##### Master failure

Solution: take snapshot periodically.

##### Semantics in the presence of failures

Map worker generates R intermediate files. (1 per reducer)

Reduce worker generates 1 output file.

Requirement: deterministic computation. Or result may differ each time.

#### Locality

Coordianted with GFS. Mapper will read input data from the nearest point in GFS to reduce expense on network.

#### Task granularity

M and R should be far more tha number of workers.

1 worker can execute multiple tasks.

#### Backup tasks

Some stragglers will drag down the performance:

1. Some abnormal phenomenon: e.g. bad disk -> read performance down
2. multiple tasks resource contention
3. other situations: cache disabled

Solution:

When almost finished, master will assign backup tasks to idle machines. Whoever finishes first will write result as final output to reduce unncessary waiting time.

### Refinements

#### Partitioning function

Mapper uses partitioning function to partition data into R pieces. (default: hash)

#### Ordering guarantees

Data is in order in every individual partition. (ascending order)

#### Combiner function

Used in mapper and reducer. (partial combiner)

Preventing data distribution from being not uniform.

#### Input and output types

#### Side-effects

#### Skipping bad records

#### Local execution

#### Status information

Hadoop UI HTTP service to indicate progress.

#### Counter

Sync counter information in heartbeat packet.

Master has a set of mechanism to prevent multiple-counters.

Possible reasons:

1. backup tasks
2. failure re-execution

### Performance

Refer to paper version. Figure is clearer.