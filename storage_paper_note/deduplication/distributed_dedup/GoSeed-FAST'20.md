---
typora-copy-images-to: ../paper_figure
---
GoSeed: Generating an Optimal Seeding Plan for Deduplicated Storage
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'20 | Deduplication Migration |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation
Data migration plans must consider the dependencies between files that are remapped to new volumes and files that are not.
> data migration: moving a portion of a physical volume's data to another volume
> typically performed for load balancing and resizing

*Migration plan*: an efficient migration plan will free the required space on the source volume while minimizing the space occupied on the target. 
> Previous studies propose greedy algorithms for determining the set of migrated files
> when migrating a file from one volume to another, this file's chunks that also belong to another file must be copied (deduplicated), rather than moved. (the chunks should reside on the same storage volume)


This paper bridges this gap for seeding and, consequently, space reclamation problems.

### GoSeed
- Main goal: 
space reclamation problems, formulation of seeding as an integer linear programming (ILP) problem.
> providing a theoretical framework for generating an optimal plan by minimizing the amount of data replicated. (NP-hard)

- Challenges
commercial optimizers can solve ILP with **hundreds of thousands ** of variables and constraints 
> (real-world case may consist of hundreds of millions of variables and constraints), may require long time to process these instances

How to do the tradeoff between runtime and optimality?
> make solving ILP more practical.

- GoSeed ILP optimization
1. Input:
> $M$: the physical data size moved from one volume to another (can have a slack factor)
> unique block set in the volume
> the file set: the mapping relationship between files and blocks 

2. objective:
$R$: the total size of the physical data that must be copied (replicated) as a result.
minimizing $R$

3. output: (Boolean variables)
the list of files that are remapped
the list of blocks that are replicated
the list of blocks that are migrated from 

the complexity depends on the deduplication ratio and on the pattern of data sharing between the files
> number of files and blocks


- GoSeed acceleration methods
The runtime for generating a migration plan for a source volume with several TBs of data would be unacceptably long.
> need the method for reducing this generation time

1. Solver timeout
The runtime of an ILP solver can be limited by specifying a timeout value, return the feasible solution before the timeout value.
> advantage: easy to process
> downside: cannot tell how far the suboptimal solution is from the optimal one. 

2. Fingerprint sampling
Using a sampling degree $k$, it includes in its sample all chunks whose fingperprint contains $k$ leading zeros, and all the files containing those chunks.
> reduce the size of the ILP instance by a predictable factor.

3. Container-based aggregation
Formulate the migration problem with containers
> coalesce chunks that are stored in the same container into a single block.

### Implementation and Evaluation
- Implementation
Using **Gurobi optimizer** as its ILP solver (C++ interface to define its problem instance)
> a wrapper program for converting a volume snapshot into an ILP instance in Gurobi 

- Evaluation
Two datasets: UBC and FSL
> treat a snapshot as a file in its scenario.

Comparison to existing approaches:
1. Rangoli
2. SGreedy (Sketch Volume)

Effect of ILP parameters
Effect of solver timeout
Effect of fingerprint sampling
Efficiency of container-based plans

## 2. Strength (Contributions of the paper)
1. This paper formulates the seeding problem as a ILP problem, and proposes three methods to accelerate the solving speed.
> solver timeout
> fingerprint sampling
> container aggregation


## 3. Weakness (Limitations of the paper)
1. Lack the part of how to use this ILP solver to build a real system


## 4. Some Insights (Future work)
1. This paper mentions providing these systems with a set of management functions is the next challenge in the design of deduplication systems.
> capacity management in deduplicated systems
> fast and effective data migration

2. It also mentions that most ILP solvers are based on the Simplex algorithm
> solves linear programming where the variables are not necessarily integers. 
> Then, search for an optimal integer solution, starting the search at the vicinity of the non-integer one.

This also can support the algorithm in TED.
