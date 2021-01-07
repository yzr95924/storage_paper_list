---
typora-copy-images-to: ./
---

#DDP: Distributed Network Updates in SDN

@ICDCS'18  @SDN Update
[TOC]


##1. Motivation

- Current update approaches heavilyh rely on the centralized controller to initiate and orchestrate the network updates, resulting in **long latency** of update completion.
> Quickly and consistently updating the distributed data plane poses a major and common challenge in SDN system 
> coordination of the distributed data plane requires <u>frequent communication between the controller and switches</u> (slow down the update completion time)

- Asynchronous communication channels: 
control messages are often received and executed by switches in an order different from the order sent by the controller.
> an inapproriate control order may violate the consistency properties on the data, resulting in network anomalies, e.g., blackholes, traffic loops and congestion.



##2. Overview of this paper

### **Main Idea** of this paper
DDP develops distributed coordination abilities at the data plane.
Each datapath operation container (DOC) is encoded with an individual operation and its dependency logic.
> the network update can be both triggered and executed in a fully local manner, further improving the update speed while maintaining the consistency.

**Insight**: Switches can coordinate with each other to orderly apply the operations, the update time as well asa the controller's processing load will be greatly reduced.

- Real-time update
the involved DOCs are sent to the data plane in one shot, and the switches can consistently execute them in a distributed manner.

- Updates directly triggered by local events
the controller prestores the DOCs at the data plane, and when corresponding events happen, the updates can be locally triggered and executed.

### **Challenges** of this paper


### Contributions of this paper

2. design novel algorithms to compute and optimize the primitive DOCs for consistent updates

3. implement the Distributed Datapath (DDP) system to evluate its performance in various update scenarios.

### The Method

1. Network Update Problem
The $C$ denote a network configuration state (a collection of exact match rules)
Network update is defined as: a transition of confirguration state from $C$ to $C^{'}=update(C,O,e)$
> $O=\{o\}$ is a set of datapath operations to implement the update, e.g., to insert/delete/modify a flow rule at a particular switch.
> $e$ is a local event at the data plane to trigger the update, e.g., a link/switch failure and link congestion.

2. DDP Design
- Operation Dependency Graph (ODG)
Introduce the concept of an **Operation Dependency Graph (ODG)** that captures the data plane dependencies
> 1. The dependency is **unidirectional** (no cycles in the graph)
> 2. ODG expresses and optimized result of the whole dependency relations.
> 3. connectivity is dispensable in the ODG
> 4. the ODGs are composable (multiple ODGs for different update events can be composed together)

- Datapath Operation Container (DOC) 
In DDP system, the SDN controller adopts DOCs to configure the data plane, rather than directly sending operations as in traditional SDN. 
> The switches then coordinate with each other to execute the update at right time.

- Execution Behaviors
>1. Push: DOC is executed 
>2. Pull: DOC is received at the data plane

Push and Pull are complementary to each other, and with the two behaviors, all operations will be  **consistently** applied in <u>a correct order</u>.

- Algorithm



### Implementation


### Experiment


### Related Work


