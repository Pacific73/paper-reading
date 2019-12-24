## Virtualization

[TOC]

### Hardware simulation

Create a procedure to simulate all hardwares that are needed to run an actual OS. For example, QEMU.

Advantage: Don't need to modify OS.

Disadvantage: All hardwares are simulated by software with bad performance.

### Full virtualization

VM's OS is isolated from low-level hardware. OS operations are captured by hypervisor and then get transformed into calling to hardware. For example, KVM.

Advantage: Don't need to modify OS. Good compatibility.

Disadvantage: Normal performance, especially I/O performance.

### Half virtualization

Similar to full virtualziation tech, using hypervisor to capture callings to low-level hardware. But in VM's OS, it integrates related code of half virtualization. That is to say, VM needs to know that itself is a VM to better collaborate with hypervisor with good performance. For example, XEN.

Advantage: Good performance.

Disadvantage: VM's OS needs to be modified.

### Hypervisor

Hypervisor captures upper-level system calls and transforms them into calls to low-level hardwares. And it supports coordinations between multiple OS.

### 2-phase commit

