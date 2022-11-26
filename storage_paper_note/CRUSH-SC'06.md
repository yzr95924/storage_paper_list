---
typora-copy-images-to: ../paper_figure
---
# CRUSH: Controlled, Scalable, Decentralized Placement of Replicated Data

|           Venue            |       Category       |
| :------------------------: | :------------------: |
| SC'06 | Ceph |
[TOC]

## 1. Summary
### Motivation of this paper

- distributing petabytes of data among tens or hundreds of thousands of storage devices
  - efficiently **map data objects to storage devices**
  - without relying on a **central** directory
- main problem
  - facilitate the **addition and removal of storage** while **minimizing unnecessary data movement**

### CRUSH

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

- object-based storage
  - unlike conventional block-based hard drives
  - object-based storage devices (**OSDs**) manage disk block allocation **internally**, exposing an interface that allows others to **read and write to variably-sized, named objects**
  - replace large block lists with **small object lists** and distributing the low-level block allocation problem
