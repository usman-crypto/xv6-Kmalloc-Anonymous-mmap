# xv6-Kmalloc-Anonymous-mmap
in this we are going to implement kmalloc and anonymous memory management

Introduction
The goal of this project was to build a memory allocator for the kernel in the xv6 operating system. The current xv6 operating system only has a user-space allocator which is not suitable for kernel-level data structures since the memory returned by the user-space allocator is freed when the process terminates. The kernel requires memory that will be stable beyond the lifetime of a process.
XV6 is a simple Unix-like operating system, developed at MIT for teaching operating systems. It is based on the sixth edition of Unix (Version 6 Unix) which was developed at Bell Labs. In this assignment, we were tasked with implementing the mmap and munmap system calls in XV6. mmap system call maps a file or device into memory while munmap system call unmaps the memory mapped region. We have implemented these system calls in accordance with the instructions given by the professor.


Approach
To achieve the goal, we created a new source file named kmalloc.c and reused most of the umalloc.c code. The kmalloc.c file contains a new memory allocator function named kmalloc() that can allocate variable-sized chunks of memory in the kernel. We have also added a corresponding kmfree() function to free the allocated memory.

In the kmalloc() function, we have used a similar algorithm as the user-space memory allocator to manage the memory regions. However, we have used kalloc() instead of morecore() to receive a free page to refill the free chunks in the kmalloc. This ensures that the memory used by kmalloc is stable beyond the lifetime of a process.

We have added a check that panics the kernel if kmalloc() is asked for more memory than the limitation allows for (4096 bytes). This is to avoid wasting space in the kernel memory.

For testing purposes, we have added two system calls for kmalloc() and kmfree(). These system calls simply call the actual kernel implementation of kmalloc & kmfree. We have also written wrappers for calling the kmalloc & kmfree from the user space.

For part 4B 
We have implemented mmap and munmap system calls in XV6. We have used a linked list data structure to keep track of mmap regions. Each element in the list represents a contiguous region of mapped memory and keeps track of the starting address, length, region type (anonymous vs file-backed), offset, and fd. We have added these metadata fields to the process struct in proc.h.

For mmap system call, we have created a new function mmap_alloc that allocates physical pages and maps them to the process's virtual address space. We have added a new switch case in syscall function to handle the mmap system call. The mmap system call first checks if the given arguments are valid or not, then it allocates pages and maps them to the virtual address space using mmap_alloc function. It then creates a new entry in the mmap regions list and initializes the metadata fields accordingly.

For munmap system call, we have created a new function munmap_remove that removes the mmap region from the list, deallocates the physical pages and clears the memory. We have added a new switch case in syscall function to handle the munmap system call. The munmap system call first checks if the given arguments are valid or not, then it removes the mmap region using munmap_remove function. It then deallocates the physical pages and clears the memory.

Code Changes
The following changes were made to the xv6 operating system:

Added kmalloc.c file containing kmalloc() and kmfree() functions.
Modified the umalloc.c code to reuse most of the code in kmalloc.c.
Modified how the allocator gets more memory by using kalloc() instead of morecore().
Added a check that panics the kernel if kmalloc() is asked for more memory than the limitation allows for (4096 bytes).
Added two system calls for kmalloc() and kmfree() to test the implementation.
Conclusion
In conclusion, we have successfully built a memory allocator for the kernel in xv6. The implementation is similar to the user-space memory allocator with modifications to make it suitable for kernel-level data structures. The added system calls for kmalloc() and kmfree() make it easier to test the implementation. The new kmalloc() ensures that the memory used by kernel data structures is stable beyond the lifetime of a process, which is necessary for a stable operating system.
