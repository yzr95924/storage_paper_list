---
typora-copy-images-to: paper_figure
---
Secret Sharing Algorithm Summary
------------------------------------------

[TOC]

##  Summary
- Comparison of Secret Sharing Algorithms

  | Algorithm | Confidentiality Degree | Storage Blowup |
  | --------- | ---------------------- | -------------- |
  | SSSS      | $r=k-1$                | $n$            |
  | IDA       | $r=0$                  | $\frac{n}{k}$  |
  | RSSS      | $r \in [0, k-1]$             | $\frac{n}{k-r}$ |
  | SSMS | $r = k -1$ | $\frac{n}{k} + n \times \frac{S_{key}}{S_{sec}}$ |
  | AONT-RS | $r = k -1$ | $\frac{n}{k} + n \times \frac{S_{key}}{S_{sec}}$ |

Formally, a secret sharing algorithm is based on three parameters $(n, k, r)$ where $n > k > r \geq 0$, 
> $n, k$ define the fault tolerance degree of a secret. 
> $r$ defines the confidentiality degree of a secret.

Storage blowup: the ratio of of the total size of $n$ shares to the size of the original size of the original secret.

> the storage blowup must be at least $\frac{n}{k}$.'

## How to Share a Secret (SSSS)






