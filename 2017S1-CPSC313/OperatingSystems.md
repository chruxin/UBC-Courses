# Operating Systems

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

  ​				
  ​			
  ​		
  ​	