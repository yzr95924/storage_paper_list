---
typora-copy-images-to: paper_figure
---
AONT-RS: Blending Security and Performance in Dispersed Storage Systems
------------------------------------------
|  Venue  |       Category       |
| :-----: | :------------------: |
| FAST'11 | Secret Sharing |
[TOC]

## 1. Summary
### Motivation of this paper
This paper aims to modify the original Rabin's scheme that it can achieve improved computational performance, security and integrity.
> Main Idea: combining All-Or-Nothing Transform (AONT) with systematic RS erasure codes.

The main problem: how to provide security without securely storing encryption keys.
Related Work:
> Information dispersal algorithm (IDA)
> Secret Sharing Made Short (SSMS)

### AONT-RS
Aiming to provide the **computationally secure** secret sharing scheme.
- Enrich Rabin's IDA with two respects:
1. employ a variant of All-or-nothing Transform (AONT) as a **preprocessing** pass over the data.
> AONT: can be viewed as a (s+1, s+1) threshold scheme $\rightarrow$ data composed of $s$ words is encoded into $s+1$ different words
> original workflow of AONT: A random key $K$ is chosen, and each codeword $c_i$ is calculated as: $c_i = d_i \oplus E(K, i+1)$
> It adds a **canary** $d_s$, which is fixed value. (for checking integrity), then generates a hash $h$ of the $s+1$ codewords using a standard hashing function. Then calculate the final block $c_{s+1}$ as: $c_{s+1} = K \oplus h$. 

2. employ a **systematic** erasure code instead of non-systematic one. To improve the performance, because it eliminates the need to encode the first $k$ codewords. (as well as of the process of decoding)

- Encoding process:
![1552535525593](paper_figure/1552535525593.png)
**difference**: The hash value and random key are combined via bitwise exclusive-or to form a difference, which is append to the encrypted data to form the AONT package.

- Decoding process:
![1552545363095](paper_figure/1552545363095.png)
**Get the key**: first compute the hash $h$, then $(K \oplus h) \oplus h = K$ 


- Security Evaluation
AONT-RS has the property that unless one has all of the encrypted data, one cannot decode any of it.
> one needs all of the data to discover $K$. And one cannot decode any of the data without $K$.
> For the encoding function, it guarantees that enumeration is the only way to discover $K$'s value. $\rightarrow$ **computational security**.

### Implementation and Evaluation
- Using Cauchy Reed-Solomon coding for the multiplication and can improve performance significantly.
- Benchmark Performance
> 1. all benchmark are performed in using a **single thread**.
> 2. the time equals the sum of the time to encrypt plus the time to hash.
> > Fast AONT: SHA-256, MD5
> > Secure AONT: AES-256, RC4-128

![1552878709205](paper_figure/1552878709205.png)
Accesser: performs the **block-to-slice** encoding and decoding
- Clients had 10 Gbps NICs, servers had 1 Gbps NICs, bottleneck is **CPU**.
## 2. Strength (Contributions of the paper)
1. This paper provides a new dispersal algorithm which can provide security without the need for a separate key management system.
2. It also conducts the theoretical and actual performance analysis.
## 3. Weakness (Limitations of the paper)

## 4. Future Works
This paper does not mention how to choose the configuration parameter during the process so as to better policy decisions. This would depends on the characteristic of the targeted workload.