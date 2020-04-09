typora-copy-images-to: paper_figure

In Search of an Understandable Consensus Algorithm
------------------------------------------
| Venue  |      Category       |
| :----: | :-----------------: |
| ATC'14 | Consensus Algorithm |
[TOC]

## 1. Summary
### Motivation of this paper
Paxos is quite different ro understand. Its architecture requires complex changes to support practical system.
> Both system builders and students struggle with Paxos.

- Two drawbacks in Paxos
1. Paxos is exceptionally difficult to understand.
> why single-decree protocol works?

2. Paxos does not provide a good foundation for building practical implementations.

Paxos does not provide a good foundation either for system building or for education. This paper could provide a better foundation for system building and education. The primary goal is understandability.

### Raft
- Three key novel features
>1. strong leader: log entries only flow from the leader to other servers.
>> simplify the management of the replicated log
>2. leader election: Raft uses randomized timer to elect leaders
>> only add a small amount of mechanism to the heartbeats 
>
>3. membership changes: a new joint consensus approach, the majorities of two different configurations overlap during transitions.

- Replicated State Machine
It can be used to solve a varity of fault tolerance problems in distributed systems. Replicated state machines are typically inplemented using **a replicated log**.
> Each server stores a log containing a series of commands
> The state machines are deterministic

*Goal*: keeping the replicated log **consistent** is the job of the consensus algorithm.

- To provide better understandability:
1. problem decomposition
>a. leader election: the leader must be chosen when an existing leader fails
>b. log replication
>c. Safety

2. simplify the state space by reducing the number of states to consider.


- Leader Election
1. It uses a heartbeat mechanism to trigger leader election.
2. If a follower receives no communication over a period of time called the election timeout, then it assumes there is no viable leader and begins an election to choose a new leader.
3. A candidate wins an election if it receives votes from a majority of the severs in the full cluster for the same term.
> **Majority**: majority rule ensures that at most one candidate can win the election for a particular term.

![1554865684235](paper_figure/1554865684235.png)

4. To handle split votes?
> if many followers become candidates at the same time, votes could be split so that no candidate obtains a majority.
> Solution: use randomized election timeout to ensure that split votes are rare and that they are resolved quickly.

- Log Replication
1. The leader appends the command to its log as a new entry, then issues **AppendEntries** RPCs in parallel to each of the other servers to replicate the entry.
> If followers crash or run slowly, or if network packets are lost, the leader retries **AppendEntries** RPCs **indefinitely** (even after the the leader has responded to the client) until all followers eventually store all log entries.

2. The leader decides when it is safe to apply a log entry to the state machines (commited)
> A log entry has replicated it on a majority of the servers.
> the leader must find the latest log entry where the two logs agree, delete any entries in the follower's log after that point, and send the follower all of the leader's entries.

- Safety
1. Adding a restriction on which servers may be elected leader
> ensure that the leader for any given term contains all of the entries committed in previous terms.

2. Raft uses a simpler approach where it guarantees that all the committed entries from previous terms are present on each new leader from the moment of its election.
> log entries only flow in one direction, from leaders to followers, and leaders never overwrite existing entries in their log.

- Cluster Membership changes
Challenge:
> Not possible to do an atomic switch (change the membership of all servers at one)

1. use a two-phase approach:
> switch first to a transitional **joint consensus** configuration
> once the joint consensus has been committed, transition to the new configuration

### Implementation and Evaluation


## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Future Works
