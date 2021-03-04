---
typora-copy-images-to: ../paper_figure
---
Enhanced Secure Thresholded Data Deduplication Scheme for Cloud Storage
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| TDSC'16 | Secure Deduplication |
[TOC]

## 1. Summary
### Motivation of this paper
Outsourced data may require different levels of protection, depending on how "popular" the datum is.
> this differentiation based on popularity also can allevuates the user's need to mannually sort the data as 
> common: deduplicated 
> potentially sensitive (non-deduplicate)

Duplicate occurs only when the file becomes popular (i.e., shared by many users)
> strictly confidential files that must never be deduplicated.

### Enhanced threshold-based deduplicated encryption
- Target scenario
the outsourced dataset contains **few instances of some data items** and **many instances of  others**.
> two backup types:
> 1. uploaded by many users: benefit strongly from deduplication
> 2. uploaded by one or very few users only: require confidentiality

- Multi-layered cryptosystem
1. the inner layer is applied using **convergent encryption**.
2. the outer layer is applied using **a semantically secure cryptosystem**.
![1561383159912](../paper_figure/1561383159912.png)

- System model
Add two trusted entities: identity provider and index repository service.
> 1. identity provider: users are identified when a user first joins the system.
> 2. index repository service: generate the file identity.
> ![1561385818759](../paper_figure/1561385818759.png)

- Security Model
The method of this paper achieves two kinds of security notions:
> 1. semantic security for unpopular data
> 2. conventional convergent security for popular data
> prevent the storage provider which is honest-but-curious.

- Popularity definition
1. a system-wide popularity limit
Represents the smallest number of **distinct, legitimate** users that need to upload a given file $F$ for that file to be declared popular.

2. A file shall be decleared popular
When at least $t$ uploads for this file have taken place.
> $t \geq p_{lim} + n_{A}$
> This paper just considers a single threshold $t$.

### Implementation and Evaluation
- Setting
Dataset 
> 1. pirate bay dataset: each torrent represents a file (file popularity)
> 2. Ubuntu popularity contest 

- Compare with three related work
Dupless, ClearBox, PAKE


1. Storage space reducation
This paper mentions the efficiency of its scheme depends on 
> 1. the value of $t$ (the tunable parameter of the scheme) 
> 2. the popularity distribution of files in the dataset

2. Overhead
> 1. Client computation
> 2. Server computation
> 3. Communication
> 4. Dropbox interact 

## 2. Strength (Contributions of the paper)
1. This paper proposes an enhanced threshold cryptosystem that leverage popularity and allows fine-grained trade-off between security and storage efficiency
> trade-off between security and storage efficiency

2. This paper also disscuss the overall security of the proposed scheme
> how to improve it?

## 3. Weakness (Limitations of the paper)
1. In this paper's model, it needs to trust the identity provider (IdP) and Index Repository Service (IRS)


## 4. Future Works
1. This paper mentions the case that how to prevent the attacker from guessing whether two ciphertexts *produced by the same user* corrspond to the same plaintext with a non-negligible advantage.

2. The first dataset used in this paper can indicate the file popularity.
> Pirate Bay dataset