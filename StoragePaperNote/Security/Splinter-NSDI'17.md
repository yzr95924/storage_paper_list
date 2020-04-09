---
typora-copy-images-to: ../paper_figure
---
Splinter: Practical Private Queries on Public Data
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| NSDI'17 | Function Secret Sharing |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation
Many online services let users query large public datasets.
> any user can query the data, and the datasets themselves are not sensitive.

Malicious servers can infer the user privacy via the user query. (privacy leakage)

- State-of-art limitations
Previous private query systems have generally not achieved practical performance.
> expensive cryptographic primitives and protocols.
> high computational cost

- Goal
This paper intends to design a system that protects user's queries on public datasets while achieving **practical performance for many current web applications**.

### Splinter
- Main idea:
The client splits each user query into shares and sends them to multiple providers.
> It then combines their results to the final answer.
> The user's query remains private as long as any one provider is honest.

![image-20191223162054823](../paper_figure/image-20191223162054823.png)

- Security goal
1. hide sensitive parameters in a user's query.
> query parameter and result

2. does not protect against providers returning incorrect results or maliciously modifying the dataset.

- Function Secret Sharing
a client divides a function $f$ into function shares $f_1,f_2,......,f_k$ so that multiple parties can help evaluate $f$ without learning certain of its parameters.

Practical FSS protocols only exists for **point** and **interval** functions.
> Additivity of FSS: low communication costs.

- Executing Splinter queries
  - Splinter client builds function shares.
  - Client sends the query with all the secret parameters removed to each provider, along with that provider's share of the condition function.
  - each provider divides its data into groups using grouping expressions.
  - the provider runs an evaluation protocol that depends on the aggregate function and on properties of the condition.

Splinter query algorithm for aggerates depends on condition type.
1. Condition Types and Classes
> 1. single-value conditions: can only be true on one combination of the values of $(e_1, ..., e_t)$ (*Equality-only conditions*) 
> 2. interval conditions: condition is true on an interval of the range values ()
> 3. Disjoint conditions: (*OR*)

2. Aggregate Evaluation
> 1. sum-based aggregates (*SUM* and *COUNT*)
> 2. MAX and MIN
> 3. TOPK


In MAX MIN, and TOPK,   

**Complexity Summary**:

![image-20191224214916387](../paper_figure/image-20191224214916387.png)

In all cases, the computation time is $O(nlogn)$ and the communication costs are much smaller than the size of the database.


- Optimized FSS Implementation
Splinter includes an FSS implementation optimized for modern hardware.

1. using one-way compression functions
Splinter uses the Matyas-Meyer-Oseas one-way function 
> for fixed key cipher 

Splinter initializes the cipher at the beginning of the query and reuses it for the rest of the query
> avoiding expensive AES initialization operations in the FSS protocol.

2. select the correct multi-party FSS protocol
Splinter proposes two different schemes
> offer different trade-offs between bandwidth and CPU usage.

**Multi-Party FSS with one-way functions**: the provider responds with all the records that match for a particular user-provided condition, and the client performs aggregation locally.
> provide the fastest response times on low-latency networks 
> it is not suitable for the bandwidth-sensitive environments

**Multi-Party FSS with Paillier**: has the same additive properties as the two-party FSS protocol. 
> the size of the function shares is independent of the number of parties.
> performance is slower since using Paillier cryptosystem. 
> it is useful for SUM and COUNT queries in bandwidth-sensitive setting.


### Implementation and Evaluation
- Implementation
Splinter in C++ using OpenSSL
> GMP: large integers 
> OpenMP: for multithreadings

1. FSS library: 2000 LoC
2. top application: 2000 LoC
3. test code: 1500 LoC

- Evaluation
  - Case studies: restaurant review site, flight search, map routing
  - Comparison to other private query systems 
  - FSS microbenmarks: Two-party FSS, Multi-party FSS.
  - Hosting cost: for the server-side computation cost in Amazon EC2.

The overall conclusion are three-folds:
> 1. it can support realistic applications
> 2. it can achieve end-to-end latencies below 1.6 sec
> 3. it can use up to 10 \* fewer round trips than prior systems.

## 2. Strength (Contributions of the paper)

1. First, this paper extends previous FSS protocols for point and interval functions with additive aggregates such MAX/MIN and TopK at low computational and communication cost.

2. Second, it also takes care of the implementation with optimized AES-NI instructions and multicore CPUs.


## 3. Weakness (Limitations of the paper)
1. In Splinter architecture, each provider needs to host a copy of the data. This introduces the overhead of maintaining data consistency.

2. Splinter cannot handle a large number of disjoint conditions, this is a place that can be considered to solve in the future.

3. For the aspect of the economic feasibility, Splinter disables the provider to mine user data, which maybe hard to be accepted in current providers.

4. Although this paper shows the Splinter can support many application in its evaluation part, it still can only support a subset of SQL. There still exists many unsupported queries, such as join operation. 

## 4. Some Insights (Future work)
- From this paper, we can learn that instead of using only one provider, using multiple provider can hide the privacy of user queries.

