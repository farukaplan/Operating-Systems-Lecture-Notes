###############################################################################################################################################################
+                                                                   SYNC

-> Cooperating Process: Can affect or  be affected by other processes executing in the system.
-> counter++ counter-- meselesi, biliyon

-> Critical section çözümü için 3 şart sağlanacak:
    - Mutual exclusion: Tek process girecek
    - Progress: Progress olacak
    - Bounded waiting: Belli bir bekleme süresi

-> Process sync solutions:
    1) Peterson's solution
    2) Hardware solutions:
        - test_and_set() - compare_and_swap() 
    3) Atomic variables:
        - Allows us to perform an atomic operation. Atomic var. minimize sync and help avoid memory consistency. Hence, it ensures mutual exclusion.
    4) Mutex locks:
        - acquire() - release()
        - So much busy waiting
    5) Semaphore:
        - wait() - signal()
        - block() - wakeup(p)
    6) Monitors:  
        - ADT
        - Semaphore incorrectly kullanıldığında deadlock falan olur
        - Semaphore'a ek olarak condition variable gelir
        - condition x; x.wait() - x.signal()
        - Advantage: More useful | Disadvantage: Gives compiler to additional burdon

-> Classic problems of sync:
    - Bounded-buffer problem: https://www.youtube.com/watch?v=YS6aQgk8Sg0
    - The Dining-philosophers problem: https://www.youtube.com/watch?v=9f1oOMX3mP4
    - The Readers–Writers Problem: https://www.youtube.com/watch?v=Lp-SqOuX2Eo 

###############################################################################################################################################################
+                                                                   DEADLOCKS

-> Deadlock: A waiting process that never again able to change state.

-> A process may utilize a resource in only the following sequence:
    1) Request
    2) Use
    3) Release

-> Sıkıntı genelde request ve release kısımlarında oluyor.

-> Livelock: Kapıdan geçmeye çalışıyolar sürekli birbirlerine yol veriyorlar, typically occurs when threads retry failing operations at the same time.
    - It can generally be avoided by having each thread retry the failing operation at random times.

-> There four conditions must hold for a deadlock to occur:
    1) Mutual exclusion: Only one process at a time can use a resource
    2) Hold and wait: A process holding at least one resource is waiting to acquire additional resources held by other processes
    3) No preemption: Resources cannot be preempted; a resource can be released only voluntarily by the process holding it
    4) Circular wait: p0 p1'i, p1 p2'yi, ... pn p0'ı bekler

-> Resource allocating graph: https://www.youtube.com/watch?v=-VksGXfiK7k
    - No cycle, no deadlock
    - If graph contain cycle:
        - if only one instance per resource type, then deadlock
        - if several instances per resource type, possibility of deadlock

-> Deadlock solutions:
    1) We can use a method to 
        - prevent deadlocks or avoid deadlocks
        - ensuring that the system will never enter a deadlocked state
    2) We can allow the system to enter a deadlocked state, detect it, and recover.
    3) We can ignore the problem altogether and pretend that deadlocks never occur in the system.
        - Most used one by OS's, including linux and windows
        - The handling the deadlocks is up to the developer

-> To ensure that deadlocks never occur, the system can use:
    1) Deadlock prevention scheme
        - At least one of the necessary condition cannot hold.
    2) Deadlock-avoidance scheme    
        - OS has additional information in advance concerning which resources a process will request and use during its lifetime. 
        - With this additional knowledge, the operating system can decide for each request whether or not the process should wait.
        - To decide whether the current request can be satisfied or must be delayed, the system must consider:
            - The resources currently available
            - The resources currently allocated to each process
            - And the future requests and releases of each process
    - These methods prevent deadlocks by constraining how requests for resources can be made.

-> Let's talk about 1st method for deadlock prevention: https://www.youtube.com/watch?v=_sKaad5GrYc
    1) The mutual exclusion condition must hold
        - At least one resource must be nonsharable.
        - Sharable resources do not require mutually exclusive access and thus cannot be involved in a deadlock
            - Ex: Read Only Files.
        - In general, we cannot prevent deadlocks by denying the mutual-exclusion condition because some resources are naturally nonsharable
            - Ex: Mutex locks
    2) To ensure that the hold-and-wait condition never occurs
        - We must guarantee that, whenever a process requests a resource, it does not hold any other resources.
            - One protocol that we can use requires each process to request and be allocated all its resources before it begins execution.
            - Alternative protocol allows a process to request resources only when it has none. 
            - Let's illustrate: A process that copies data from a DVD to a file, sorts the file, then prints the results to a 
                - If all resources must be requested at the beginning of the process, then the process must initially request the DVD drive, disk file, and printer
                    - It will hold the printer for its entire execution, even though it needs the printer only at the end.
                - The second method allows the process to request initially only the DVD drive and disk file.
                    - It copies from the DVD drive to the disk and then releases both the DVD drive and the disk file.
                    - The process must then request the disk file and the printer.
                    - After copying the disk file to the printer, it releases these two resources and terminates.
            - These 2 protocols have 2 main disadvantage:
                - First, resource utilization may be low, since resources may be allocated but unused for a long period.
                - Second, starvation is possible. 
    3) There is no preemption of resources that have already been allocated.
        - If a process is holding some resources and requests another resource that cannot be allocated to it
            - Then all resources the process is currently holding are preempted (released).
    - These 3 prevention is generally not practical
    4) Circular wait: One way to ensure that this condition never holds 
        - To impose a total ordering of all resource types
        - To require that each thread requests resources in an increasing order of enumeration.
    - One side effect of these prevention algorithms are: Low device utilization and reduced system throughput.
    - An alternative method is: To require additional information about how resources are to be requested.

-> A deadlock-avoidance algorithm dynamically examines the resource-allocation state to ensure that a circular-wait condition can never exist
    - State: Number of available and allocated resources and the maximum demands of the processes
    - Safe state: System can allocate resources to each process in some order and still avoid a deadlock
        - There is sequence <P1, P2, …, Pn>
        - If Pi resource needs are not immediately available, then Pi can wait until all Pj have finished (j<i)
        - When Pj is finished, Pi can obtain needed resources, execute, return allocated resources, and terminate
        - When Pi terminates, Pi +1 can obtain its needed resources, and so on

-> If a system is in safe state => no deadlocks
-> If a system is in unsafe state => possibility of deadlock
-> Avoidance => ensure that a system will never enter an unsafe state.

-> Deadlocks s55 to s57

-> Single instance of a resource type: Use a resource-allocation graph
-> Multiple instances of a resource type: Use the banker’s algorithm

-> Resource-Allocation graphta ekstra olarak Claim edge var, dashed line ile gösterilir, gelecekte herhangi bir process tarafından request edilme olasılığı var, request edilince request line'a dönüşür, bu request edge de assignment edge'e dönüşür when allocated the process

-> Resources must be claimed a priori in the system

-> Banker's Algorithm: https://www.youtube.com/watch?v=CVhhKt03Y1c (belli bi dakikadan sonra)
    - s71'de Resource-Allocation Graph and  Wait-for Graph var, videoda zaten var ekstra bir şey değil

-> If deadlocks occur frequently, then the detection algorithm should be invoked frequently. 
    - Resources allocated to deadlocked threads will be idle until the deadlock can be broken
    - In addition, the number of threads involved in the deadlock cycle may grow.

-> To eliminate deadlocks by aborting a process, we use one of two methods:
    - Abort all deadlocked processes
    - Abort one process at a time until the deadlock cycle is eliminated
        - In which order should we choose to abort?
            - Priority of the process
            - How long process has computed, and how much longer to completion
            - Resources the process has used
            - Resources process needs to complete
            - How many processes will need to be terminated

-> To eliminate deadlocks using resource preemption, we preempt some resources from processes and give it to others.
    - Selecting a victim: Which resources and which processes are to be preempted?
    - Rollback: Return to some safe state, restart process for that state
        - It is difficult to determine what a safe state is, the simplest solution is total safe state: Abort the process and then restart it.
    - Starvation: same process may always be picked as victim
        - A process can be picked as a victim only a finite number of times: Include number of rollback in cost factor

###############################################################################################################################################################
+                                                                   MAIN MEMORY

-> Main memory and registers are only storage CPU can access directly
-> Register access: 1 CPU cycle; Main memory access: 200 cycles, cause stall, to prevent this, we use caches.

-> We must protect the operating system from access by user processes. On multiuser systems, we must additionally protect user processes from one another. 
    - This protection must be provided by the hardware
    - We need to make sure that each process has a separate memory space.
        - To provide this, we can use 2 register: Base and limit registers
            - some process | base register | our process | base + limit register

-> CPU hardware compare every address generated in user mode with the registers for protection of memory space
-> The base and limit registers can be loaded only by the OS, which uses a special privileged instruction.
    - Since privileged instructions can be executed only in kernel mode:
        - and only the OS executes in kernel mode, only the operating system can load the base and limit registers. (s13)

-> The processes on the disk that are waiting to be brought into memory for execution form the input queue.
    - Select one of the processes in the input queue and to load that process into memory.
    - As the process is executed, it accesses instructions and data from memory. 
    - Eventually, the process terminates, and its memory space is declared available.

-> Addresses may be represented in different ways during these steps:
    - Addresses in the source program are generally symbolic (such as the variable count).
    - A compiler typically binds these symbolic addresses to relocatable addresses (such as “14 bytes from the beginning of this module”).
    - The linkage editor or loader in turn binds the relocatable addresses to absolute addresses (such as 74014).

-> Address binding of instructions and data to memory addresses can happen at three different stages:
    - Compile time: If memory location known a priori, absolute code can be generated; must recompile code if starting location changes
    - Load time: final binding is delayed until load time. Must generate relocatable code if memory location is not known at compile time. If the starting address changes, we need only reload the user code.
    - Execution time:  binding delayed until run time if the process can be moved during its execution from one memory segment to another
        - Most general-purpose OS use this method.

-> Binding addresses at either compile or load time generates identical logical and physical addresses. 
-> However, the execution-time address-binding scheme results in differing logical and physical addresses.  
    - We usually refer to the logical address as a virtual address. 
    - The set of all logical addresses generated by a program is a logical address space.
    - The set of all physical addresses corresponding to these logical addresses is a physical address space. 

-> Address generated by the CPU = Virtual (logical) address; Address seen by memory = Physical address
-> Set of all adresses generated by; CPU = logical address space, Corresponding to these logical space = Physical address space

-> The run-time mapping from virtual to physical addresses is done by a hardware device called the memory-management unit (MMU) 
    - CPU | logical adress | MMU | phys address | physical memory

-> Base register = relocation register
    - The value in the relocation register is added to every address generated by a user process
        - For ex: CPU | 346 | MMU (relocation reg.) | 14346 | physical memory
    - The user program never sees the real physical addresses, the user program deals with logical addresses. (s24)

-> The size of a process has thus been limited to the size of physical memory.
    - To obtain better memory-space utilization, we can use dynamic loading.
        - With dynamic loading, a routine is not loaded until it is called. 
        - All routines are kept on disk in a relocatable load format.
            - a routine is loaded only when it is needed. 
                - This method is particularly useful when large amounts of code are needed to handle infrequently occurring cases, such as error routines. 

-> Dynamic linking, in contrast, is similar to dynamic loading.
    - However, linking, rather than loading, is postponed until execution time.
    - Without DLL's, each program on a system must include a copy of its language library in the executable image. 
        - This wastes both disk space and main memory.
        - A second advantage of DLLs is that these libraries can be shared among multiple processes, so that only one instance of the DLL in main memory.
            - For this reason, DLLs are also known as shared libraries (Freq. used in windows and linux)

-> Memory divided into 2 part:
    - For OS
    - For user processes
    
-> We can place the OS in either low memory addresses or high memory addresses.
    - This decision depends on many factors, such as the location of the interrupt vector.
    - However, many OS (Linux and Windows) place the OSin high memory

-> We want several user processes in memory at same time
    - In contiguous memory allocation, each process is contained in a single section of memory that is contiguous to the section containing the next process.

-> Memory protection: Process alanları karışmasın. We provide this with relocation and limit registers.
    - The relocation register contains the value of the smallest physical address
    - The limit register contains the range of logical addresses
        - Each address must fall within the range specified by the limit register. (s35)

-> When the CPU scheduler selects a process for execution
    - The dispatcher loads the relocation and limit registers with the correct values as part of the context switch. 

-> Let's come back to memory allocation.
-> Variable partition scheme: The OS keeps a table indicating which parts of memory are available and which are occupied. 
    - Initially, all memory is available for user processes and is considered one large block of available memory, a hole. (s38)

-> When process enters the system, the operating system takes into account:
    - the memory requirements of each process
    - the amount of available memory space 

-> When there isn’t sufficient memory to satisfy the demands of an arriving process, there will be 2 option:
    - One option is to simply reject the process
    - Alternatively, we can place such processes into a wait queue
        - When memory is later released, OS checks the wait queue

-> Degree of multiprogramming limited by number of partitions
-> When a process arrives, it is allocated memory from a hole large enough to accommodate it

-> OS maintains info about:
    - Allocated partitions
    - Free partitions (hole)

-> Dynamic storage allocation problem
    - concerns how to satisfy a request of size n from a list of free holes
    - There are many solutions used to select a free hole from the set of available holes.
        - First-fit: Allocate the first hole that is big enough
        - Best-fit: Allocate the smallest hole that is big enough
            - must search entire list, unless ordered by size  
        - Worst-fit: Allocate the largest hole (en verimsizi)
            - must also search entire list 

-> External fragmentation: There is enough total memory space to satisfy a request, but the available spaces are not contiguous
    - There are large number of small holes.
    - First-fit and best-fit suffers from this a lot

-> For ex: 18.500 byte hole, 18.448 byte process, left 2 byte from hole
    - The overhead to keep track of this hole will be larger than the hole itself.
    - To avoiding this problem is to break the physical memory into fixed-sized blocks and allocate memory in units based on block size.
        - the memory allocated to a process may be slightly larger than the requested memory.
        - Internal fragmantation: Bu aradaki fark işte

-> Solution to external fragmantation: Compaction
    - The goal is to shuffle the memory contents so as to place all free memory together in one large block.
    - Compaction is not always possible. If relocation is static and is done at assembly or load time, compaction cannot be done.
    - It is possible only if relocation is dynamic and is done at execution time. 
        - Relocation requires only moving the program and data and then changing the base register to reflect the new base address.
        - This has a cost
        - The simplest compaction algorithm is to move all processes toward one end of memory (expensive)
        - Another solution is to permit the logical address space of the processes to be non-contiguous
            - 2 method achieves this, also these can be combined:
                - Segmentation: Permits the physical address space of a process to be noncontiguous.
                - Paging: Avoid ext. frag., whereas segmentation does not. It used frequently

-> Paging: https://www.youtube.com/watch?v=LKYKp_ZzlvM
    - Breaking physical memory into fixed-size blocks = frames
    - Breaking logical memory into blocks of same size = pages
    - When process executed, its pages loaded into any available frame 
    - The backing store (secondary disk) is divided into fixed-size blocks that are the same size as the memory frames
        - Logical address space is totally seperate from the physical adderss space, so
            - a process can have 64 bit address space even though the system has less than 2^64 bytes of physical memory
    - the offset d does not change
    - The page size (like the frame size) is defined by the hardware
    - The size of a page is a power of 2, typically varying between 4 KB and 1 GB per page, depending on the computer architecture.
    - External frag. olamaz ama internal olabilir. 
        - 2048 byte page size, 72.766 byte process size => we need 35 page + 1.086 byte
            - if we allocate n+1 page, resulting in internal fragmentation of almost an entire frame.
    - So small frame sizes desirable ?
        - But each page table entry takes memory to track
    - Each page of the process needs one frame
        - Thus, if the process requires n pages, at least n frames must be available in memory. (s65)
    - This mapping is hidden from the programmer and is controlled by the operating system.
    - The importance is the clear separation between the programmer’s view of memory and the actual physical memory. 
    - Page table stored in RAM

-> Since the operating system is managing physical memory it must be aware of the allocation details of physical memory:
    - which frames are allocated, which frames are available...
    - This information is generally kept in a data structure called a frame table. ,
        - The frame table has one entry for each physical page frame, indicating whether
            - it is free or allocated
            - if it is allocated, to which page of which process or processes.

-> Translation look-aside buffer (TLB): TLB speeds up translation of virtual address to physical address by storing page-table in a faster memory
    - TLB sits between CPU and Main memory like Cache.
    - TLB is used by MMU
    - keeps track of where virtual pages are stored in the physical memory. 
    - cache of the page-table.
    - Contains page number and frame number
        - If the page number is not in TLB
            - known as TLB miss
            - A memory reference to the page table must be made. Depending on the CPU:
                - This may be done automatically in hardware or
                - Via an interrupt to the OS. (s71)

s72

 
