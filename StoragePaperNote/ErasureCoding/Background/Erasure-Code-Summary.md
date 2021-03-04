---
typora-copy-images-to: paper_figure
---
# Erasure Coding for Cloud Storage Systems: A Survey
@Survey @2013
[TOC]

## Summary
- The key question in cloud storage is: how do it store data reliably with a high efficiency in terms of both storage overhead and data integrity?
- This paper presents coding techniques into two categories:
>1. Regenerating codes: optimizing bandwidth consumption.
>2. Locally repairable codes: optimizing I/O overhead

- Regenerating codes saves bandwidth by not transferring data that are unnecessary to the particulat newcomer.
> Most instances of regenerating codes need to ask providers to send linear combinations of their data to the newcomer. It only saves the bandwidth but not disk I/O. Compared with conventional erasure coding, disk I/O will even probably be increased with regenerating codes.

### Erasure Coding and Its Performance Metrics
1. For $(n,k)$ Maximum Distance Separable (MDS) code, its storage efficiency is at best $\frac{k}{n}$
2. **Repair Bandwidth**: if encoding operations can be performed both on the newcomer and providers rather than on the newcomer only, **a much smaller** amount of data can be transferred.
3. **Repair I/O**: writing operations are performed only at the newcomer, and the amount of data written should equal the size of the coded block (**unaviodable**). So people really care is actually the amount of data read from disks of providers. Two ways to save disk I/O:
> a). obtian specific coded segments from providers,  and hence other coded segments in the same provider will not be encoded and will not be read.
> b). access specific storage nodes as providers, rather than accessing any $k$ storage nodes.

4. **Access Latency**: systematic codes which the original data can be embedded into code blocks, are able to maintian a higher storage efficiency than replcias while achieving a low access latency.
5. **Storage Efficiency**: storage efficiency denotes the ration of the amount of the original data to the actual amount of data stored on disks. (MDS achieves the best storage efficiency) 

### Tradeoff Between Storage $\alpha$ and Bandwidth $r$: Regenerating Codes
Two special cases of regenerating codes, which correspond to the minimum storage space required at storage nodes and the minimum total bandwidth consumption in the repair, respectively.
1. **Minimum-Storage Regenerating (MSR) Codes**: $(\alpha_{MSR}, r_{MSR})=(\frac{M}{k},\frac{Md}{k(d-k+1)})$
2. **Minimum-Bandwidth Regenerating (MBR) Codes**: $(\alpha_{MBR}, r_{MBR})=(\frac{2Md}{k(2d-k+1)},\frac{2Md}{k(2d-k+1)})$
3. To implement regenerating codes, the simplest way is to use **random linear coding** (significant computational complexity, not ensure that any $k$ code blocks are decodable). Thus, it is necessary to find explicit construction of regenerating codes.
4. **Repair-by-transfer regenerating codes**: with the repair-by-transfer property, the disk I/O overhead can be minimal since only data needed by the newcomer weill be read from the providers.

### Saving the Disk I/O Overhead: Locally Repairable Codes
Three representative families of the LRC:
>1. Hierarchical codes
>2. Self-repairing codes
>3. Simple regenerating codes

1. **Hierarchical Codes**: the repair degrees of coded blocks vary from 2 to $k$. An instance of large hierarchical codes can be constructed step by step from instances of smaller hierarchical codes.
2. **Self-repairing codes**: self-repairing codes can achieve a constant repair degree, independent of any specific missing block.
3. **Simple regenerating codes**: Though both hierarchical codes and self-repairing codes can achieve a low repair degree, their resiliences to the failures of storage nodes are **probabilistic**. The tolerance against failures and storage overhead of simple regenerating codes becomes predictable.
4. **Tradeoff between the failures tolerance and the repair degree**: It has been shown that none of the locally repairable codes can preserve the MDS property. There is the tradeoff between repair degree and the minimum distance.

### Trend in the research
1. the design objective gradually transfers from **data integrity** to **resource overhead**, from the **bandwidth resource** to some other scarcer resource for the cloud storage system, such as **computation** and **disk I/O overhead**.
2. The tradeoff between the repair degree and storage overhead has not been established clearly.
3. Given that the cloud storage system scales globally in multiple data centers, bandwidth, computation, and the corresponding geographical heterogeneities should be carefully discussed.







