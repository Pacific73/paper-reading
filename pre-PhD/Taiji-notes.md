# Taiji: Managing Global User Traffic for Large-Scale Internet Service at the Edge

[TOC]

## Abstract

### Definition

Taiji, a system for managing user traffic for large-scale Internet Services that accomplishes 2 goals:

1. Balancing the utilization of data centers
2. Minimizing network latency of user requests

### Methods

Taiji models edge-to-datacenter traffic routing as an assignment problem - assigning traffic objects at the edge to data centers to satisfy service-level objectives.

Key: Constraint optimization solver -> generating an optimal routing table that specifies the fractions of traffic assignment.

Taiji routes highly-connected users to the same data center.

## Introduction

Focus: serve dynamic traffic while optimizing data center capacity and minimizing network latency.

### Challenges

1. Capacity Crunch

   Traffic imbalance between DCs

2. Product heterogeneity

   Serve sticky connection traffic

3. Hardware heterogeneity

   How much traffic a data center can handle can evolve.

4. Fault tolerance

   Network failures/Power loss/...

### Contribution

1. First system that manages user traffic to dynamic content for Internet service in a user-aware manner
2. Model user traffic routing as an assignment problem, which can be solved by constraint optimization solvers.
3. Present connection-aware routing that improves caching effectiveness and backend utilization.

## Background

### Overview of FB Infra

```
User request -> DNS -> 1 of edge nodes -> L4 LB -> L7 LB (Edge LB)
																												| 
													  Taiji (Routing traffic)  => | 
																												v
Backend <- Backend LB <- Frontend <- Frontend LB	<- Data Center
```

### Backbone capacity

Abandunt backbone capacity between every edge node to data centers to allow for load spikes, link failures, maintenences

### Traffic types

Stateless v.s. Sticky

## Taiji

```
    policy/real-time statistics
               |
               v
Runtime (connection-aware routing tech)
               |
               v
         Routing table
               |
               v
            Pipeline
               |
               v
specific routing conf for each Edge LB
```

Connection-aware routing groups highly-connected users into "buckets" and selectively routes buckets to the same data centers, based on the traffic fractions specified in the routing table.

### Runtime

#### Inputs to Runtime

2 kinds of dynamic data:

1. opeartional state of the infrastructure (capacity/health/utilization)
2. measurement data (edge traffic volumes/edge-to-datacenter latency)

Reader for reading

#### Modeling and Constraint Solving

Taiji models traffic load as **requests per second (RPS) for stateless traffic** and as **user sessions for sticky traffic**.

Another input: utilization of data centers.

Taiji re-evaluates traffic allocation decisions every epoch.

Solver employs a local search algorithm using the "best single move" strategy.

#### Pacing and Dampening

Onloading safe guard breaks up large changes into multiple steps. (缓步慢走)

Taiji has 2 levels of protection to prevent unnecessary oscillations.

1. Skip the changes if shift does not reach minimum shift limit.
2. Specify a dampening factor.

Sensitivity Analysis: Run several sensitivity analysis to tune the threshold presets.

### Traffic pipeline

Connection-aware routing disseminates the routing configuration out to each Edge LB. (Takes a bout 1 minute)

#### Connection-Aware Routing

It builds upon Social Hash that partitions that community graph into user buckets.

Taiji desires smaller bucket sizes of the order of 0.01% of global traffic.

Connectin-aware routing trades off granularity and locality by coupling:

1. offline user-to-bucket assignments
2. online bucket-to-datacenter assignments

##### Offline user-to-bucket assignment

Community hierarchy is created in the form of a complete binary tree based on Social Hash.

This partitioning is produced offline on a weekly basis.

2 types of connections where tree-based partitioning does not work well:

1. User to highly-connected entity (Obama)
2. One-time interactions

##### Online bucket-to-datacenter assignment

Stable Segment Assignment Algorithm.

This algorithm strives to preserve bucket locality by assigning a while level of buckets (called a segment) in the community hierarchy to the same data center.

This algorithm achieves stable routing.

#### Edge LB Forwarding

We maintained fine-grained user-to-bucket mapping in data centers and let Edge LBs route traffic at the granularity of buckets.

### Correctness and Reliability

#### Testing

The major risk to Taiji is not a fail-stop bug, which are easily detected via a litany of unit tests, integration tests, etc., but semantic bugs that generate incorrect routing configuration. (already affected service)

1. Regression test that leverage historical snapshots of data
2. online testing that covers weekly patterns

##### Input and output validation

Validate inputs: cross-check data from different sources.

Validate outputs: check against pre-defined invariants.

#### Tolerating Failures

Register metrics based on their own health monitoring.

##### Dependencies

Only depends on the monitoring infrastructure.

##### Hardware

Taiji runs on commodity hardware. We deploy multiple Taiji instances in different geographic regions.

#### Embracing Site Events

In FB, traffic control is also used as the mechanism for 2 types of site events:

1. Reliability tests. FB runs regular load tests as well as drain and storm tests which shift traffic into and out of a data center, respectively.
2. Failure mitigation. We occasionally drain traffic out of a data center or a set of data centers to mitigate unexpected widespread failures.

#### Backend Safety as a First Class Principle

Protect backend systems from being overloaded by diurnal traffic patterns or peak load.

1. leverage safety guards to ensure that the frequency and magnitude of changes are smooth without load spikes.
2. account for minimum and maximum levels for operation, deciding when to bail out.

Disaster-readiness strategy.

## Evaluation

...

