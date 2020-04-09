# SoftRing: Taming the Reactive Model for Software Defined Networks

@ICNP'2017 

[TOC]

## 1. Motivation

Switch's packet buffer is limited, besides, the reactive events triggers many exchanging messages between controller and switches, which bring heavier bandwidth load would congest the control/data channel. 

> the reactive events raised by such flexible model meanwhile consume lots of the bottleneck resources of the fast memory in switch and bandwidth between controller and switches.

## 2. Overview of this paper

**Main idea**

In order to solve the problems causes by the reactive events in SDN reactive model, instead of storing the reactive packets in a single switch or sending to the controller, it keeps them in the switches along the pre-computed waiting-loop paths.

**Challenges**

> 1. How to select waiting-loop paths that ensure reactive model without undue overhead?
> 2. How to enforce the path configurations in the switches?
> 3. How to handle the network dynamics and adjust the waiting-loop paths?

**Contributions**

1. This paper proposes softRing to eliminate the reactive overhead, which makes reactive packets forwarding along a pre-computed waiting-loop paths.

2. Two key techniques, loop seeking and loop selection, completing the waiting-loop path's computation.

**The Method**

1. Waiting-loop Paths Geneation

   - Tarjan's algorithm--find all biconnected components in network topology undirected graph.
   - RandomShuffle algorithm, DFS

2. Loop Selection Algorithm

   - Considering bandwidth cost on link *i* and the number of the extra flow entries, this paper employ a greedy heuristic algorithm to solve it.

3. Flow table's design
   - Send 128Bytes to Controller
   - Push VLAN field a tag as *LOOP_ID*
   - Forward the packet to the next hop of the pre-computed waiting-loop

4. Dynamic problem

   - Traffic load dynamics--A heavily loaded or congested link shouldn't be use for the loop paths

      method: the forbidden links.

   - Delay dynamics--The dynamics of tij and T will impact the process results.

      method: tij--Collecting satistics which queryed by controller link discover module, T--send probe packet to measure time.
## 3. Strength and Weekness

**The Strengths**

1. The proposed model-SoftRing is easy to deply and guarantee consistency(no packet's drop, congest).
2. A good effect---SoftRing reduced 5X control channel bandwidth consumption compared with OpenFlow.

**The Weekness**

Because of keeping packets along waiting-loop path, Softing slightly increases the processing delay of the reactive model.

## 4. Doubts

1. The details about *Virtual links* in waiting-loop path's seeking algorithm.
2. How to modify the sdn switch to support SoftRing's enforcement.



------

