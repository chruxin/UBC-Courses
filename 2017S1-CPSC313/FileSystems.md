# File Systems

## Robustness and Recovery

### Journaling

|                   | Write-back Mode                          | Ordered Mode                             | Data Mode                                |
| ----------------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| What is journaled | Only write metadata operations           | Only write metadata operations           | Write both data and metadata             |
| Timeline          | journal: …; journal: add commit message;  write data to block 1234; | journal: …; write data to block 1234; journal: add commit message; | journal: …; journal: change data block 1234 to [INSERT DATA HERE]; journal: add commit message;  write data to block 1234; |

Example: create a file

Write-back mode:

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

Ordered mode:

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

Data mode:

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

