Address Sanitizer is a memory error detector tool designed by Google. It finds out-of-bounds accesses to heap, stack and global objects, as well as use-after-free bugs. It employs a specialized memory allocator and code implementation that is simple, yet effective. 

ASAN consists of 2 parts: instrumentation module and a run time library. 

Instrumentation module modifies the code to check the shadow state for each memory access and creates poisoned red-zones around stacks and global objects to detect underflows and overflows. 

## [[Shadow Memory]]

Shadow memory is used to store metadata corresponding to each piece of application data. 

Algorithm