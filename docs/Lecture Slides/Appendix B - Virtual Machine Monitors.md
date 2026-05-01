---
share_cop4600: "true"
site-folder: docs/Lecture Slides
---

## Introduction to Virtual Machine Monitors
---

**Virtual Machine Monitors (VMMs)**
- Also known as hypervisors
- Sits between operating systems and hardware
- Creates illusion that each OS controls the machine
- Manages multiple OS instances on single hardware
---
**Historical Context**
- Originally developed by IBM for mainframes
- Problem: Running different OS versions simultaneously
- Solution: Add virtualization layer (VMM)
- Recent revival led by Stanford researchers (Disco project)
---
**Why VMMs? Modern Use Cases**
- Resource utilization optimization 
	- Server consolidation
- Desktop virtualization
- Cross-platform testing
- Development environments
---
**Basic VMM Architecture**
```
Hardware
    ↑
VMM (Hypervisor)
    ↑
OS 1    OS 2    OS 3
 ↑       ↑       ↑
Apps    Apps    Apps
```
---
**CPU Virtualization**
- Uses limited direct execution
- *Machine switching* between VMs
- Similar to OS context switching
- Must save/restore entire machine state
---
**Privileged Operations**
- OS cannot directly execute privileged instructions
- VMM must intercept privileged operations
- Maintains control over hardware
- Example: System calls
---
**System Call Flow (Traditional)**
```
1. USER: Process executes trap instruction
2. HARDWARE: Switch to kernel mode
3. HARDWARE: Jump to OS trap handler
4. OS: Handle system call
5. OS: Return to user mode
```
---
**System Call Flow (Virtualized)**
```
1. USER: Process executes trap instruction
2. VMM: Intercept trap
3. VMM: Forward to OS trap handler
4. OS: Handle system call
5. VMM: Intercept return
6. VMM: Return to user mode
```
---
## Memory Virtualization and Advanced Topics
---
**Memory Virtualization Overview**
- Multiple layers of address translation
- Virtual → Physical → Machine addresses
- VMM maintains additional mapping tables
---
**Address Translation Hierarchy**
```
Virtual Address Space (Process)
        ↓
Physical Address Space (OS)
        ↓
Machine Address Space (VMM)
```
---
**TLB Management**
- Software-managed TLB systems
- VMM intercepts TLB misses
- Additional translation layer
- Performance implications
---
**TLB Miss Handling (Traditional)**
```
1. TLB miss occurs
2. Trap to OS
3. OS handles page table lookup
4. Update TLB
5. Resume execution
```
---
**TLB Miss Handling (Virtualized)**
```
1. TLB miss occurs
2. Trap to VMM
3. VMM forwards to OS
4. OS performs lookup
5. VMM intercepts TLB update
6. VMM installs correct mapping
7. Resume execution
```
---
**Performance Optimizations**
- VMM-level software TLB
- Shadow page tables
- Hardware virtualization support
- Para-virtualization techniques
---
**The Information Gap**
- VMM's limited knowledge of OS behavior
- Examples:
  * Idle loop detection
  * Page zeroing
  * Resource allocation
---
**Para-virtualization**
- Modified OS for better VMM integration
- Improved performance
- Reduced virtualization overhead
- Trade-off: Less transparency
---
**Modern Developments**
- Hardware support (Intel VT-x, AMD-V)
- I/O virtualization
- Memory overcommitment
- Hosted vs. bare-metal configurations
- Containers
---
**Summary**
- VMMs provide transparent OS multiplication
- Key challenges in CPU and memory virtualization
- Performance vs. transparency trade-offs
- Growing importance in modern computing
---
# Types of Virtualization
---
**Type 1 (Bare Metal)**
- Runs directly on hardware without a host OS
- Provides direct hardware access through a hypervisor
- Offers better performance and security
---
**Type 2 (Hosted)**
- Runs as an application on top of a host OS
- Hardware access is mediated through the host OS
- Generally slower than Type 1 but easier to use
---
## Paravirtualization Relationship

Paravirtualization represents a middle ground between full virtualization and emulation:
- Modifies guest OS to communicate directly with hypervisor
- Can work with both Type 1 and Type 2 hypervisors
- More efficient than full emulation but requires modified guests
---
## Performance Considerations

The performance hierarchy from fastest to slowest is:
1. Virtualization with direct hardware access (Type 1)
2. Paravirtualization
3. Hosted virtualization (Type 2)
4. Full emulation

This difference exists because emulation must translate every instruction between architectures, while virtualization can use the native hardware directly or with minimal translation

---
# Examples
---
## Type 1 Virtualization
- **Data Centers**: VMware ESXi running multiple enterprise servers on bare metal
- **Cloud Providers**: Microsoft Hyper-V powering Azure's infrastructure (Note: WSL is actually Hyper-V!)
- **Enterprise Servers**: Proxmox managing multiple business applications
---
## Type 2 Virtualization
- **Development Environments**: VirtualBox running test environments on a developer's laptop
- **Software Testing**: VMware Workstation running multiple OS versions for compatibility testing
- **Home Labs**: Running Windows and Linux simultaneously on a personal computer
---
## Paravirtualization Examples
- **Cloud Services**: Amazon Web Services using Xen-based paravirtualization for EC2 instances
- **Linux Systems**: KVM/QEMU implementing paravirtualization for improved performance
- **Enterprise Virtualization**: Xen hypervisor managing resource-intensive workloads
---
## Emulation Examples
- **Legacy Systems**: Emulating old MRI scanner control computers
- **Mobile Development**: Android Studio's emulator for app testing
- **Game Development**: Nintendo Switch emulators for debugging
- **Industrial Applications**: Emulating specialized microcontrollers for embedded systems testing
---
## Real-World Hybrid Implementations
- **Modern ARM Macs**: Using both virtualization and emulation to run x86 Windows applications
- **Network Testing**: GNS3 using both emulation and virtualization for network simulation
- **Development Environments**: Using emulated mobile devices within virtualized development environments
---
The choice between these technologies often depends on specific needs:
- Use emulation when you need to run software on incompatible hardware architectures
- Use Type 1 virtualization for production environments requiring maximum performance
- Use Type 2 virtualization for development and testing scenarios
- Use paravirtualization when modified guest OS support is available and performance is critical

---
## Virtualization vs Emulation
---
**Core Differences**
- Virtualization partitions existing hardware resources and allows direct hardware access
- Emulation completely mimics one system's hardware on another, requiring full translation of instructions
- Virtualization is generally faster since it doesn't need to translate between architectures

