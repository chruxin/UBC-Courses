# Operating Systems

- [Operating Systems](#operating-systems)
	- [Processor/Control of Flow](#processorcontrol-of-flow)
	- [Exceptions](#exceptions)
		- [Types of Exceptions](#types-of-exceptions)
		- [Interrupts](#interrupts)
		- [Traps and System Calls](#traps-and-system-calls)
		- [Faults](#faults)
		- [Aborts](#aborts)
	- [Kernel Mode](#kernel-mode)
	- [Virtual Memory](#virtual-memory)
		- [Address Space](#address-space)
			- [Virtual Address Space](#virtual-address-space)
			- [Physical Address Space](#physical-address-space)
		- [VM for Caching](#vm-for-caching)
			- [DRAM Cache Organization](#dram-cache-organization)
			- [Page Tables](#page-tables)
				- [Page Table Entry (PTE)](#page-table-entry-pte)
		- [VM for Protection](#vm-for-protection)
			- [Page Faults](#page-faults)
		- [Address Translation](#address-translation)
			- [Translation Lookaside Buffer (TLB)](#translation-lookaside-buffer-tlb)
			- [Translation Process](#translation-process)
	- [Summary of Symbols](#summary-of-symbols)
		- [Basic parameters](#basic-parameters)
		- [Components of a virtual address (VA):](#components-of-a-virtual-address-va)
		- [Components of a physical address (PA):](#components-of-a-physical-address-pa)
		- [Others:](#others)

## Processor/Control of Flow

A sequence of control transfers is called the flow of control, or control flow, or the processor.

## Exceptions

At system boot time (when the computer is reset or powered on),

- the operating system allocates and initializes a jump table called an exception table, so that entry k contains the address of the handler for exception k.

At run time (when the system is executing some program),

- the processor detects that an event has occurred and determines the corresponding exception number k.
- The processor then triggers the exception by making an indirect procedure call through entry k of the exception table, to the corresponding handler.
- The exception number is an index into the exception table, whose starting address is contained in a special CPU register called the exception table base register.

### Types of Exceptions

| Class     | Cause                         | Async/sync | Return behavior                       |
| --------- | ----------------------------- | ---------- | ------------------------------------- |
| Interrupt | Signal from I/O device        | Async      | Always return to the next instruction |
| Trap      | Intentional exception         | Sync       | Always returns to next instruction    |
| Fault     | Potentially recoverable error | Sync       | Might return to current instruction   |
| Abort     | Nonrecoverable error          | Sync       | Never returns                         |

### Interrupts

Interrupts occur asynchronously as a result of signals from I/O devices that are external to the processor.

### Traps and System Calls

Traps are intentional exceptions that occur as a result of executing an instruction. The most important use of traps is to provide a procedure-like interface between user programs and the kernel, known as a system call.

Difference between a regular function call and system call:

- A regular function call runs in user mode, which restricts the types of instructions they can execute, and they access the same stack as the calling function.
- A system call runs in kernel mode, which allows it to execute privileged instructions and access a stack defined in the kernel.
- Interrupts occur asynchronously as a result of signals from I/O devices that are external to the processor.


### Faults

Faults result from error conditions that a handler might be able to correct. If the handler is able to correct the error condition, it returns control to the faulting instruction, thereby re-executing it. Otherwise, the handler returns to an abort routine in the kernel that terminates the application program that caused the fault.

### Aborts

Aborts result from unrecoverable fatal errors, typically hardware errors.

## Kernel Mode

What you can do:

- Change value of special registers, e.g. page table base register, exeption table base register
- Change behavior of interrupts
- Access memory using physical address
- Halt the processor

Talking to the I/O devices:

- I/O addresses are mapped to memory

   - Only kernel can access that memory

##Virtual Memory

### Address Space

Address space is an ordered set of nonnegative integer addresses:

{0, 1, 2, 3, ...}

Size of an address space is characterized by the number of bits that are needed to represent the largest address. e.g. Address spaces with N=2^n addresses is called an n-bit address space.

#### Virtual Address Space

In a system with virtual memory, the CPU generates virtual addresses from an address space of N=2^n addresses called the virtual address space:

{0, 1, 2, 3, …, N-1}

It's used by the suer process.

#### Physical Address Space

A system also has a physical address space that corresponds to the M bytes of physical memory in the system:

{0, 1, 2, 3, …, M-1}

It's used by the actual physical memory.

### VM for Caching

- Virtual memory is organized as an array of N contiguous byte-size cells stored on disk.
- Each byte has a unique virtual address that serves as an index into the array.
- The contents of the array on disk are cached in main memory.
- VM systems partition the virtual memory into fixed-size blocks called vrtual pages (VPs).
- Each VP is P=2^p bytes in size.
- Physical memory is partitioned into physical pages (PPs), also P bytes in size.
- Physical pages are also referred to as pages frames.

#### DRAM Cache Organization

SRAM: L1, L2, L3 cache memories

DRAM cache: VM system's cache that caches virtual pages in main memory

The penalty for a miss in DRAM is huge, therefore,

- virtual pages tend to be large,
- DRAM caches are fully associative (one single set that holds everything), i.e., any virtual page can be placed in any physical page,
- the replacement policies are much more complicated
- because of the large access time of disk, DRAM caches always use write-back instead of write-through

#### Page Tables

A page table is an array of page table entries (PTEs).

- Each page in the virtual address space has a PTE at a fixed offset in the page table.
- Page table is stored in main memory
- Each process has a separate page table
- Base address stored in a special register: PTBR (page table base register)

##### Page Table Entry (PTE)

- Each PTE consists of a valid bit and an n-bit address field.
- The valid bit indicates whether the virtual page is currently cached in DRAM.
  - If it's set to 0: the address points to the start of the virtual page on disk if not null, the page is unallocated if the address field is null.
  - If it's set to 1: the address field indicates the start of the corresponding physical page in DRAM where the virtual page is cached.

### VM for Protection

A process can only access physical memory mapped by its page table.

#### Page Faults

In virtual memory parlance, a DRAM cache miss is known as a page fault.

Problems can be caused by:

- Accessing a page the process is not allowed to access
  - Usually a programming error, e.g. NULL pointer
  - Operating system will terminate the process, possible sav "core" (copy of memory contents) and issue a warning
- Reading/writing to page with no read/write permission
- Accessing SUP page in user mode
- Accessing unallocated page
- Accessing uncached page

When MMU detects a page fault:

- Fault (which is an exception) triggers the corresponding exception handler in kernel mode
- Kernel may fix problem (in this case re-execute the instruction after), or abort

### Address Translation

#### Translation Lookaside Buffer (TLB)

MMU uses a small, high-associativity cache only for virtual to physical address translation.

Structure:

- Each entry in TLB corresponds to one page
- Virtual page number is composed of set index and tag (similar to L1 cache)
- TLB entry has the same data as page table entry
- If the TLB has T=2^t sets, then the TLBI (TLB index) consists of the t least significant bits of the VPN, and the TLBT (TLB tag) consists of the remaining bits in the VPN.

| TLBT | TLB  | VPO  |
| ---- | ---- | ---- |
| VPN  | VPN  | VPO  |

#### Translation Process

High level overview:

- The n-bit virtual address has two components: a p-bit virtual page offset (VPO) and an (n-p)-bit virtual page number (VPN).
- The MMU uses the VPN to select the approppriate PTE.
- The corresponding physical address is the concatenation of the physical page number (PPN) from the page table entry and the VPO from the virtual address.
- The PPO and VPO are identical.

Detailed process when using a flat page table:

- Divide Virtual Address into Virtual Page Number (VPN) and Offset (VPO)
  - log(page size) = # of bits for VPO and PPO
- Check TLB for VPN, if miss:
  - Compute physical address of page table entry (PTE)
    - PTE physical address = PTBR + VPN * PTE_SIZE (e.g., 8)
  - Read PTE from memory using this address
- Check PTE valid bit, throw exception if invalid
- Read Page Frame Number (PFN) from PTE to compute Physical Address (PA)
  - PA = PFN * PAGE_SIZE + Offset
- Read/Write target PA from/to memory
  - Start with L1, then L2…

## Summary of Symbols

### Basic parameters

| Symbol | Description                              |
| ------ | ---------------------------------------- |
| N=2^n  | Number of addresses in virtual address space |
| M=2^m  | Number of addresses in physical address space |
| P=2^p  | Page size (bytes)                        |

### Components of a virtual address (VA):

| Symbol | Description                              |
| ------ | ---------------------------------------- |
| VPO    | Virtual page offset (bytes)              |
| VPN    | Virtual page number                      |
| TLBI   | TLB (translation lookaside buffer) index |
| TLBT   | TLB tag                                  |

### Components of a physical address (PA):

| Symbol | Description                  |
| ------ | ---------------------------- |
| PPO    | Physical page offset         |
| PPN    | Physical page number         |
| CO     | Byte offset with cache block |
| CI     | Cache index                  |
| CT     | Cache tag                    |

### Others:

| Symbol | Description              |
| ------ | ------------------------ |
| MMU    | Memory management unit   |
| PTE    | Page table entry         |
| PTBR   | Page table base register |
