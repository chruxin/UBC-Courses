# Midterm Review

- [Midterm Review](#midterm-review)
  * [Sequential CPU](#sequential-cpu)
  * [Pipeline CPU](#pipeline-cpu)
  * [Memory Hierarchy](#memory-hierarchy)
    + [Cache Access](#cache-access)
    + [Cache Memory Organization](#cache-memory-organization)
    + [Direct-Mapped Cache](#direct-mapped-cache)
    + [Set Associative Caches](#set-associative-caches)
      - [Fully Associative Cache](#fully-associative-cache)
      - [Set Associative Cache](#set-associative-cache)
    + [Prefetching](#prefetching)
    + [Handling Writes](#handling-writes)
      - [Write Hit](#write-hit)
      - [Write Miss](#write-miss)
    + [Impact of Cache Parameters](#impact-of-cache-parameters)
  * [Memory Mountain](#memory-mountain)
  * [File Size Table](#file-size-table)

## Sequential CPU

### Y86-64 Instruction Set Architecture

#### Calling Conventions

- Stack pointer: 	%rsp
- Arguments: %rdi, %rsi, %rdx, %rcx, %r8, %r9
- Results: %rax
- Caller-save: %rax, %r10, %r11 + argument registers
- Callee-save: %rbx, %rbp, %r12, %r13, %r14, %r15

Assembly code snippet:

```assembly
xorq %rdx, %rdx        # %rdx = 0
mrmovq a(%rdx), %rax   # %rax = a
mrmovq b(%rdx), %rbx   # %rbx = b
```

## Pipeline CPU

### Data Flow Through Processor Stages

| Fetch  | Decode | Execute | Memory | Write back |
| ------ | ------ | ------- | ------ | ---------- |
| f.prPC | d.iCd  | e.iCd   | m.iCd  | w.iCd      |
|        | d.iFn  | e.iFn   | m.dstE | w.dstE     |
|        | d.rA   | e.valC  | m.dstM | w.dstM     |
|        | d.rB   | e.srcA  | m.valA | w.valE     |
|        | d.valC | e.srcB  | m.valE | w.valM     |
|        | d.valP | e.dstE  | m.bch  |            |
|        |        | e.dstM  |        |            |
|        |        | e.valA  |        |            |
|        |        | e.valB  |        |            |

### Instruction-Level Parallelism

Multiple in-flight (executing in the pipeline) instructions run partly at the same time, may even run out-of-order. However, the program must have the same output as if instructions were executed sequentially.

### Dependencies

#### Data Dependencies

- Casual: B reads a value written by A
- Output: B writes to a location written by A
- Anti/Alias: B writes to a location read by A

#### Control Dependencies



Number of bubbles:

Return: 3

Jump: 2

Relations:

Throughput = clock rate / CPI

Execution time = CPI * # of instructions / clock rate

## Memory Hierarchy

### Cache Access

- Cache Hit
- Cache Miss:
  - Cold/Compulsory miss: data not there in cache
  - Conflict miss: some other data is already there
  - Capacity miss: cache is too small to handle active cache blocks (working set)

### Cache Memory Organization 

**M**: maximum # of unique memory addresses

**m**: # of bits per memory address

***M = 2^m***

**S**: # of sets

**s**: # of set index bits

***S = 2^s*** (therefore must be power of 2)

**B**: Block size (# of bytes)

**b**: # of block offset bits

***B = 2^b*** (therefore must be power of 2)

**E**: # of lines per set (cache associativity)

**C**: Cache size (# of bytes)

***C = S x B x E***

**t**: # of tag bits

***m = t + s + b***

### Direct-Mapped Cache

Caches with exactly one line per set (*E = 1*).

Process:

- Set selection: set index (the value that the middle *s* bits hold) is what we use to select a set.
- Line matching: a copy of the data we are looking for (*w*) is contained in the line if and only if the valid bit is set to 1 and the tag in the cache line matches the tag in the address of *w*.
- Word selection on hits: once we have a hit, we use block offset (the value that the last *b* bits holds) to know what is the starting byte.
- Line replacement on misses: replacement policy is the current line is replaced by the newly fetched line.

### Set Associative Caches

- Fully-associative
- E (or N)-way associative

#### Fully Associative Cache 

A fully associative cache consists of a single set (*E = C/B*) that contains all of the cache lines. Item can be stored anywhere and only the tag is used for identifying a block. Therefore, there are no set index bits, and memory addresses are partitioned into only a tag and a block offset.

Process:

- Set selection: only one set
- Line matching: cache must search each line in the set in parallel for a valid line whose tag matches the tag in the address
- Word selection on hits: block offset selects a word from the block
- Line replacement on misses: replacement policy is the current line is replaced by the newly fetched line.

Because the cache circuitry must search for many matching tags in parallel, it
is difficult and expensive to build an associative cache that is both large and fast.
As a result, fully associative caches are only appropriate for small caches.

#### Set Associative Cache

A cache with *1 < E < C/B* is often called an *E*-way set associative cache.

Process:

- Set selection: identical to direct-mapped cache - set index bits identify the set
- Line matching and word selection: same as fully associative for each set
- Line replacement on misses: least frequently used (LFU), least recently used (LRU)

### Prefetching

Fetch data into cache before it is accessed.

### Handling Writes

#### Write Hit

The location being written to is in the cache.

Options:

- Write-through: write the value back to the next level immediately
  - Memories are consistent but writes become slower
- Write-back: defer write to next level until cache line is replaced
  - Faster but inconsistent, and cache misses become more expensive because we need to write the old data back before throwing it out

#### Write Miss

Block is not in the cache.

Options:

- No-write-allocate: write directly to the next lower level, ignoring the cache
- Write-allocate: loads the corresponding block from the next lower level into the cache and then updates the cache block.

Note: write-through is used with no-write-allocate, write-back is used with write-allocate.

### Impact of Cache Parameters

- Cache size and hierarchy depth: bigger caches have higher hit rates, so better performance, but they are also slower.
- Block size: larger block sizes increase spatial locality (hit rates) but decrease temporal locality (fewer lines fit in the cache).
- Associativity (choice of *E*): higher associativity decreases the # of misses, but it's also slower.
- Write strategy: In general, caches further down the hierarchy are more likely to use write-back than write-through.

## Memory Mountain

Information that *can* be discovered by a memory mountain:

- block size: where the slopes of spatial locality “flatten out”.
- cache size: the ridges of temporal locality

Information that *cannot* be discovered by a memory mountain:

- replacement policy
- associativity
- behaviours for `write` operations: write-through vs write-back, write-allocate vs no-write-allocate

## File Size Table

| Data size     | Number of bits |
| ------------- | -------------- |
| Bit           | 1              |
| Nibble        | 4              |
| Byte          | 8              |
| Kilobyte (kB) | 2^10           |
| Megabyte (MB) | 2^20           |
| Gigabyte (GB) | 2^30           |