---
typora-copy-images-to: ../paper_figure
---
The CASE of FEMU: Cheap, Accurate, Scalable and Extensible Flash Emulator
------------------------------------------
|           Venue            |       Category       |
| :------------------------: | :------------------: |
| FAST'18 | Block Storage |
[TOC]

## 1. Summary
### Motivation of this paper

- Motivation
  - SSD simulators (e.g., DiskSim's SSD model, FlashSim and SSDSim) only support internal-SSD research 
    - not kernel-level extensions
  - Hardware research platforms such as FPGA boards, OpenSSD, or OpenChannel SSD support full-stack software/hardware research
    - high costs (thousands of dollars per device) impair large-scale SSD research
  - Storage research community needs to have a new software-based emulator
    - FEMU: a QEMU-based flash emulator
      - cheap: open-sourced software
      - accurate: can be used a drop-in replacement for OpenChannel SSD (e.g., 0.5-38% variance), prototyping SSD-related kernel changes can be done without a real device
      - scalable: can scale to 32 IO threads and still achieve a low latency
      - extensible: support internal-SSD research (FEMU layer modification), kernel research (Guest OS modification on top of unmodified FEMU)

### FEMU

- FEMU is implemented in QEMU v2.9 and acts as a virtual block device to the Guest OS
  - with FEMU, the stack is {Application + Guest OS + FEMU}
- Scalability
  - All of the scalability bottlenecks of QEMU
    - QEMU uses a traditional trap-and-emulate method to emulate IOs
      - This "doorbell" is an MMIO operation, that will cause **an expensive VM-exit** ("world switch") from the Guest OS to QEMU
    - QEMU uses asynchronous IOs (AIO) to perform the actual read/write (byte transfer) to the backing image file
      - the AIO overhead becomes significant when the storage backend is a RAM-backed image
  - FEMU transfers QEMU from an interrupt to a polling-based design and disable the doorbell writes in the Guest OS.
    - 1 LOC commented out in the Linux NVMe driver
  - FEMU does not use virtual image file 
    - create its own RAM-backed storage in QEMU's heap space (with configurable size malloc())
- Accuracy
  - Delay emulation
    - When an IO arrives, FEMU will issue the DMA read/write command, then label the IO with an emulated completion time
      - add the I/O to its "endio queue", sorted based on IO completion time
  - Basic delay model
    - the basic queueing model represents a **single-register** and **uniform page latency** model
  - Advanced "OC" delay model
    - extend its model and achieve a more accurate delay emulation of OpenChannelSSD
    - OC uses double-register planes, every plane is built with two registers
- Usability and extensibility
  - white-box vs. black-box mode
    - a white-box device
      - the device **exposes physical page addresses and the FTL** is managed by the OS such as in Linux LightNVM
    - a black-box device
      - the **FTL resides inside FEMU** and only logical addresses are exposed to the OS
  - Page-level latency variability
    - FEMU supports **page-level latency variability**
    - High quality chips are mixed with lesser quality chips as long as the overall quality passes the standard

### Implementation and Evaluation

## 2. Strength (Contributions of the paper)

## 3. Weakness (Limitations of the paper)

- Limitations
  - FEMU is DRAM-backed, hence cannot emulate large-capacity SSDs

## 4. Some Insights (Future work)

- The rise of software-defined flash
  - host-managed flash ("software-defined" or "user-programmable") 
  - mostly done on top of expensive and hard-to-program FPGA platforms. A more affordable and simpler platform is available, OpenChannelSSD (managed by Linux-based LightNVM).
- The state of SSD research platforms
  - application layer
  - OS kernel
  - low-level SSD controller logic
- SSD simulators do not support running applications and operating systems
