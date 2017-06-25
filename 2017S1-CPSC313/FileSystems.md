# File Systems

## Hard Disks

### Rotating Hard Disks

**Blocks vs. sectors**

Sector is the smallest amount of data that the hardware will allow to be transferred to/from the disk.

 A block is the unit of size that a file system works with and it is some number of consecutive sectors. 

#### Average time to read data

*Average time = average seek time + average rotational latency + transfer time*

**Average seek time**:

Usually provided.

**Rotational latency**:

Time it takes for 1 rotation.

*1 / # of rotations per second*

RPS = Rotations Per Second

RPM = Rotations Per Minute

**Average rotational latency**:

*0.5 * (1 / # of rotations per second)*

**Transfer time**:

*(# of sectors to read / # of sectors per track) * rotational latency*

= *(# of sectors to read / # of sectors per track) * time it takes for 1 rotation*

**Example**:

Consider a hard disk with 4800 rotations per minute, 1000 sectors per track, 512 bytes per sector and an average seek time of 4ms. Calculate the average time needed to read:

- A block of 4KB (4096 bytes)

4800 RPM = (4800 / 60) RPS = 80 RPS

One rotation takes: 1 / (80 RPS) = 0.0125 s = 12.5 ms

Average rotational latency: 12.5 ms / 2 = 6.25 ms

4096 bytes / 512 bytes = 8 sectors to read

Transfer time: (8 / 1000) * 12.5 ms = 0.1 ms

Time in total: 4 ms + 5.25 ms + 0.1 ms = 10.35 ms

- An 80KB file where all data is found in contiguous 4KB blocks along a track

4 ms + 6.25 ms + (20 * 0.1 ms) = 12.25 ms

- An 80KB file where all data is found in random (fragmented) 4KB blocks spread out across the disk

20 * 10.35 ms = 207 ms

## Robustness and Recovery

### Journaling

|                       | Write-back Mode                          | Ordered Mode                             | Data Mode                                |
| --------------------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| **What is journaled** | Only write metadata operations           | Only write metadata operations           | Write both data and metadata             |
| **Timeline**          | journal: …; journal: add commit message;  write data to block 1234; | journal: …; write data to block 1234; journal: add commit message; | journal: …; journal: change data block 1234 to [INSERT DATA HERE]; journal: add commit message;  write data to block 1234; |

**Example: create a file**

**Write-back mode:**

- journal: mark block 1234 as used;
- journal: mark inode 123 as used;
- journal: set inode's data to (...) and set first block of 123 to 1234;
- journal: add entry to directory that contains it;
- journal: add commit message;
- write data to block 1234;
- mark block 1234 as used;
- mark inode 123 as used;
- set inode's data to (...) and set first block of 123 to 1234;
- add entry to directory that contains it;
- delete entry from journal.

**Ordered mode:**

- journal: mark block 1234 as used;
- journal: mark inode 123 as used;
- journal: set inode's data to (...) and set first block of 123 to 1234;
- journal: add entry to directory that contains it;
- *write data to block 1234;*
- *journal: add commit message;*
- mark block 1234 as used;
- mark inode 123 as used;
- set inode's data to (...) and set first block of 123 to 1234;
- add entry to directory that contains it;
- delete entry from journal.

**Data mode:**

- journal: mark block 1234 as used;
- journal: mark inode 123 as used;
- journal: set inode's data to (...) and set first block of 123 to 1234;
- journal: add entry to directory that contains it;
- *journal: change data block 1234 to [INSERT DATA HERE];*
- journal: add commit message;
- write data to block 1234;
- mark block 1234 as used;
- mark inode 123 as used;
- set inode's data to (...) and set first block of 123 to 1234;
- add entry to directory that contains it;
- delete entry from journal.

