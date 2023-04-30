Download Link: https://assignmentchef.com/product/solved-csce313-project-1-pa1-buddy-allocator
<br>
The kernel manages the physical memory both for itself and for all of the tasks and user processes that run on the system. The memory occupied by the kernel code is reserved and is never used for any other purpose. Physical memory separate from this reserved space, can be used for any other task imaginable in the system. Most of this memory must be allocated and de-allocated dynamically, and an algorithm must be in place to keep track of which physical memory is in use, and by whom. Ideally, physical memory should look like a single contiguous segment from which an allocator can take portions of memory and provide them from processes. In the same way, when a process is done with a segment of memory, the allocator should be able to place it back inside of the huge segment for future use. This is not the case in most systems. Having to deal with a single chunk of memory could be too much for the allocator to manage, especially given the wide variety of ways that memory is used on the system. Rather, different segments of physical memory have different properties. For example, DMA (direct memory access) may not be able to address physical memory above 16 MB. Similarly, the system may contain more physical memory than what can be directly addressed. If this is the case, the memory cannot be viewed as one giant segment, and it needs to be managed using one of several address space extension mechanisms (address space extension is out of the scope of this assignment). For these two reasons, along with many possible others, a large number of operating systems (Linux/UNIX included) partition the memory into zones, and treat each zone separately for allocation purposes. Memory allocation requests typically come with a list of zones that can be used to satisfy the request. For example, a particular request may be preferably satisfied from the ”normal” zone. If that fails, then the allocation may possibly be reattempted from the high-memory zone that needs special access mechanisms. If the previous two requests fail, allocating from more zones can be attempted as long as the process has permissions to do so. Within each zone, many systems (for example Linux) use a buddy-system allocator to allocate and free physical memory. This is what you will be providing in this Programming Assignment (i.e. an allocator for a single memory zone, and not for an entire physical system). In this Programming Assignment, you are to develop a simple memory allocator that implements, using the buddy system memory allocation scheme, the functions

, similar to the standard C li-

<table width="422">

 <tbody>

  <tr>

   <td colspan="3" width="198">BuddyAllocator::alloc()</td>

   <td width="35">and</td>

   <td colspan="2" width="188">BuddyAllocator::free()</td>

  </tr>

  <tr>

   <td width="87">brary calls:</td>

   <td width="74">malloc()</td>

   <td width="37">and</td>

   <td colspan="2" width="59">free()</td>

   <td width="165"> </td>

  </tr>

  <tr>

   <td width="87"></td>

   <td width="74"></td>

   <td width="37"></td>

   <td width="35"></td>

   <td width="24"></td>

   <td width="165"></td>

  </tr>

 </tbody>

</table>

. The objectives of this programming assignment in

general are:

1

<ul>

 <li>Package a simple module with some static data as a separately compiled unit.</li>

 <li>Become deeply familiar with pointer management and array management in the C/C++ languages.</li>

 <li>Become familiar with standard methods for handling command-line arguments in a C++/UNIX environment.</li>

 <li>Become familiar with simple UNIX development tools such as: the compiler, make, the GNU debugger, object file inspector, etc..</li>

</ul>

<h1>2      Assignment</h1>

You are to implement a buddy-system memory manager that allocates memory in blocks with sizes that are power-of-two multiples of a basic block size. The basic block size is given as an argument when the allocator is initialized.

<ul>

 <li>The memory allocator shall be implemented as a C++ module BuddyAllocator , which consists of a header file h and BuddyAllocator.cpp . (A copy of the header file and a rudimentary .cpp file are provided to you). You have to:

  <ul>

   <li>Provide implementation for the empty types (e.g., BlockHeader, LinkedList and functions in the BuddyAllocator class</li>

   <li>Add necessary functions and types as you see fit</li>

  </ul></li>

 <li>Evaluate the correctness (up to a certain point) and the performance of your allocator. For this, you will be given the source code of a function which implements the highly-recursive Ackerman function. In this implementation of the Ackerman function, random blocks of memory are allocated and de-allocated sometime later, generating a large combination of different allocation patterns. The Ackerman function is provided in the form of two files (i.e. h and Ackerman.cpp ).</li>

 <li>You will modify the given cpp so that it reads the basic block size and the memory size (in bytes) from the command line, initializes the memory, and then calls the Ackerman function. It must measure the time it takes to perform the number of memory operations.</li>

 <li>Use the getopt() C library function to parse the command line for arguments. The usage of your executable should be of the form: memtest [-b <em>&lt;</em>blocksize<em>&gt;</em>] [-s <em>&lt;</em>memsize<em>&gt;</em>]</li>

</ul>

-b <em>&lt;</em>blocksize<em>&gt; </em>defines the block size, in bytes. The default should be 128 bytes if the -b argument is not provided.

-s <em>&lt;</em>memsize<em>&gt; </em>defines the size of the memory to be allocated, in bytes. The default should be 512 kB.

<ul>

 <li>Repeatedly invoke the Ackerman function with increasingly larger number of values for n and m (be careful to keep <em>n </em>≤ 3 as the processing time increases steeply for large values of n). Identify at least one point that you may modify in the simple buddy system described above to improve the performance. Also be sure to provide an argument as to why/how your proposed change would improve performance. • Make sure that the allocator gets de-allocated (and has its memory freed) when the program either exits or aborts. Use the destructor function of the BuddyAllocator class to acheive that.</li>

</ul>

You are to submit the directory containing all the files in the starter code. If you add any files, please include those as well and a corrected makefile in that case. The Ackerman module is not supposed to change except for dubugging. The submission folder should also contain a report file (called analysis.pdf, in PDF format) with the analysis of the effects of the performance of your allocator for increasing numbers of allocate/free operations. Vary this number by varying the parameters n and m in the Ackerman function. Determine where the bottlenecks are in the system, or where poor implementation is affecting performance. Identify at least one point that you would modify in this simple implementation to improve performance. Argue why it would improve performance. The complete analysis can be made in 500 words or less with at least one or two graphs. If your program does not work correctly, this report/analysis is the right place to be as clear as possible about which of your functions work, which do not, and which only partially work. While writing this program, you will realize how difficult it is for the grader to give you partial points unless you help him. Please try to communicate with screen-shots and other forms such that the grader has little difficulty running your program and he can identify what is working and what is not working easily. Remember, failure to follow these instructions or any of the assignment directives will result in lost points.

<h1>4          Implementating Buddy-System Allocation</h1>

In our buddy-system memory allocator, memory block sizes must be a power of two with a minimum size defined by the variable basic block size . For example, if 9kB of memory is requested, the allocator must return the nearest power of two, 16kB. Because of this 7kB is wasted in a process known as fragmentation. In spite of the potential for wastage, the restriction of block sizes to powers of two makes the management of free memory blocks easy. The allocator must simply keep an array of free lists, one for each allowable block size. Every request is rounded up to the next allowable size, and the corresponding free list is checked. If there is an entry in the free list, this entry is returned and then removed from the free list.

In the event that the free list for that particular block size is empty (i.e. there are no free memory blocks of that size), a larger block is selected using the free list of some larger block size and then recursively split until a block of the desired size is obtained. Whenever a free memory block is split in two, one block gets either used or further split, and the other unused block, its buddy, is added to the corresponding free list.

For example: Assume that the system needs a 16kB block, but the free list is empty.

Also assume that the free list for 32kB blocks is empty, but the 64kB list has a free element,

<ol>

 <li><em>B</em>. The allocator should therefore select the 64kB block and split it into two blocks, <em>B<sub>L </sub></em>and <em>B<sub>R</sub></em>, each 32kB and add then to the 32kB free list. <em>B<sub>L </sub></em>should then be split again into <em>B<sub>LL </sub></em>and <em>B<sub>LR</sub></em>, each 16kB. Finally, <em>B<sub>LL </sub></em>should be used and <em>B<sub>LR </sub></em>should be added to the 16kB free list.</li>

</ol>

In the previous example, the blocks <em>B<sub>L </sub></em>and <em>B<sub>R </sub></em>are buddies, as are <em>B<sub>LL </sub></em>and <em>B<sub>LR</sub></em>. Whenever a memory block is freed, it may be coalesced with its buddy. Coalescing can only occur if the buddy is free as well. If it is, then the two buddies can be combined to form a single memory block that is twice the size of each buddy individually.

For example: Assume that <em>B<sub>LL </sub></em>and <em>B<sub>LR </sub></em>are free, and that we have just freed <em>B<sub>LR</sub></em>. In this case, <em>B<sub>LL </sub></em>and <em>B<sub>LR </sub></em>can be coalesced into the single block <em>B<sub>L</sub></em>. We therefore, delete <em>B<sub>LL </sub></em>from its free list and proceed to insert the newly formed <em>B<sub>L </sub></em>into into the next higher free list. Before we do that, we check to see if its buddy, <em>B<sub>R </sub></em>is free. If so, then <em>B<sub>L </sub></em>and <em>B<sub>R </sub></em>can be recombined to form <em>B</em>, recreating the 64kB block that was split in the first example of this section.

<h2>4.1        Finding Buddies</h2>

The buddy system performs two operations on memory blocks: splitting and coalescing. Whenever we split a free memory block of size 2<em><sup>n </sup></em>with start address A, we generate two buddies: one with start address A, and the other with the start address of A that has the (<em>n</em>− 1)<em><sup>th </sup></em>bit flipped.

Finding the buddy of a block being freed is just as simple when the size of the block is known: The address of the buddy block is determined by flipping the appropriate bit of the block’s start address, just as is the case when we split a block. The problem is: How will we know what the block size is when the program needs to split or coalesce a block?

The easiest way is to explicitly store it at the beginning of the allocated block as a part of a header. This wastes memory as the space where the header is being stored cannot be used by the requesting program to store any data. Alternatively, the size can be implicitly inferred from other data, typically stored in the free list. However, for this assignment, you should just keep it part of the header.

<h2>4.2         Managing the Free List</h2>

You want to minimize the amount of space needed to manage the free list. For example, you do not want to implement the lists using traditional means (i.e. with dynamically created elements that are connected with pointers). An easy solution is to use the free memory blocks themselves to store the free-list data. For example, the first bytes of each free memory block would contain the pointer to the previous and to the next free memory block of the same size. The pointers in the first and last block in each free list can easily be stored in an array of pointers, two for each allowable block size.

<h2>4.3        Note on Block Size</h2>

If you decide to put management information into allocated blocks such as the size of the block, you have to be careful about how this may affect the size of the allocated block. For example, when you allocate a block of size 5, and add an 8-byte header to the block, you actually need room for the 8-byte header along with the 5-bytes for the data space requested. Consequently, you would need a 13-byte block instead of a 5-byte one (in case you couldn’t tell, this is extremely wasteful).

<strong>4.4          Where does the allocator get memory from?</strong>

Inside the constructor of BuddyAllocator class, your program must acquire the required amount of memory from the system. It must interface with the existing system library, malloc() in C or new operator in C++ , in order to do so. Remember, your allocator must

also return the memory to the system when it is done using the BuddyAllocator::free() function.

<strong>4.5              What does </strong>BuddyAllocator::alloc() <strong>return?</strong>

In the case above, putting the management information block in front of the allocated block is an effective way to access it easily. In this case, make sure that your BuddyAllocator::alloc() routine returns the address of the allocated block, not the address of the management information block. There is no guarantee that the program calling BuddyAllocator::alloc()

won’t completely overwrite the header information.

<h2>4.6           Initializing the Free List and the Free Blocks</h2>

You are given the size of the available memory as argument to the BuddyAllocatorconstructor function. The number entered by a potential user is likely not a power of two multiple of the basic block size. However, in order to function correctly, your program must partition the memory into a sequence of power-of-two sized blocks and initialize the blocks and the free list accordingly. This means that your program could use more memory than requested by the user.