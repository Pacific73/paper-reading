## The Google File System

[TOC]

### Abstract

GFS provides fault tolerance while running on bunch of inexpensive commodity hardware, and delivers high aggregate performance.

### 1. Introduction

GFS shares the same goals as before such as **performance**, **scalability**, **reliability** and **availability**.

4 Design Points

1. Component failures are the norm rather than the exception.

   application/OS/memory/human/disk/network/power bugs...

2. Files are huge by traditional standards.

3. Most files are mutated by appending new data rather than overwriting existing data.

4. Co-designing the applications and the file system API benefits the overall system by increasing our flexibility.

### 2. Design Overview

#### 2.1 Assumptions

1. The system is built from many inexpensive commodity components that often fail.

2. The system stores a modest number of large files.

3. The workloads primarily consist of two kinds of reads: large streaming reads and small random reads. 

   In large streaming reads, individual operations typically read hundreds of KBs, more commonly 1 MB or more. A small random read typically reads a few KBs at some arbitrary offset.

4. The workloads also have many large, sequential writes that append data to files.

5. The system must efficiently implement well-defined sementics for multiple clients that concurrently append to the same file.

6. High sustained bandwidth is more important than low latency.

#### 2.2 Interface

GFS supports `create/delete/open/close/read/write`.

Moreover, GFS supports `snapshot/record append`.

`record append` supports appending concurrently while guaranteeing the atomicity of each individual client's append.

#### 2.3 Architecture

GFS has a single master and multiple chunkservers. 

Files are divided into fixed-size chunks. Each chunk has a chunk handle. Chunkserver stores chunks on local disks as normal Linux files. (built on Linux File System) Each chunk is replicated.

Master contains all metadata. (namespace/access control information/mapping from files to chunks) It controls activities such as chunk lease management, GC and chunk migration between chunkservers. Master periodically communicates with chunkservers to give them instructions and collect their states.

GFS client provides API but not POSIX API.

Both chunkserver and client don't use cache. They depend on Linux internal cache.

#### 2.4 Single Master

Single master:

1. simplify the design
2. enable the master to make sophisticated chunk placement and replication decisions using global knowledge.

We must minimize its involvement in reads and writes so that it does not become a bottleneck.

Typical communication process: Master first, chunkserver second.



