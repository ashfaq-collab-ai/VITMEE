# Operating Systems -- Learning Notes

## Contents

1.  Process Management
2.  Process States
3.  Process Control Block (PCB)
4.  Processes and Threads
5.  CPU Scheduling
6.  Process Synchronization
7.  Deadlocks
8.  Memory Management
9.  File Organization
10. Blocking and Buffering
11. File Descriptors
12. File and Directory Structures
13. Input/Output (I/O) Devices
14. Quick Revision and Interview Checklist

------------------------------------------------------------------------

# 1. Process Management

## What is a process?

A **process** is a program that is currently executing.

-   A program is a passive set of instructions stored on disk.
-   A process is an active instance of that program.
-   A single program can have multiple processes running at the same
    time.

Example: `chrome.exe` stored on disk is a program. When Chrome is
opened, the operating system creates a process.

## Responsibilities of process management

The operating system:

-   Creates and terminates processes.
-   Schedules processes on the CPU.
-   Suspends and resumes processes.
-   Provides communication between processes.
-   Synchronizes processes that share resources.
-   Handles deadlocks.
-   Tracks process information.

## Process creation and termination

In Unix/Linux, common process-related system calls include:

-   `fork()` -- creates a child process.
-   `exec()` -- replaces the current process image with another program.
-   `wait()` -- allows a parent process to wait for a child process.
-   `exit()` -- terminates a process.

Useful Linux commands:

``` bash
ps
ps aux
top
htop
pstree
kill PID
nice
renice
```

------------------------------------------------------------------------

# 2. Process States

A process moves through different states during its lifetime.

## Five-state process model

1.  **New** -- The process is being created.
2.  **Ready** -- The process is ready to execute and waiting for CPU
    time.
3.  **Running** -- The CPU is executing the process.
4.  **Waiting/Blocked** -- The process is waiting for an event, such as
    I/O completion.
5.  **Terminated** -- The process has finished or has been stopped.

Typical transitions:

-   New → Ready: admitted by the OS.
-   Ready → Running: selected by the CPU scheduler.
-   Running → Ready: preempted or its time slice expires.
-   Running → Waiting: requests I/O or waits for an event.
-   Waiting → Ready: the event or I/O operation completes.
-   Running → Terminated: execution finishes.

## Ready vs. waiting

  Ready                        Waiting/Blocked
  ---------------------------- -----------------------------------
  Waiting for CPU time         Waiting for an event or resource
  Can run when scheduled       Cannot run until the event occurs
  Usually in the ready queue   Usually in a waiting/device queue

## Linux process states

Linux tools may show states such as:

-   `R` -- Running or runnable.
-   `S` -- Interruptible sleep.
-   `D` -- Uninterruptible sleep, often waiting for I/O.
-   `T` -- Stopped or traced.
-   `Z` -- Zombie.

Useful commands:

``` bash
ps -l
ps aux
top
```

------------------------------------------------------------------------

# 3. Process Control Block (PCB)

A **Process Control Block (PCB)** is an operating-system data structure
that stores information needed to manage a process.

A PCB commonly contains:

  Field                    Purpose
  ------------------------ -------------------------------------
  PID                      Process identifier
  Process state            Current state
  Program counter          Address of the next instruction
  CPU registers            Saved CPU register values
  Scheduling information   Priority and scheduling data
  Memory information       Memory mappings and related details
  I/O status               Open files and I/O information
  Accounting information   CPU time and other usage data

## Why is the PCB important?

During a context switch, the OS saves the running process's state and
restores the state of another process. PCB information helps the OS
pause and later resume processes correctly.

On Linux, process information is represented through kernel data
structures, including `task_struct`.

Useful places to inspect process information:

``` bash
cat /proc/PID/status
cat /proc/PID/sched
ls -l /proc/PID/fd
```

Replace `PID` with the process ID.

------------------------------------------------------------------------

# 4. Processes and Threads

## Process

A process is an executing program with its own process resources and
address space.

## Thread

A **thread** is a unit of execution within a process. A process can
contain one or more threads.

Threads in the same process generally share:

-   Code.
-   Data and heap.
-   Process resources such as open files.

Each thread has its own:

-   Stack.
-   Program counter.
-   CPU registers.
-   Execution state.

## Process vs. thread

  -----------------------------------------------------------------------
  Process                             Thread
  ----------------------------------- -----------------------------------
  Has its own address space           Shares the process address space

  More resource-intensive to create   Usually lighter to create

  Communication often needs IPC       Can communicate through shared
                                      memory

  Failure is often isolated from      A faulty thread can affect its
  other processes                     whole process
  -----------------------------------------------------------------------

## Concurrency vs. parallelism

-   **Concurrency** -- Multiple tasks make progress during overlapping
    periods.
-   **Parallelism** -- Multiple tasks literally execute at the same time
    on different CPU cores or processing units.

## Java example

``` java
class MyTask extends Thread {
    public void run() {
        System.out.println("Thread is running");
    }
}

public class Main {
    public static void main(String[] args) {
        MyTask t = new MyTask();
        t.start();
    }
}
```

Important distinction:

-   `start()` asks the JVM to start a new thread.
-   `run()` is the task's method; calling it directly does not create a
    new thread.

------------------------------------------------------------------------

# 5. CPU Scheduling

CPU scheduling selects a process from the ready queue and gives it CPU
time.

## Scheduling goals

-   High CPU utilization.
-   High throughput.
-   Low waiting time.
-   Low turnaround time.
-   Low response time.
-   Fairness.

## Important calculations

Let:

-   AT = Arrival Time.
-   BT = Burst Time (CPU execution time).
-   CT = Completion Time.
-   TAT = Turnaround Time.
-   WT = Waiting Time.

Formulas:

``` text
Turnaround Time (TAT) = Completion Time (CT) - Arrival Time (AT)

Waiting Time (WT) = Turnaround Time (TAT) - Burst Time (BT)

Average Waiting Time = Sum of waiting times / Number of processes

Average Turnaround Time = Sum of turnaround times / Number of processes
```

## Preemptive vs. non-preemptive scheduling

-   **Non-preemptive:** Once a process gets the CPU, it keeps it until
    it finishes or blocks.
-   **Preemptive:** The OS can take the CPU away and schedule another
    process.

## Common scheduling algorithms

### First Come, First Served (FCFS)

Processes execute in arrival order.

Advantages: - Simple. - Easy to implement.

Disadvantages: - Can cause the convoy effect. - A long process can make
short processes wait.

Example, all arrival times are zero:

  Process     Burst time
  --------- ------------
  P1                   5
  P2                   3
  P3                   4

Order: `P1 → P2 → P3`

Waiting times: P1 = 0, P2 = 5, P3 = 8.

Average waiting time = `(0 + 5 + 8) / 3 = 4.33`.

### Shortest Job First (SJF)

Selects the available process with the shortest CPU burst.

-   Non-preemptive SJF runs the selected process until it finishes or
    blocks.
-   Its preemptive version is Shortest Remaining Time First (SRTF).

Advantages: - Can minimize average waiting time when burst times are
known.

Disadvantages: - Burst-time prediction is difficult. - Long processes
can starve.

### Priority Scheduling

The CPU is assigned to the highest-priority available process.

-   Priority can be preemptive or non-preemptive.
-   The meaning of the priority number depends on the system; in many
    textbook examples, a smaller number means higher priority.
-   **Starvation** can occur when low-priority processes wait
    indefinitely.
-   **Aging** gradually increases the priority of waiting processes to
    reduce starvation.

### Round Robin (RR)

Each process receives a fixed time quantum.

Example: quantum = 2 ms.

-   P1 runs for up to 2 ms.
-   P2 runs for up to 2 ms.
-   P3 runs for up to 2 ms.
-   Processes that are not finished return to the ready queue.

Advantages: - Fair CPU sharing. - Useful for time-sharing systems.

Disadvantages: - Very small quantum causes frequent context switches. -
Very large quantum makes it behave more like FCFS.

### Multilevel Queue Scheduling

The ready queue is divided into different queues, often for different
categories of processes. Each queue may use its own scheduling
algorithm.

------------------------------------------------------------------------

# 6. Process Synchronization

**Process synchronization** coordinates processes or threads that access
shared resources.

It prevents incorrect results caused by uncontrolled concurrent access.

## Race condition

A race condition occurs when the result depends on the unpredictable
timing or ordering of concurrent operations.

Example: Two threads both read a shared counter value of 5, both add 1,
and both write 6. The expected result of 7 is lost.

## Critical section

A critical section is a part of a program that accesses shared data or
resources and must be protected against unsafe concurrent access.

A correct critical-section solution aims for:

1.  Mutual exclusion.
2.  Progress.
3.  Bounded waiting.

## Mutex

A **mutex** is a mutual-exclusion lock. Only one thread can hold it at a
time.

Java example:

``` java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}
```

The `synchronized` keyword protects the method using the object's
monitor lock.

## Semaphore

A semaphore is a synchronization mechanism that maintains a counter.

-   Binary semaphore: commonly used to represent two states,
    available/unavailable.
-   Counting semaphore: allows a specified number of concurrent
    accesses.

Java example:

``` java
import java.util.concurrent.Semaphore;

class Example {
    private final Semaphore semaphore = new Semaphore(2);

    void useResource() throws InterruptedException {
        semaphore.acquire();
        try {
            System.out.println("Using resource");
        } finally {
            semaphore.release();
        }
    }
}
```

## Mutex vs. semaphore

  -----------------------------------------------------------------------
  Mutex                               Semaphore
  ----------------------------------- -----------------------------------
  Provides mutual exclusion           Controls access to a number of
                                      resources

  Usually has ownership               Generally does not have mutex-style
                                      ownership

  Typically allows one holder         Can allow multiple permits
  -----------------------------------------------------------------------

## Monitor

A monitor combines shared data and operations with synchronization.
Java's `synchronized`, `wait()`, `notify()`, and `notifyAll()` are
related to monitor-based synchronization.

------------------------------------------------------------------------

# 7. Deadlocks

A **deadlock** occurs when a set of processes or threads waits
indefinitely because each is waiting for a resource held by another.

Example:

-   P1 holds Resource A and waits for Resource B.
-   P2 holds Resource B and waits for Resource A.
-   Neither can proceed.

## Four necessary conditions for deadlock

All four conditions must hold simultaneously for a deadlock to occur:

1.  **Mutual exclusion** -- At least one resource cannot be shared.
2.  **Hold and wait** -- A process holds a resource while waiting for
    another.
3.  **No preemption** -- Resources cannot simply be taken away; they
    must be released voluntarily.
4.  **Circular wait** -- A circular chain of processes exists, each
    waiting for a resource held by the next.

## Resource Allocation Graph

-   Process: circle.
-   Resource: square.
-   Process → Resource: request edge.
-   Resource → Process: allocation edge.

A cycle means deadlock if every resource type in the graph has only one
instance. With multiple instances, a cycle indicates the possibility of
deadlock, not necessarily deadlock.

## Deadlock handling strategies

-   **Prevention:** Design the system so at least one necessary
    condition cannot hold.
-   **Avoidance:** Allocate resources only when the system remains in a
    safe state. Banker's Algorithm is a classic example.
-   **Detection:** Allow deadlocks, then detect them.
-   **Recovery:** Terminate processes, preempt resources where possible,
    or roll back work.

## Deadlock vs. starvation

-   Deadlock: a group of processes is stuck waiting for one another.
-   Starvation: a process waits indefinitely because other processes
    keep getting preference.

## Java deadlock prevention tip

When multiple locks are needed, acquire them in a consistent global
order. This helps prevent circular wait.

------------------------------------------------------------------------

# 8. Memory Management

Memory management is the OS function responsible for tracking,
allocating, protecting, and freeing memory.

## Memory hierarchy

A simplified hierarchy:

1.  CPU registers.
2.  Cache.
3.  Main memory (RAM).
4.  Secondary storage (SSD/HDD).

Generally, registers and cache are faster and smaller than RAM, while
SSD/HDD storage is slower but offers much greater capacity.

## Logical and physical addresses

-   **Logical/virtual address:** Address generated by a process.
-   **Physical address:** Actual location in RAM.
-   **MMU (Memory Management Unit):** Hardware that translates virtual
    addresses to physical addresses and helps enforce memory protection.

## Contiguous memory allocation

A process is allocated one continuous region of memory.

Types: - Fixed partitioning. - Variable partitioning.

Fragmentation: - **Internal fragmentation:** Wasted space inside an
allocated block. - **External fragmentation:** Free memory exists, but
it is split into small non-contiguous pieces.

## Paging

Paging divides:

-   Virtual memory into fixed-size pages.
-   Physical memory into equal-size frames.

A page table maps virtual pages to physical frames.

Advantages: - Avoids external fragmentation in physical-memory
allocation. - Supports virtual memory.

Disadvantage: - Can cause internal fragmentation, especially in the
final page.

A **TLB (Translation Lookaside Buffer)** is a cache that stores recent
address translations.

## Segmentation

Segmentation divides a program into variable-sized logical units, such
as:

-   Code.
-   Data.
-   Stack.
-   Heap.

  -----------------------------------------------------------------------
  Paging                              Segmentation
  ----------------------------------- -----------------------------------
  Fixed-size units                    Variable-size units

  Pages and frames                    Segments

  Mainly physical-memory management   Reflects logical program divisions
  mechanism                           

  Can cause internal fragmentation    Can cause external fragmentation
  -----------------------------------------------------------------------

## Virtual memory

Virtual memory gives each process a virtual address space and allows the
OS to use secondary storage as backing for memory pages that are not
currently resident in RAM.

### Demand paging

Pages are loaded into RAM when needed.

A **page fault** occurs when a process accesses a page that is not
currently mapped as present in physical memory. The OS checks whether
the access is valid and, if appropriate, loads the page.

### Page replacement algorithms

-   FIFO -- Replaces the page that entered memory first.
-   LRU -- Replaces the page that has not been used for the longest
    time.
-   Optimal -- Replaces the page whose next use is farthest in the
    future; mainly a theoretical benchmark.

### Thrashing

Thrashing occurs when the system spends too much time handling page
faults and moving pages rather than doing useful work.

Possible mitigations include reducing the degree of multiprogramming or
providing more memory.

## Linux memory commands

``` bash
free -h
top
htop
vmstat
cat /proc/meminfo
ps aux
```

## Java memory overview

Common JVM memory areas include:

-   Heap -- Where objects and arrays are allocated.
-   Thread stacks -- Hold stack frames for method calls and local
    execution state.
-   Metaspace -- Stores class metadata in modern HotSpot JVMs.
-   Program counter and native method stack -- Per-thread execution
    support.

Garbage collection reclaims eligible heap objects.

------------------------------------------------------------------------

# 9. File Organization

A **file** is a named collection of related data stored on a storage
device.

A file system manages files, directories, metadata, and storage
allocation.

## File operations

Common operations include:

-   Create.
-   Open.
-   Read.
-   Write.
-   Append.
-   Close.
-   Rename.
-   Delete.
-   Truncate.

## Record organization methods

These describe how records within a file are logically organized.

### Sequential organization

Records are stored and typically processed in sequence.

-   Simple.
-   Suitable for sequential processing.
-   Searching may require scanning many records.

### Direct or random organization

Records can be accessed directly using a calculated or known location.

-   Fast access when the location is known.
-   Requires a suitable record-addressing scheme.

### Indexed organization

An index stores keys and pointers/locations for records.

-   Helps locate records quickly.
-   Requires extra storage and index maintenance.

## Disk block allocation methods

These describe how a file's blocks are allocated on a storage device.

### Contiguous allocation

A file occupies consecutive disk blocks.

Advantages: - Fast sequential and direct access.

Disadvantages: - External fragmentation. - Growing a file can be
difficult.

### Linked allocation

File blocks can be scattered, with each block linking to the next.

Advantages: - Easy file growth. - No need for contiguous free space.

Disadvantages: - Poor direct/random access. - Pointer overhead and
possible pointer corruption.

FAT uses a file allocation table to maintain chains of blocks.

### Indexed allocation

An index block stores pointers to the file's data blocks.

Advantages: - Supports direct access. - Data blocks need not be
contiguous.

Disadvantages: - Index storage overhead. - Large files may need
multi-level indexing.

## Common file systems

-   NTFS -- Common on Windows.
-   FAT32 -- Widely compatible, but has limitations such as a 4 GiB
    maximum individual file size.
-   exFAT -- Common for removable storage and large files.
-   ext4 and XFS -- Common Linux file systems.
-   APFS -- Used by modern Apple systems.

------------------------------------------------------------------------

# 10. Blocking and Buffering

## Blocking

A blocking operation makes the calling thread wait until the operation
can proceed or complete.

Example: A program waits for keyboard input.

``` java
Scanner scanner = new Scanner(System.in);
String input = scanner.nextLine(); // waits for input
```

## Non-blocking operation

A non-blocking operation returns control instead of waiting when the
requested operation cannot proceed immediately.

## Buffering

Buffering temporarily stores data during transfer between components
that may operate at different speeds.

Examples: - Video streaming buffer. - Keyboard input buffer. - File I/O
buffer.

Common approaches: - Single buffering. - Double buffering. - Circular
buffering.

## Blocking vs. buffering

  -----------------------------------------------------------------------
  Blocking                            Buffering
  ----------------------------------- -----------------------------------
  Describes whether an operation      Describes temporary data storage
  waits                               

  Related to execution flow           Related to data transfer

  Example: blocking read              Example: buffered file read
  -----------------------------------------------------------------------

A buffered operation can still be blocking. For example, Java's
`BufferedReader.readLine()` uses a buffer but normally blocks while
waiting for input.

## Buffering vs. caching vs. spooling

-   Buffering: temporary storage during data transfer.
-   Caching: keeps copies of data to speed up future access.
-   Spooling: queues jobs for a device, such as a printer.

------------------------------------------------------------------------

# 11. File Descriptors

A **file descriptor (FD)** is a small non-negative integer that a
process uses to refer to an open file or I/O resource in Unix-like
systems.

A file descriptor is not the file itself.

## Standard file descriptors

    FD Name                         Purpose
  ---- ---------------------------- ---------------
     0 Standard input (`stdin`)     Input
     1 Standard output (`stdout`)   Normal output
     2 Standard error (`stderr`)    Error output

Newly opened resources often receive the lowest available descriptor,
commonly 3 or higher.

## Linux commands

``` bash
ls -l /proc/PID/fd
lsof -p PID
ulimit -n
```

Replace `PID` with a process ID.

## Redirection

``` bash
echo "Hello Linux" > output.txt
```

Writes standard output to a file.

``` bash
ls /wrong-folder 2> error.txt
```

Redirects standard error to a file.

``` bash
ls /home /wrong-folder > result.txt 2>&1
```

Redirects standard output and standard error to the same destination.

``` bash
cat < student.txt
```

Uses a file as standard input.

## File descriptor vs. inode

-   File descriptor: process-specific integer handle for an open
    resource.
-   Open file description: kernel object holding open-file state such as
    file offset and status flags.
-   Inode: file-system metadata object identifying a file in many
    Unix-like file systems.

Simplified relationship:

``` text
Process
  |
  v
File descriptor table entry
  |
  v
Open file description
  |
  v
Filesystem object (such as an inode)
```

------------------------------------------------------------------------

# 12. File and Directory Structures

## What is a file?

A file is a named collection of data stored on a storage device.

Common file attributes:

-   Name.
-   Identifier.
-   Type.
-   Location.
-   Size.
-   Protection/permissions.
-   Timestamps.
-   Owner.

Examples: `student.txt`, `photo.jpg`, `program.java`, `resume.pdf`.

## What is a directory?

A directory organizes files and other directories. It is commonly
represented to users as a folder.

Example:

``` text
College/
├── Students/
│   ├── student.txt
│   └── marks.txt
├── Photos/
│   └── photo.jpg
└── Documents/
    └── resume.pdf
```

## Five directory structures

### 1. Single-level directory

All files are kept in one directory.

Advantages: - Simple.

Disadvantages: - File names must be unique within the directory. - Poor
organization for many files.

### 2. Two-level directory

A master directory contains a separate directory for each user.

Advantages: - Different users can have files with the same name. -
Better user separation.

Disadvantages: - Sharing between users can be less convenient.

### 3. Tree-structured directory

Directories contain files and subdirectories in a hierarchy.

Advantages: - Natural organization. - Supports many levels. - Easy to
group related files.

Disadvantages: - Deep structures can become complicated. - Sharing
across branches may require links or copies.

### 4. Acyclic graph directory

Files or directories can be shared through multiple paths, but cycles
are not allowed.

Advantages: - Supports sharing. - Avoids unnecessary duplication.

Disadvantages: - More complex deletion and link management.

### 5. General graph directory

Links can create cycles.

Advantages: - Flexible linking and sharing.

Disadvantages: - Traversal must handle cycles. - More complex searching
and deletion.

## Comparison

  Structure       Organization              Sharing
  --------------- ------------------------- ----------------------
  Single-level    One directory             Limited
  Two-level       Directory per user        Limited
  Tree            Parent-child hierarchy    Through links/copies
  Acyclic graph   Shared paths, no cycles   Yes
  General graph   Cycles may exist          Yes

## Absolute and relative paths

-   Absolute path: full path beginning at the root.
-   Relative path: path interpreted from the current working directory.

Linux path symbols:

  Symbol   Meaning
  -------- -------------------------------
  `/`      Root directory
  `.`      Current directory
  `..`     Parent directory
  `~`      Current user's home directory

Example absolute path:

``` text
/home/user/documents/resume.pdf
```

If the current directory is `/home/user`, the relative path is:

``` text
documents/resume.pdf
```

## Linux directory commands

``` bash
pwd
ls
mkdir OS_Practice
cd OS_Practice
touch student.txt
mkdir Documents Pictures
mv student.txt Documents/
ls -R
rm Documents/student.txt
rmdir Pictures
```

Use `rm` carefully: deletion may be difficult or impossible to undo.

------------------------------------------------------------------------

# 13. Input/Output (I/O) Devices

I/O devices allow a computer to communicate with the outside world.

## Types of I/O devices

### Input devices

Send data to the computer.

Examples: - Keyboard. - Mouse. - Scanner. - Microphone. - Webcam. -
Barcode reader.

### Output devices

Present information from the computer.

Examples: - Monitor. - Printer. - Speakers. - Headphones. - Projector.

### Input/output devices

Perform both input and output.

Examples: - Touchscreen. - USB drive. - HDD/SSD. - Network interface
card. - Printer with scanner.

## How the OS manages I/O

The OS provides an interface between applications and hardware.

A simplified path:

``` text
Application
    |
    v
Operating System
    |
    v
Device Driver
    |
    v
Device Controller / Hardware
    |
    v
I/O Device
```

Applications typically use OS interfaces rather than directly managing
device-specific hardware details.

## Device driver

A **device driver** is software that allows the OS to communicate with a
hardware device.

Examples: - Printer driver. - Graphics driver. - Audio driver. - Network
driver.

## I/O controller

An **I/O controller** is hardware that manages communication with a
device.

Controller registers commonly include:

-   Data register -- Holds data.
-   Status register -- Reports device status.
-   Control register -- Receives commands.

## Three I/O techniques

### 1. Programmed I/O (polling)

The CPU repeatedly checks the device status.

Advantages: - Simple.

Disadvantages: - Can waste CPU time while waiting.

### 2. Interrupt-driven I/O

The device interrupts the CPU when it needs attention or when an
operation completes.

Typical sequence:

1.  CPU starts an I/O operation.
2.  CPU performs other work.
3.  Device sends an interrupt.
4.  CPU handles the interrupt.
5.  CPU resumes its previous work.

Advantages: - Avoids continuous polling. - Often improves CPU
utilization.

Disadvantages: - Interrupt handling has overhead.

### 3. Direct Memory Access (DMA)

DMA allows a controller to transfer data between an I/O device and main
memory with less CPU involvement during the bulk transfer.

Typical sequence:

1.  CPU configures the DMA operation.
2.  DMA controller manages the transfer.
3.  Data moves between device and memory.
4.  Controller notifies the CPU when appropriate.

Advantages: - Reduces CPU involvement in large transfers. - Useful for
high-speed devices.

## Comparison

  ------------------------------------------------------------------------
  Feature           Programmed I/O    Interrupt-driven   DMA
                                      I/O                
  ----------------- ----------------- ------------------ -----------------
  CPU involvement   High              Moderate           Lower during bulk
                                                         transfer

  Waiting           Repeated polling  Device             Controller
                                      notification       manages transfer

  Data movement     CPU handles it    CPU handles it     DMA controller
                                                         handles bulk
                                                         transfer
  ------------------------------------------------------------------------

## Buffering

Buffering temporarily stores data in memory while it is transferred
between components operating at different speeds.

Example: Data is placed in a buffer before being sent to a slower
printer.

## Spooling

Spooling queues jobs for a device, often using disk storage.

Example: Multiple print jobs wait in a print queue and are processed one
by one.

  Buffering                                Spooling
  ---------------------------------------- -------------------------------------
  Temporary data storage during transfer   Queues jobs for a device
  Often uses RAM                           Commonly uses disk
  Helps manage speed differences           Helps share a device among requests

## Linux commands to inspect devices

``` bash
lsblk
lsusb
lspci
lscpu
free -h
```

Additional commands (may require installing packages):

``` bash
sudo lshw -short
iostat
```

------------------------------------------------------------------------

# 14. Quick Revision and Interview Checklist

## Definitions to remember

  -----------------------------------------------------------------------
  Term                                Short definition
  ----------------------------------- -----------------------------------
  Process                             A program in execution

  Thread                              A unit of execution within a
                                      process

  PCB                                 OS data structure holding process
                                      information

  Context switch                      Saving one process/thread state and
                                      restoring another

  CPU scheduling                      Selecting a ready process for CPU
                                      execution

  Race condition                      Result depends on concurrent
                                      operation timing

  Critical section                    Code that accesses shared resources
                                      and needs protection

  Mutex                               Mutual-exclusion lock

  Semaphore                           Counter-based synchronization
                                      mechanism

  Deadlock                            Processes wait indefinitely for one
                                      another's resources

  Paging                              Dividing virtual memory into
                                      fixed-size pages

  Segmentation                        Dividing memory into variable-sized
                                      logical units

  Page fault                          Access to a page that is not
                                      currently present as required

  Buffering                           Temporary storage during data
                                      transfer

  Spooling                            Queuing jobs for a device

  File descriptor                     Process-specific integer handle for
                                      an open resource

  Directory                           Structure that organizes files and
                                      subdirectories

  Device driver                       Software interface to hardware

  DMA                                 Bulk data transfer with reduced CPU
                                      involvement
  -----------------------------------------------------------------------

## Important formulas

``` text
Turnaround Time = Completion Time - Arrival Time

Waiting Time = Turnaround Time - Burst Time

Average Waiting Time = Total Waiting Time / Number of Processes

Average Turnaround Time = Total Turnaround Time / Number of Processes
```

## Topics to practise

-   Draw the five process states and transitions.
-   Explain the contents of a PCB.
-   Compare processes and threads.
-   Solve FCFS, SJF, Priority, and Round Robin scheduling problems.
-   Explain race conditions, mutexes, and semaphores.
-   List the four necessary deadlock conditions.
-   Compare paging and segmentation.
-   Explain page faults and page replacement.
-   Compare contiguous, linked, and indexed file allocation.
-   Explain file descriptors and standard streams.
-   Draw the five directory structures.
-   Compare programmed I/O, interrupt-driven I/O, and DMA.
-   Explain buffering and spooling.

## Suggested practical revision

On a Linux system, practise these commands:

``` bash
# Process management
ps aux
top
pstree

# Files and directories
pwd
ls -l
mkdir practice
touch practice/example.txt
find practice -type f

# Storage and devices
lsblk
lsusb
lspci

# Memory
free -h
cat /proc/meminfo

# Open file descriptors (replace PID)
ls -l /proc/PID/fd
```

------------------------------------------------------------------------

**End of Operating Systems learning notes.**

These notes consolidate the topics covered in this learning
conversation. They are designed for beginner revision and fresher-level
interview preparation.
