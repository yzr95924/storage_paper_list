# Ceph: A Scalable, High-Performance Distributed File System

@Weil @OSDI'06 @Distributed File System

[TOC]

## 1. Summary
***The motivation of this paper***: Systems adopting object-based storage model suffer from scalability limitations due to little or no do distribution of the metadata workload. (reliance on allocation lists amd inode tables), which has limited scalability and performance, and increased the cost of reliability. 

This paper proposes a distributed file system that provides excellent performance and reliability while promising unparalleled scalability, called **Ceph**. It has three main components: the client, a cluster of OSDs (stores all data and metadata), a metadata server cluster (namespace).

***The key ideas of this paper***: 
**Decoupled Data and Metadata**: Ceph maximizes the separation of file metadata management from the storage of file data. It leverages CRUSH assigns objects to storage devices, instead of allocation lists. This allow any party to calculate the name and location of objects comprising a file's contents, eliminating the need to maintain and distributed object lists, and reducing the metadata cluster workload.
**Dynamic Distributed Metadata Management**: Ceph utilizes a novel metadata cluster architecture based on **Dynamic Subtree Partitioning** that adaptively and intelligently distributed responsibility for managing the file system directory hierarchy among many Metadata Servers (MDSs).
**Reliable Autonomic Distributed Object Storage**: Ceph delegates responsibility for data migration, replication, failure detection, and failure recovery to the cluster of OSDs that store the data.This approach allows Ceph to more effectively leverage **CPU and Memory** present on each OSD to achieve reliable highly available object storage with linear scaling.
## 2. Strength (Contributions of the paper)



## 3. Weakness (Limitations of the paper)

## 4. Future Work
