
A bits - Every memory byte is shadowed with a single A bit (A is short for addressability) which indicates if the client may legitimately access it. 
	0 represents unaddressable byte 
	1 represents addressable byte
A bits can detect uses of unaddressable memory such as heap buffer overflows and wild reads and writes.  

V bits - Every register and memory byte is shadowed with eight V bits ("V" for validity) which indicates if the value bits are defined (i.e intialized). 
	0 represents a defined bit. 
	1 represents undefined bit. 
This can check dangerous uses of undefined values with bit precision. 

Heap Blocks - Records the location of every live heap block in a hash table. With this info, it can detect bad or repeated frees of heap blocks, and memory leaks.  

Each shadow memory byte has 8 V bits and 1 A bit. 