zero-copy
=========

Zero copy lets you avoid redundant data copies between intermediate buffers and reduces the number of context switches between user-space and kernel-space. Ideal Zero copy (**zero cpu copy**) is possible when your hardware (disk drive, network card, graphic card, sound card) supports **DMA** (Direct Memory Acess).

Example below demonstrates zero and non-zero file copy in Java .


Let's try out example in Java
---------------------------

Compiling a java class is as simple as:
```shell
javac JioChannel.java
```

Now let's execute non-zero file copy where 8K buffer is used to read and write data.
```shell
time java JioChannel /tmp/rbigfile.dat /tmp/wbigfile.dat 1

-----------------------
real  0m14.445s
user  0m0.364s
sys   0m1.956s
-----------------------
```


Now let's execute zero file copy where **FileChannel.transferTo()** is used to copy data between one file channel and another.
```shell
time java JioChannel /tmp/rbigfile.dat /tmp/wbigfile.dat 2

-----------------------
real  0m15.473s
user  0m0.124s
sys   0m1.516s
-----------------------
```
Again as copying is done within the kernel space, **FileChannel.transferTo()** is more efficient than the combination of read and write, which would require transferring data to and from user space.

###real vs user vs sys

**real** - elapsed "wall clock" time, like using a stop watch. It includes time slices used by other processes and time the process spends blocked.  
**user** - amount of CPU time spent in user-space code (outside the kernel-space) within the process.  
**sys** - amount of CPU time spent in the kernel-space within the process. CPU time spent in system calls.  
**user + sys** - actual CPU time used by process.  


Conclusion
---------------------------
You have seen performance advantages of using **sendfile** and **transferTo()** compared to reading from one channel and writing the same data to another. Frameworks like netty, grizzly, kafka and others rely on zero-copy to have better throughput, lower latency.
