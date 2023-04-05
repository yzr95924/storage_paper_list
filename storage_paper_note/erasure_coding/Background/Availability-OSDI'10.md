---
typora-copy-images-to: paper_figure
---
# Availability in Globally Distributed Storage System
@OSDI'10 @Background
[TOC]

## Summary

***Availability***
**Part 1**
![1537856407481](paper_figure/1537856407481.png)
The vast majority of such unavailability events are transient and do not result in permanent data loss. Less than 10% of events last longer than 15 minutes.

- Two metrics throughout this paper:

1. **The average availability** of all $N$ nodes in a cell
$$
A_N = \frac{\sum_{N_i \in N}uptime(N_i)}{\sum_{N_i \in N}(uptime(N_i)+downtime(N_i))}
$$
$uptime(N_i)$ and $downtime(N_i)$ refer to the lengths of time a node $N_i$ is available or unavailable.

2. **Mean time to failure (MTTF)**: 
the measurements of availability      
$$
MTTF = \frac{uptime}{number \quad failures}
$$

**Failure Bursts**
This paper presents a simple time-window-based method to group failure events into failure bursts which, despite its simplicity, successfully identifies bursts with a common cause.

**Key findings from the fleetwide data**
1. Most unavailability periods are transient
2. Unavailability durations differ significantly by cause
> Median duration between 1 and 30 minutes depending on the cause

3. Correlated failures matter
> 37% of all failures of at least 15 minutes in duration in Google datacenters are part of a correlated failure burst.

4. Largest correlated failures share common failure domains
> Large failure bursts almost always have significant **rack-corelation**.

## Feedback and Recommendations
1. Determining the acceptable rate of successful transfers to battery power for individual machines upon a power outage.
2. Focusing on reducing reboot times, because planned kernel upgrades are major source of related failures.
3. Moving towards a dynamic delay before initiating recoveries, based on failure classification and recent history of failures on the cell.
