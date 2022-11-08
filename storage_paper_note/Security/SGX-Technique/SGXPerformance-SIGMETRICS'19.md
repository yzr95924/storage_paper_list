---
typora-copy-images-to: ../paper_figure
---
Everything You Should Know About Intel SGX Performance on Virtualized Systems
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| SIGMETRICS'19 | SGX Performance |
[TOC]

## 1. Summary
### Motivation of this paper
- Motivation 
  - No work has studies in detail the **performance degradation** caused by SGX in virtualized systems.
    - this paper presents for the first time a detailed performance analysis of Intel SGX in a virtualized system 
    - compare with bare-metal system
  - identify several optimization strategies to improve performance of Intel SGX


### SGX Evaluation

1. Basis
- Intel SGX in a virtualized system 
  - For each VM started with SGX support, the hypervisor allocates a section of the EPC to use a virtual EPC.
    - For several VMs, the hardware EPC is partitioned and distributed to several VMs.
    - Using KVM in this paper

- Evaluation setting
  - For local machine, *kvm-sgx* and *qemu-sgx*
  - Ubuntu 16.04
  - For VM, it uses 32MB of EPC capacity.

2. ECall/OCall Performance 
- buffer in, out, in/out, user_check (zero-copy data transfer)
  - OCalls are generally faster than ECalls (10.6%-12.4)

3. Memory access

- write/read from the enclave vs. write/read from outside the application
  - outside application: write/read unencrypted memory
  - enclave: write/read unencrypted memory, write/read encrypted memory
- Result:
  - memory reads and writes into encrypted memory from the enclave is similar to performance without SGX
  - read/write into unencrypted memory from enclave is slightly slower (17%).

4. Swapping
- Evict process: 
  - mark the given pages 
  - no new TLB entries pointing to the page can be created
  - keep track of TLB entries concerning the page
  - OS must cause all processors executing inside the enclave to exit to flush these TLB entries
  - once all processors have exited from the enclave, the OS can write the sealed memory page to RAM
- Paging Measurement
  - multiple threads, accessing random addresses in a large memory buffer inside the enclave.

5. Enclave initialization and destruction
- Initialization: Before an enclave is ready to use, its memory contents are measured by CPU to produce a **cryptographic hash**
  - add a large static array into the enclave code and measured the enclave startup time
    - startup time vs. array size (varying from 0MB to 160MB)
    - it is measured by *RDTSCP* instruction.

- Destruction: large EPC also means more pages in the enclave's working set must be deallocated
  - leading to a slower shutdown


6. SGX-SSL benchmarks
- Measure the performance of encrypting memory buffers of various sizes from 16 bytes to 16MB using AES-128-GCM.
  - virtualized enclaves have *a small (less than 10%) performance penalty* over bare-metal enclaves.
  - the slowdown disappears with large buffer sizes. 



## 2. Strength (Contributions of the paper)
1. do the benchmark on SGX in both bare-metal and virtualization machines.
2. also propose a non-intrusive method for measuring the characteristics of a given workload to identify whether it is suitable with nested paging or shadow paging.

## 3. Weakness (Limitations of the paper)

## 4. Some Insights (Future work)
1. Techniques on trusted computing

- **Trusted platform module (TPM)**: used for platform integrity purpose by measuring critical system software (firmware, boot loaders, kernel, etc.) through the use of platform configuration registers
  - is not complete solution to trustworthy computing since the user still needs to trust many other components (e.g., OS, storage devices).

- **Intel SGX**: users no longer need to trust the system software nor any other hardware components. (self-contained solution)

- **Virtualization-friendly trusted computing**
  - AMD secure encrypted virtualization (SEV): a memory encryption-based technique:
    - protect virtual machines (VMs) from hypervisor attacks and hardware snooping.

  - **Microsoft shielded VM**
    - special VMs running on strictly controlled environments (e.g., guarded fabrics)
    - protected from snooping and alteration by the host administrator


2. SGX initialization

- At the boot time, the BIOS verifies whether SGX is enabled. It then reserves a region of physical memory for the CPU
  - designated as the *enclave page cache* (EPC).