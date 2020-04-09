# A Solution to the Network Challenges of Data Recovery in Erasure-coded Distributed Storage Systems: A study on the Facebook Warehouse Cluster
@HotStorage'13 @Study on Data-center Network @Note
[TOC]

## Summary
***Background from Facebook's warehouse cluster***:
- The warehouse cluster at Facebook employs an RS code with parameters ($k=10, r=4$), thus resulting in a $1.4 \times$ storage requirement, as compared to $3 \times$ under conventional replication, for a similar level fo reliability.
- The most frequently accessed data is stored as 3 replicas, to allow for efficient scheduling of the map-reduce jobs.
- For the data which has not been accessed for more than three months is stored as a $(10,4)$ RS code.

***Data Recovery***
- In Facebook warehouse cluster, the median is more than 50 machines-unavailability events per day.
> This reasserts the necessity of redundancy in the data for reliability and availability.

- Number of missing blocks in a stripe:
> one block missing: 98.08% (most common)
> two blocks missing: 1.87%
> three or more blocks missing: 0.05%

- Cross-rack bandwidth consumed: 
> A median of more than 180TB of data is transferred through the TOR switches every day for RS-coded data recovery.