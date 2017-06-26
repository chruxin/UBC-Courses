# Process Control

## Fork

Fork is a system call used in Unix-based systems to create a new process.

### After a call to fork

- The current process continues running as usual
- A new process with same state is created
- All process memory is "copied" into new process

### Returning value of fork 

The return value of fork distinguishes "parent" process from "child" process:

- 0: current process is the child
- positive: current process is the parent - value depends on the process ID of the child
- -1: fork failed, ned process was not created

Example:

```c
switch(fork()) {
  case -1:
  	perror("fork"); /* something went wrong */
  case 0:
  	printf("This is the child process!\n");
  /* … */
  default:
  	printf("This is the parent process!\n");
  /* … */
  wait(NULL);
}
```

### Page table after fork

During fork:

- Parent's page table is set to read-only
- Child receives new page table, same data as parent

When either process reads data, nothing happens as both processes can read from same memory space

When either process writes to memory:

- new page is allocated in physical memory
- data is copied from old page to new page
- child's page table is updated to new page
- both pages are set to write-enabled

## Signals

Messages that notify a process that an event has occurred.

Kernel notifies process of specific event:

- System event detected by kernel (e.g., fault)
- Another process requested a signal being sent (system
  call "kill")

Process can:

- terminate
- handle it through a pre-determined handler function
- Ignore the signal

