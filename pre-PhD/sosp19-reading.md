## SOSP 19'论文阅读列表

### Session 1: Machines, Learning

#### PipeDream: Generalized Pipeline Parallelism for DNN Training

在神经网络训练时引入了pipeline机制。现有的并行训练采用intra-batch方式，而PipeDream采用了inter-batch方式，能够将训练计算分布到各个worker上，对已有的机制进行改进，提高训练效率和通量。

总的来说，PipeDream使用了系统中的经典模型流水线来优化深度学习，算是System for AI的代表作品。

#### A generic communication scheduler for distributed DNN training acceleration

data parallelism training 可以通过把计算和通信一起跑的方法加速，但是目前的方案都是针对具体的机器学习框架设计的。他们提出了一个统一的 scheduling 框架，能够在不同的机器学习框架、不同的网络通信架构、不同的网络协议（比方说RDMA）上面实现更高的训练速率。

总的来说，ByteScheduler针对目前DNN通信生态碎片化的现状，提出了统一的整合方案，不仅能支持不同的情况，还综合运用系统设计（credit）和机器学习（Baysian Optimization）将性能调整到最佳。当然，随着DNN进一步发展，更多的运行时场景还将出现，ByteScheduler依然有很大的拓展空间。

#### Parity Models: Erasure-Coded Resilience for Prediction Serving Systems

在 serve ML model 的时候，因为种种原因（网络、计算、宕机等等），prediction service 有可能无法在规定的时间内返回结果。把一个 query 直接发给多个 prediction service 然后返回最快的答案会极大地浪费计算资源。作者提出，把多个输入 encode 成一个新的 parity input，然后让不同的 prediction service 计算原来的输入以及这个新增的 parity input。如果有的 service 没能按时返回结果，就可以根据剩下的结果 decode 出来缺失的这个结果。

#### TASO: Optimizing Deep Learning Computation with Automated Generation of Graph Substitutions

现有的 DNN optimizer 都是基于手工编写的规则进行计算图的变换的。这些变换并不适用于所有的DNN结构以及所有的硬件；每当有新的算子被发明出来，就要花费非常多的人工成本来添加新的规则；一些非常细微的优化也经常会被忽略掉。作者的主要思路就是自动生成计算图的变换规则，然后形式化验证这些替换规则是不是对的。

### Session 2: It Must Be Secure (略)

#### Teechain: A Secure Payment Network with Asynchronous Blockchain Access

#### Fast and Secure Global Payments with Stellar

#### Notary: A Device for Secure Transaction Approval

### Session 3: Systems: Still buggy (略)

#### CrashTuner: Detecting Crash Recovery Bugs in Cloud Systems via Meta-info Analysis

#### The Inflection Point Hypothesis: A Principled Debugging Approach for Locating the Root Cause of a Failure

#### Finding Semantic Bugs in File Systems with an Extensible Fuzzing Framework

#### Efficient and Scalable Thread-Safety Violation Detection --- Finding thousands of concurrency bugs during testing

### Session 4: Keeping Things Private (略)

#### Privacy Accounting and Quality Control in the Sage Differentially Private ML Platform

#### Honeycrisp: Large-scale Differentially Private Aggregation Without a Trusted Core

#### Yodel: Strong Metadata Security for Voice Calls

### Session 5: It Must Be Correct (略)

#### Scaling Symbolic Evaluation for Automated Verification of Systems Code with Serval

#### Verifying Concurrent, Crash-safe Systems with Perennial

#### Using Concurrent Relational Logic with Helpers for Verifying the AtomFS File System

#### Verifying Software Network Functions with No Verification Expertise

### Session 6: Data, Data, Everywhere

#### Optimizing Data-Intensive Computations in Existing Libraries with Split Annotations

作者指出，数据密集型应用花费大量的时间在处理器和内存之间的数据移动，尤其是处理器越来越快而内存的带宽增长缓慢的现状下，这个问题越来越严重。虽然现在有 Weld 和 XLA 这类通过把计算程序编译成中间表示的方法可以增加计算的并行程度，但是如果现有的程序比如说 Pandas 和 Intel MKL 要采用这些方法的话，基本上要重写整个库。这篇文章就提出了通过给现有的函数定义加上 Split Annotation 的方式来增加程序的执行效率，好处就是只要加注解就行了，不用修改库的实现，而且因为这些库都是算法上高度优化的代码，可以保留这些人工的优化。

#### Niijima: Sound and Automated Computation Consolidation for Efficient Multilingual Data-Parallel Pipelines

这个工作主要是针对 Microsoft Scope 数据库的一些优化。Microsoft Scope 允许用户用 C# 编写自定义函数，然后混合到 SQL 语句里面。作者表示这种 SQL + 命令式语言的组合越来越流行，因为命令式语言的表达力更强，而用 SQL 又能够利用好现有的数据库优化。所以说这篇文章对其他类似的系统，比如说 Apache Spark，也是有借鉴作用的。

#### Nexus: A GPU Cluster Engine for Accelerating DNN-Based Video Analysis

深度神经网络服务与传统服务一样，服务提供者都对请求的延迟有目标（比方说 99% 的请求都能在 100ms 内返回）。该系统旨在高效率地调度GPU集群来运行深度神经网络服务，增大吞吐量，同时尽量满足延迟目标。现有的深度神经网络服务系统都是针对单一应用设计的，而我们的系统能够支持多个应用，充分挖掘集群层面、应用层面以及模型层面的优化空间。

### Session 7: The Revolution Will Be Distributed

#### Lineage Stash: Fault Tolerance Off the Critical Path

目前主流的分布式计算框架通过全局快照和日志这两种方式之一来实现容错。全局快照的运行时开销小，因为是异步的，但是恢复很慢，因为需要把上一个快照之后的操作回滚再重来。使用日志的话，因为粒度比较细，所以恢复的时候比较快，只要跟着日志走就行了，然而平时的计算都需要等日志写完才能进行，也就是在关键路径上，所以运行时的开销很大。这篇文章提出了 lineage stash 的方法，能够减少日志方法的运行时开销，但又能实现高效的恢复。

大体上，方法是在把任务交给另一个节点处理的时候，顺便带上当前节点的日志，同时异步地把日志写到一个全局的存储上面。这样一来，如果有一个节点挂了，其他节点可以把这个节点的日志写到全局存储上面，这样这个节点重新上线开始恢复的时候就可以完整地得到之前自己的日志，而且在平时执行任务的时候也不用等到日志写到存储上面。

#### File Systems Unfit as Distributed Storage Backends: Lessons from 10 Years of Ceph Evolution

这篇文章是一篇经验文章，讲的是分布式文件系统 Ceph 十年发展的经验。作者说 Ceph 一开始是构建在文件系统上面的，主要原因还是方便。他们花了10年时间才让他们基于文件系统的存储后端 FileStore 变得稳定起来，期间也经过多次大的架构变化，比如说从 btrfs 变成了 xfs。然后他们写了一个新的存储后端 BlueStore，不基于文件系统，把元数据存在 RocksDB 里面，数据访问直接通过块设备。他们说不出所料地新后端取得了2倍的性能，小得多 tail latency，还能够支持很多功能，比方说校验码、纠错码、透明压缩等等。但是出乎他们意料的是，这个新的后端只花了2年时间就稳定下来了。

作者提到了几个挑战。一个是事务支持。一个是支持新的硬件功能，比如说 Shingled Magnetic Recording 和 Zoned Namespace，要利用好这些新的技术就要使用和传统的硬盘很不一样的存储方式。

#### I4: Incremental Inference of Inductive Invariants for Verification of Distributed Protocols

这篇论文的目标是自动地找出分布式协议的归纳不变式。他们的启发是分布式协议的行为不随节点数量的增长发生变化。所以他们的系统的大体思路是，先用 Ivy 描述分布式协议，然后生成小的实例。基于这个小的实例，用 model checker 生成一些归纳不变式。I4 会自动地泛化这些归纳不变式，接着让 Ivy 来验证这些归纳不变式是不是对的。如果 Ivy 发现了归纳不变式不对，I4 会生成一个更大的实例，重新跑一遍这个过程。整个过程中，需要人工参与的地方有几个：一个是要描述协议本身；一个是如果 model checker 发现了反例，要 debug 这个协议的描述；一个是要给定一个初始的小的实例；还一个是当 model checker 跑不出来的时候要把 I4 生成的小的实例具体化，也就是把某些变量的值固定下来。

#### Aegean: Replication Beyond the Client-Server Model

这篇文章说 replication 专注于提供一个正确运行的单机服务器的抽象，但是这个对于现在很多的服务都是不适用的。比方说你在网站上面订机票，网站的后台需要联系银行的服务器收付款。假设网站的后台使用了主从复制，可以保证网站后台不会挂。但是如果在网站后台的主节点往银行发了请求，并且银行已经执行了这个请求之后，网站后台的主节点挂了，问题就会很复杂。因为备用节点并不知道主节点已经向银行发了请求，而且请求已经被执行了。这篇文章就提出了几个大方向上面的方法来指导这种包含了第三方服务的系统的设计。

### Session 8: Net Work

#### Snap: a Microkernel Approach to Host Networking

Snap是一个用户态网络系统，能够以众多灵活的组件，支持Google快速演进的需求。其中，这些组件支持边缘数据包交换、云平台虚拟化，流量整形策略增强，以及一个高性能、可靠的、RDMA-like的消息服务。

Snap加快了新网络功能的开发和部署，利用了地址空间分隔以及用户态软件开发效率的优势，以及网络服务升级时无需迁移应用等支持。同事，Snap通过组件化架构实现了高性能。该架构通过最小化共享状态集提升了主要的同步过程，通过一个新颖的内核态/用户态CPU调度器协同设计，支持了实时调度以及CPU资源的动态伸缩。实验证明，性能得到了极大的提升。

#### Risk-based planning for evolving data-center networks

数据中心随着用户流量而演进。当试图对网络进行改动、升级时，操作者需要冒着影响用户流量的风险，因为网络的容量降低，而且对故障和流量变化更加敏感。这种对用户流量的影响，最终会反映在操作成本上（比如对顾客退款补偿）。然而，筹划一次网络变动，同时最小化风险是有挑战性的，因为我们需要适应动态的流量变化以及开销函数，同时伸缩至更大的网络结构和变化。今天，操作者常使用最大化对偶容量的计划（MRC）。此计划常预示着在不同的动态流量下的更高开销。为此我们提出了Janus，它搜索一个更大的变化空间，通过利用数据中心网络的高维对称性。通过对Clos以及Facebook的流量轨迹进行评估，我们得出结论：Janus实时产生变动计划，只需要MRC planner开销的33-71%，同时拥有一定的灵活性。

#### Taiji: Managing Global User Traffic for Large-Scale Internet Services at the Edge

Taiji是一个管理大型互联网应用用户流量的系统，它实现了2个目标：1.平衡数据中心使用率 2. 最小化用户请求的网络延迟。它将从边缘到数据中心的流量路由问题抽象成了一个分配问题——将边缘的流量对象分配到数据中心，从而满足SLO。使用了一个有限制的凸优化求解器。

### Session 9: The Persistence Of Memory

#### KVell: the Design and Implementation of a Fast Persistent Key-Value Store

现代的块-可编址的NVMe SSD与原来的Random and Sequential Access相比，提供了更高的带宽以及相似的性能。早期的为当时的存储设备设计的可持久化的kv-store(KVs)，要么使用了log-structured merge，要么使用了B树，并没有完全利用新设备的特性。为了避免随机访问、对磁盘上有序数据的昂贵操作、以及同步的瓶颈而采用的逻辑，使得KVs在NVMe SSDs上受CPU的限制。

我们提出了一种新的可持久化KV设计方案。不像早期的设计，现在不再使用sequential access，以及数据存储在磁盘上时，不再有序。shared-nothing的哲学被应用以避免同步带来的额外开销。再加上batch device accesses，这些设计决定使得读写的性能和设备的带宽接近。最后，在内存中维护一个不昂贵的局部排序可以得到一个体面的扫描性能。我们在KVell中实现了这一设计，也是第一个利用现代NVMe SSD的最大带宽的可持久化KV存储。我们将KVell于最先进的基于LSM或B树的KVs进行比较，在人造benchmark与实际生产workload上。KVell性能更佳。

#### Recipe: Converting Concurrent DRAM Indexes to Persistent-Memory Indexes

我们提出了Recipe，一种将并行DRAM indexes转化成crash-consistent indexes的方法，为了持久化内存。主要的思路是，一种concurrent in-memory indexes提供的isolation，可以通过对crash-consistency进行小的更改来转换，当相同的索引在PM中被使用时。我们提出了一系列条件，使得这类DRAM indexes能够被识别，以及确保能够采取措施使得每项index被持久化。基于这些条件和转化动作，我们基于B+树，字典树，radix树，哈希表，修改了5种不同的DRAM indexes至他们对应的crash-consistent PM。修改量很小，约30-200行代码。经评估后性能更佳。

#### Performance and Protection in the ZoFS User-space NVM File System

作者首先提到了在用户态实现非易失性内存上面的文件系统的好处，包括了开发、移植和维护比起在内核实现更容易，性能也更高。但是现有的 NVM 文件系统都会把元数据的操作放在内核态，这是因为为了保证文件系统的完整性以及权限控制，而因为内核的参与，自然而然这样会使得性能下降。

这篇文章发现，大多数的文件及文件夹都拥有相同的权限设置，并且这些设置很少修改。基于这个发现，他们提出了一种叫做 Coffer 的抽象，把权限相同的文件及文件夹放在同一个 Coffer 里面，这样一来文件系统就变成了由 Coffer 组成的树。一个 Coffer 实际上是一堆具有相同访问权限的 NVM 页，它们的元数据是在一起维护的，这份元数据会映射到用户态，从而让 ZoFS 的用户态库能够操作这些元数据。

ZoFS 依赖于操作系统和硬件的分页机制来保证安全性。他们的内核态代码会做一些权限的检查，以及在把元数据映射到用户态的时候设置页表的权限。具体的还有很多细节问题，比如如果用户程序恶意修改映射到用户态的元数据等等，ZoFS 有相对应的保护措施，比如说用 PKRU 寄存器来实现更细粒度的页表权限控制等等。

#### SplitFS: Reducing Software Overhead in File Systems for Persistent Memory

这个工作的主要思想就是把元数据操作放在内核、把数据操作放在用户态，这是系统研究的一个经典思路。这篇文章的目标是提供可持久化内存上的一个高性能的文件系统，但同时又不想自己真的去写太多的文件系统的代码，于是他们内核态就使用了已有的 ext4-dax 文件系统，然后再额外地写少量的用户态代码就行了。

### Session 10: Making Things Faster

#### AutoMine: Harmonizing High-Level Abstraction and High Performance for Graph Mining

对图挖掘提出了一种新的算法，提供了方便编程的抽象和高性能支持。

#### KnightKing: A Fast Distributed Graph Random Walk Engine

图计算的新算法->性能提升。

#### Gerenuk: Thin Computation over Big Native Data Using Speculative Program Transformation

文章关注的是对象这一数据组织方式对于大数据系统的性能影响，并提出了Gerenuk这一使用高级语言直接访问和操作Native Data以提升性能的方式。

### Session 11: The Final Session

#### An Analysis of Performance Evolution of Linux's Core Operations

主要贡献是通过对Linux的核心内核操作（core kernel operations) 的详尽性能测试发现，Linux的核心内核操作的性能意外地随着时间的推移而变差。而导致Linux Kernel的基本OS操作变慢的主要原因可以分为以下不同的三类：安全性补丁、新的特性以及kernel配置上的变化。为此，作者提出了一个新的内核测试工具：LEBench，其包含了一系列有代表性的负载测试以及一个回归测试框架，其能够横向的对比测试不同版本的Linux的核心内核操作的性能差异。最后，作者列出了11个显著导致Linux的核心内核操作减缓的原因并避免或解决他们带来的性能影响。

#### ShortCut: Accelerating Mostly-Deterministic Code Regions

在应用中通常会进行大量的相似的计算，如：在流水线中有很多相似的行为；应用有相似的启动与初始化过程。当前系统使用记忆的方式（memoization）避免重复计算：其使用checkpoint来加速完全一致的、确定性的计算。但是目前的问题是：大部分实际代码都是不一致或者不确定的（由时钟、通讯、调度导致）。目前解决这个问题的方法是针对不同的程序使用定制化的优化，但是这些方式都比较难于开发，且代码复杂、容易产生错误、难以维护。因此作者希望有一种更普适性的方法应对这种场景。

本文提出的ShortCut方法是一种OS层的解决该问题的方案。ShortCut的目标是使用部分记忆(partial memoization)的方式加速近似确定性(mostly-deterministic)的计算。ShortCut的主要思路是将程序分为不同的片段，在该片段上提供参考执行(reference execution) ，并针对该程序片段其中的每个片段的输入（包括输入数据、内存状态、寄存器值等）、数据流与控制流作出假设。在之后的执行该程序片段过程时，通过验证这些假设，生成一个相对于参考执行的的修正，以在参考执行的结果基础上产生满足该次执行的正确的值。若假设失败，则回滚到普通的执行。

#### Scalable and Practical Locking with Shuffling

本工作主要是follow D. Dice在Eurosys 19'中的工作Compact NUMA-aware Lock（其提出的锁的简称为CNA），设计了一种锁的框架SHLFLock。该框架可以灵活的采纳各种优化策略、能够无缝的放入内核中。

### 参考链接：

[SOSP'19——SJTU-IPADS的集体见闻（Day-1）](https://mp.weixin.qq.com/s/IQbBD_RxbeecrlPUiKcLjQ)

[SOSP'19——SJTU-IPADS的集体见闻（Day-2）](https://mp.weixin.qq.com/s/s_P2FZf3uhuPlxliuSTQ0w)

[SOSP'19——SJTU-IPADS的集体见闻（Day-3）](https://mp.weixin.qq.com/s/59419dEiCRsO_MfyC_NNNQ)

[SOSP'19有哪些值得关注的论文？](https://www.zhihu.com/question/336446443/answer/872624113)