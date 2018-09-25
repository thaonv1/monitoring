# Understanding Linux performance metrics

## Processor metrics
The following are processor metrics:
- CPU utilization

This is probably the most straightforward metric. It describes the overall utilization per
processor. On IBM System x architectures, if the CPU utilization exceeds 80% for a
sustained period of time, a processor bottleneck is likely.

- User time

Depicts the CPU percentage spent on user processes, including nice time. High values in
user time are generally desirable because, in this case, the system performs actual work.

- System time

Depicts the CPU percentage spent on kernel operations including IRQ and softirq time.
High and sustained system time values can point you to bottlenecks in the network and
driver stack. A system should generally spend as little time as possible in kernel time.

- Waiting

Total amount of CPU time spent waiting for an I/O operation to occur. Like the blocked
value, a system should not spend too much time waiting for I/O operations; otherwise you
should investigate the performance of the respective I/O subsystem.

- Idle time

Depicts the CPU percentage the system was idle waiting for tasks.

- Nice time

Depicts the CPU percentage spent on re-nicing processes that change the execution
order and priority of processes.

- Load average

The load average is not a percentage, but the rolling average of the sum of the following:
– The number of processes in queue waiting to be processed
– The number of processes waiting for uninterruptable task to be completed
That is, the average of the sum of TASK_RUNNING and TASK_UNINTERRUPTIBLE
processes. If processes that request CPU time are blocked (which means that the CPU
has no time to process them), the load average will increase. On the other hand, if each
process gets immediate access to CPU time and there are no CPU cycles lost, the load
will decrease.

- Runable processes

This value depicts the processes that are ready to be executed. This value should not
exceed 10 times the amount of physical processors for a sustained period of time;
otherwise a processor bottleneck is likely.

- Blocked

Processes that cannot execute while they are waiting for an I/O operation to finish.
Blocked processes can point you toward an I/O bottleneck.

- Context switch

Amount of switches between threads that occur on the system. High numbers of context
switches in connection with a large number of interrupts can signal driver or application
issues. Context switches generally are not desirable because the CPU cache is flushed
with each one, but some context switching is necessary.

- Interrupts

The interrupt value contains hard interrupts and soft interrupts. Hard interrupts have a
more adverse effect on system performance. High interrupt values are an indication of a
software bottleneck, either in the kernel or a driver. Remember that the interrupt value
includes the interrupts caused by the CPU clock.

## Memory metrics

The following are memory metrics:

- Free memory

Compared to most other operating systems, the free memory value in Linux should not be
a cause for concern. As explained in 1.2.2, “Virtual memory manager” on page 12, the
Linux kernel allocates most unused memory as file system cache, so subtract the amount
of buffers and cache from the used memory to determine (effectively) free memory.

- Swap usage

This value depicts the amount of swap space used. As described in 1.2.2, “Virtual memory
manager” on page 12, swap usage only tells you that Linux manages memory really
efficiently. Swap In/Out is a reliable means of identifying a memory bottleneck. Values
above 200 to 300 pages per second for a sustained period of time express a likely memory
bottleneck.

- Buffer and cache

Cache allocated as file system and block device cache.

- Slabs

Depicts the kernel usage of memory. Note that kernel pages cannot be paged out to disk.


- Active versus inactive memory

Provides you with information about the active use of the system memory. Inactive
memory is a likely candidate to be swapped out to disk by the kswapd daemon.

## Network interface metrics

The following are network interface metrics:

- Packets received and sent

This metric informs you of the quantity of packets received and sent by a given network
interface.

- Bytes received and sent

This value depicts the number of bytes received and sent by a given network interface.

- Collisions per second

This value provides an indication of the number of collisions that occur on the network that
the respective interface is connected to. Sustained values of collisions often concern a
bottleneck in the network infrastructure, not the server. On most properly configured
networks, collisions are very rare unless the network infrastructure consists of hubs.

- Packets dropped

This is a count of packets that have been dropped by the kernel, either due to a firewall
configuration or due to a lack of network buffers.

- Overruns

Overruns represent the number of times that the network interface ran out of buffer space.
This metric should be used in conjunction with the packets dropped value to identify a
possible bottleneck in network buffers or the network queue length.

- Errors

The number of frames marked as faulty. This is often caused by a network mismatch or a
partially broken network cable. Partially broken network cables can be a significant
performance issue for copper-based gigabit networks.

## Block device metrics

The following are block device metrics:

- Iowait

Time the CPU spends waiting for an I/O operation to occur. High and sustained values
most likely indicate an I/O bottleneck.

- Average queue length

Amount of outstanding I/O requests. In general, a disk queue of 2 to 3 is optimal; higher
values might point toward a disk I/O bottleneck.

- Average wait

A measurement of the average time in ms it takes for an I/O request to be serviced. The
wait time consists of the actual I/O operation and the time it waited in the I/O queue.

- Transfers per second

Depicts how many I/O operations per second are performed (reads and writes). The
transfers per second metric in conjunction with the kBytes per second value helps you to identify the average transfer size of the system. The average transfer size generally should match with the stripe size used by your disk subsystem.

- Blocks read/write per second

This metric depicts the reads and writes per second expressed in blocks of 1024 bytes as
of kernel 2.6. Earlier kernels may report different block sizes, from 512 bytes to 4 KB.

- Kilobytes per second read/write

Reads and writes from/to the block device in kilobytes represent the amount of actual data
transferred to and from the block device.
