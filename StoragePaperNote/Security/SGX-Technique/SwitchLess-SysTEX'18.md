---
typora-copy-images-to: ../paper_figure
---
Switchless Calls Made Practical in Intel SGX
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| SysTEX'18 | SGX-Technique |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - One primary performance overhead is `enclave switches`, which are expensive and can be triggered frequently by cross-enclave function calls.
    - the overhead of an ECall and OCall is over 8000 CPU cycles, which is > 50x more expensive than that of a system call.
    - Previous works propose switchless calls, avoids enclave switches by using **worker** threads to execute function calls **asynchronously**.
  - It argues this technique is questionable in terms of efficiency
    - It is always wise to trade extra CPU cores for reduced enclave switches?
- Existing work
  - Switchless calls: caller thread send the request of ECalls/OCalls into **shared, untrusted buffers**, from which the requests are received and processed asynchronously by worker threads.
    - thus requiring extra CPU cores
  - is questionable in face of diverse and dynamic workloads encountered by real-world applications

### Switchless calls

- Main idea:
  - trade as few as extra CPU cores as possible for as many reduced enclave switches as possible
    - under different usage patterns and changing runtime workloads
  - determine on what conditions can switchless calls improve the performance efficiently
  - insights:
    - ECall/OCalls should be executed as Switchless Calls if they are `short and called frequently`

- Performance model
  
  - assume the implementation of switchless calls adopts the `busy-wait` approach
    - the caller thread must wait for the response in **a busy loop**
    - pushing requests to a shared queue
  - model
    - the time spent inside the enclave $T_t$
    - the time spent outside the enclave $T_u$
    - the time to do an enclave switch $T_{es}$
    - insights:
      - busy-wait switchless OCalls outperform the traditional OCalls if and only if the $T_t + T_u \leq T_{es}$
  
- Efficiency-based worker scheduling algorithm 

  - strike a good balance between performance speedup and power conservation
  - determine, at any point in time, the optimal number of workers so that the performance speedup of the callers is maximized while the wasted CPU cycles of the worker are minimized
  - worker efficiency
    - The CPU time saved by the worker / The CPU time consumed by the worker = $\frac{X \cdot T_{es}}{T}$
    - has a **positive linear relationship** with the throughput speedup
    - reflects the trade-off between the extra CPU cores and the reduced enclave switches
  - Algorithm:
    - maximizing the number of worker threads under the constraints of an upper bound on the number of worker threads and a lower bound on the average worker efficiency.
      - self-adaptive: determine the optimal number of worker threads at any point in time
      - user-configurable: make an explicit tradeoff between performance and energy conservation
      - negligible overhead: require **collecting a few basic statistics** at runtime, thus incurring virtually no runtime overhead
    - adjust the number of running worker threads by **sleeping or waking up threads**
      - current average worker efficiency < the expected worker efficiency 
        - sleep some threads
      - current average worker efficiency > the expected worker efficiency
        - wake up some threads

### Implementation and Evaluation
- Implementation (in Intel SGX SDK)
  - adopt `busy-wait` switchless approach 
  - maintain a fixed size thread pool for workers 
  - Easy to use: label in EDL file
  - Customizable worker management
    - support register callback function to handle worker events

- Evaluation
  - Static workloads
    - empty ECalls/OCalls
    - sgx_fwrite
  - Dynamic workloads

## 2. Strength (Contributions of the paper)

- the first work gives an in-depth performance analysis of switchless calls
- propose a self-adaptive worker scheduling algorithm to automatically determine the number of workers 
  - strike a good balance between performance and energy conservation

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)

- SGX background
  - SGX is considered a promising hardware-based isolation technology, especially for **protecting security-sensitive workloads** on public clouds.
  - Due to the high cost of enclave switches, it is problematic for **system-intensive** workloads.

