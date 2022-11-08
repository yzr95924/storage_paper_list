---
typora-copy-images-to: paper_figure
---
Side Channels in Cloud Services, the Case of Deduplication in Cloud Storage
------------------------------------------
| Venue  |       Category       |
| :----: | :------------------: |
| S&P'10 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
This paper studies the privacy implications of cross-user deduplication. It shows that in different scenario, deduplication can be used as a covert channel by which malicious software can communicate with its command and control center.
> regardless of any firewall settings at the attacked machine.

### Side Channel Attacks and Defenses
- Two features of the deduplication services for attacks 
1. Source-based deduplication:
the client can observe whether a certain file or block was deduplicated.
> by examining the amount of data transferred over the network.

2. Cross-user deduplication: 
> each file or block is compared to the data of other users.

- It is easy to identigy whether a storage service is source-based and cross-user deduplication 
1. installing two clients with different accounts
> upload or download the popular files, and check whether it is re-transmitted. 
> e.g. Dropbox, Mozy, 

2. It is easy to test
> monitor network traffic and measure the amount of transmitted data.

3. Can query any information
> Did any user previously upload a copy of this file?

- Attack 1: Identifying files
identifying whether a specific file, known to the attacker, was previously uploaded to the storage service.

- Attack 2: learning the contents of files 
the attacker might apply Attack 1 to multiple version of the same file, performing a brute force attack over all possible values of the content of the file.
> this attack can be applied whenever the number of **possible version of the target file is moderate**.

- Attack 3: A Covert Channel
a malicious user can leverage the deduplication attack to establish a covert channel from the malicious software to a remote control center run by that malicious user.
> generate multiple versions of a file, and upload to the cloud storage, another client can use it to identify the transformed inforamtion.
> the covert channel can be used to transfer arbitrarily long messages by having the software save more than single file.

- Solution 1: Using encryption to stop deduplication
1. Instead of using a global key, stop cross-user deduplication, ensures the encryption method is not deterministic.
> using personal encryption key (costly to service provider, prevent usage of deduplication, key management issue)
> susceptible to offline dictionary attacks against that key. (key is generated from a small domain)

2. the services generate the encryption keys


- Solution 2: Performing deduplication at the servers
1. A major drawback: eliminates all bandwidth savings of deduplication, and service provide or user must pay for transferring the raw amount of data.
2. A trade-off between bandwidth and privacy
> small size files: always upload
> larger files: source-based deduplication 
> assume: sensetive data are always in small size files


- Solution 3: A Randomized Solution
1. weak the corelation between deduplication and the existence of files in the storage service.
> set a random threshold $T$: perform deduplication when the number of copies of the file exceeds this threshold. 
> Attacker: can test $T$ via uploading many copies of the file.

2. A truly random solution
> It is important to achieve that no one except for the server can compute $T$. And this can be achieve by using a private server.
> it can also use a secret key $s$, and computing the threshold as a function of the contents of the file. $T = F(X, s)$ 
> This solution can still adopt the sever-side deduplication 


## 2. Strength (Contributions of the paper)
1. This paper provides three kinds of attacks for online deduplication system which are very practical.
## 3. Weakness (Limitations of the paper)
1. For the analysis part of whether the occurrence of deduplcation adds any information about whether a file was uploaded to the server or not, it is not very easy to understand, I think maybe something wrong in this part.
2. No experiment to show the result in real case, just analysis.
## 4. Future Works
This paper shows that the usage of convergent encryption does not solve the security risks of data leakage.
> Since an adversary can still identify the occurence of deduplication.

**Importantance**: This paper also mentions that <u>it is possible to choose the threshold according to different distributions to minimize the cost while maximizing security.</u>