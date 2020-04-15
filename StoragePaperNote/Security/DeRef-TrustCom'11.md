---
typora-copy-images-to: ../paper_figure
---
A Privacy-Preserving Defense Mechanism Against Request Forgery Attacks
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| TrustCom'11 | Network Security |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation 
This paper intends to defend against a general class of cross-site and same-site request forgery attacks.
> an attacker's website triggers a client's browser to send an HTTP request to a target website. 
> If the HTTP request carries the client credentials, then the attacker can perform actions on the website using the client's privileges, without the client be notified.

- The limitation of current countermeasure
A website can configure the scopes that are legitimate to initiate or receive sensitive requests that contain authentication credentials.
> scopes: a combination of the protocol, domain, and path.

the shortcoming of existing fine-grained access control approaches is *the policy file carries sensitive scope information* in **plain format**.

### DeRef

- Goal
Not only protect a browser from revealing the URLs from which it initiates requests, but also protect a website from revealing how it configures the legitimate scope
> the browser and website to exchange sensitive scope information while they may not need to fully trust each other.

1. Detecting forged requests
2. Fine-grained access control
3. Privacy-preserving checking
4. Feasible deployment


- Key idea
DeRef uses two-phase checking to make a trade-off between performance and privacy protection in real deployment. 

- Two-phase Privacy-Preserving checking (hash checking, blind checking)
Allow the *browser* and *the website* to exchange information in a privacy-preserving manner.
For example, suppose the website configures $L$ legitimate scopes in an ACL
> denoted by $x_i$, where $i=1,2,...,L$. 

If the browser initiates a request to the website from URL $y$, then it checks if $y$ belongs to any of the $x_i$
> the browser derives **all possible** scopes for a given URL $y$ into $y_1, y_2, ...,y_m$

**Requirements**:
1. the browser does not reveal $y$ to the website
2. the browser does not know the $x_i$'s configured by the website, unless a scope of $y$ matches any of these.

For (1):
the website send the browser a list of $k$-bit hashes of the configured scopes,
> $h(s,x_1)...h(s, x_L)$, $s$ is a random salt that is sent alongside the hash list.

The browser also initiates a request from URL $y$. it computes $h(s, y_i), i \in [1, m]$ and checks if it matches any $h(s, x_i)$
> does not reveal $y$ to the website.

if $k$ is small, then the browser cannot surely tell if a $x_i$ is being configured.

For (2):
Use the potentially matched scopes returned by hash checking as inputs, and conduct blind checking
> follow the blind-RSA, and send the blinded hash of $y_i$ to the website
> the website signs and returns the hash of blinded hash 

- Rationale 
In blind checking, the browser needs to take a round trip to send every potentially matched scope to the website and have the website sign the scope.
> high computation overhead.

It introduces hash checking to ignore any scopes that are guaranteed to be not configured
> reduce the overhead of blind checking.

- Putting all things together
1. Start-up: get the same base URL.
2. Downloading the policy file
3. Checking process: two-phase checking


### Implementation and Evaluation
- Evaluation
1. Browsing insensitive webpage
2. Browsing sensitive webpage
3. Browsing malicious webpage
4. Trade-off between performance and privacy
> tune the parameter $k$.

## 2. Strength (Contributions of the paper)
1. propose a practical privacy-preserving approach to defending against cross-site and same-site request forgery attacks.
> allow the browser and the website to exchange configuration information in a *privacy-preserving* manner.


## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
1. In this paper, it implements a fine-grained access control mechanism by storing the policy-based information in the *Bloom filter*.

2. In this paper, it needs to create privacy-preserving lists 
> The website should keep the ACLs private to browsers to avoid revealing its defense strategy.

3. In its two-phase checking, it uses hashing checking to reduce the overhead of blind hash checking which can be used in other issues.