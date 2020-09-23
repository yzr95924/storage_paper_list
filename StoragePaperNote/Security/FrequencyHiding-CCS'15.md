---
typora-copy-images-to: paper_figure
---
Frequency-Hiding Order-Preserving Encryption	
------------------------------------------
| Venue  |                       Category                       |
| :----: | :--------------------------------------------------: |
| CCS'15 | Frequency Encryption, Property Preserving Encryption |
[TOC]

## 1. Summary
### Motivation of this paper
This paper wants to do a new trade-off. It can clearly increase security while preserving the functionality for most queries relying on the order information. 

It needs to increase **client storage size** and introduce **a small error in some queries**.

- Application of order-preserving encryption
> Order-preserving encryption enables to perform range queries over an encrypted database without any changes to the database management system.

**Security notion**: indistinguishability under *frequency-analyzing*

### Frequency-Hiding Order-preseving Encryption
1. Main idea: present an order-preserving encryption scheme that is randomized.
> Repeated plaintexts will (or can) become different ciphertexts.

2. It also employs a number of data compression techniques to reduce the amount of information stored on the client.
> make this scheme somewhat practical while still improving the security of ciphertexts.



### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

## 4. Future Works
1. This paper mentions that if the ciphertexts can approximate a uniform distribution, it would improve against frequency-analyzing attacks.

2. Some related notions in this area
> Searchable encryption, homomorphic encryption, functional encryption