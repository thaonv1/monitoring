# Monitoring tools

## top

The top command shows actual process activity. By default, it displays the most CPU-intensive tasks running on the server and updates the list every five seconds. You can sort the processes by PID (numerically), age (newest first), time (cumulative time), and resident memory usage and time (time the process has occupied the CPU since startup).

You can further modify the processes using renice to give a new priority to each process. If a process hangs or occupies too much CPU, you can kill the process (kill command). The columns in the output are:

- PID Process identification.

- USER Name of the user who owns (and perhaps started) the process.

- PRI Priority of the process. (See 1.1.4, “Process priority and nice level” on page 5 for details.)

- NI Niceness level (Whether the process tries to be nice by adjusting the priority by the number given. See below for details.)

- SIZE Amount of memory (code+data+stack) used by the process in kilobytes.

- RSS Amount of physical RAM used, in kilobytes.

- SHARE Amount of memory shared with other processes, in kilobytes.

- STAT State of the process: S=sleeping, R=running, T=stopped or traced, D=interruptible sleep, Z=zombie.

- %CPU Share of the CPU usage (since the last screen update).

- %MEM Share of physical memory.

- TIME Total CPU time used by the process (since it was started).

- COMMAND Command line used to start the task (including parameters).

The top utility supports several useful hot keys, including:

- t Displays summary information off and on.

- m Displays memory information off and on. A Sorts the display by top consumers of various system resources. Useful for quick identification of performance-hungry tasks on a system.

- f Enters an interactive configuration screen for top. Helpful for setting up top for a specific task.

- o Enables you to interactively select the ordering within top.

- r Issues renice command.

- k Issues kill command.

## vmstat

vmstat provides information about processes, memory, paging, block I/O, traps, and CPU activity. The vmstat command displays either average data or actual samples. The sampling mode is enabled by providing vmstat with a sampling frequency and a sampling duration.

<img src="https://i.imgur.com/LF0YerQ.png">

Note: The first data line of the vmstat report shows averages since the last reboot, so it should be eliminated.

The columns in the output are as follows:
- Process (procs)

  - r: The number of processes waiting for runtime

  - b: The number of processes in uninterruptable sleep

- Memory swpd: The amount of virtual memory used (KB)

  - free: The amount of idle memory (KB)

  - buff: The amount of memory used as buffers (KB)

  - cache: The amount of memory used as cache (KB)

- Swap
  - si: Amount of memory swapped from the disk (KBps)

  - so: Amount of memory swapped to the disk (KBps)

- IO

  - bi: Blocks sent to a block device (blocks/s)

  - bo: Blocks received from a block device (blocks/s)

System

  - in: The number of interrupts per second, including the clock

  - cs: The number of context switches per second

- CPU (% of total CPU time)

  - us: Time spent running non-kernel code (user time, including nice time).

  - sy: Time spent running kernel code (system time).

  - id: Time spent idle. Prior to Linux 2.5.41, this included I/O-wait time.

  - wa: Time spent waiting for IO. Prior to Linux 2.5.41, this appeared as zero.

The vmstat command supports a vast number of command line parameters that are fully documented in the man pages for vmstat. Some of the more useful flags include:

  - m displays the memory utilization of the kernel (slabs)
  - a provides information about active and inactive memory pages
  - n displays only one header line, useful if running vmstat in sampling mode and piping the output to a file. (For example, root#vmstat –n 2 10 generates vmstat 10 times with a sampling rate of two seconds.)

When used with the –p {partition} flag, vmstat also provides I/O statistics.

## uptime

The uptime command can be used to see how long the server has been running and how many users are logged on, as well as for a quick overview of the average load of the server. The system load average is displayed for the past 1minute, 5 minute, and 15 minute intervals. The optimal value of the load is 1, which means that each process has immediate access to the CPU and there are no CPU cycles lost. The typical load can vary from system to system. For a uniprocessor workstation, 1 or 2 might be acceptable, whereas you will probably see values of 8 to 10 on multiprocessor servers.
You can use uptime to pinpoint a problem with your server or the network. For example, if a network application is running poorly, run uptime and you will see whether the system load is high. If not, the problem is more likely to be related to your network than to your server.

## ps and pstree

The `ps` and `pstree` commands are some of the most basic commands when it comes to system analysis. `ps` can have 3 different types of command options, UNIX style, BSD style and GNU style. Here we look at UNIX style options. The `ps` command provides a list of existing processes. The top command shows the process
information, but `ps` will provide more detailed information. The number of processes listed depends on the options used. A simple `ps -A` command lists all processes with their respective process ID (PID) that can be crucial for further investigation.
A PID number is required in order to use tools such as `pmap` or `renice`. On systems running Java™ applications, the output of a `ps -A` command might easily fill up the display to the point where it is difficult to get a complete picture of all running processes. In this case, the `pstree` command might come in handy as it displays the running processes in a tree structure and consolidates spawned subprocesses (for example, Java threads). The `pstree` command can help identify originating processes. There is another ps variant, pgrep. It might be useful as well.

We will look at some useful options for detailed information.

-e All processes. Identical to -A

-l Show long format

-F Extra full mode

-H Forest

-L Show threads, possibly with LWP and NLWP columns

-m Show threads after processes

Here’s an example of the detailed output of the processes using following command:

`ps -elFL`

<img src ="https://i.imgur.com/gjOY535.png">

The columns in the output are:

- F Process flag
- S State of the process: S=sleeping, R=running, T=stopped or traced, D=interruptable sleep, Z=zombie.
- UID Name of the user who owns (and perhaps started) the process.
- PID Process ID number
- PPID Parent process ID number
- LWP LWP(light weight process, or thread) ID of the lwp being reported.
- C Integer value of the processor utilization percentage.(CPU usage)
- NLWP Number of lwps (threads) in the process. (alias thcount).
- PRI Priority of the process.
- NI Niceness level (whether the process tries to be nice by adjusting the priority by the number given; see below for details).
- ADDR Process Address space (not displayed)
- SZ Amount of memory (code+data+stack) used by the process in kilobytes.
- WCHAN Name of the kernel function in which the process is sleeping, a “-” if the process is running, or a “* ” if the process is multi-threaded and ps is not displaying threads. RSS Resident set size, the non-swapped physical memory that a task has used (in kiloBytes).
- PSR Processor that process is currently assigned to.
- STIME Time the command started.
- TTY Terminal
- TIME Total CPU time used by the process (since it was started).
- CMD Command line used to start the task (including parameters).

### Thread information

You can see the thread information using ps -L option.

<img src="https://i.imgur.com/8tlT8KO.png">

## free

The command /bin/free displays information about the total amount of free and used memory (including swap) on the system. It also includes information about the buffers and cache used by the kernel.

When using free, remember the Linux memory architecture and the way the virtual memory manager works. The amount of free memory is of limited use, and the pure utilization statistics of swap are not an indication of a memory bottleneck.

<img src="https://i.imgur.com/9IMxgWG.png">

Useful parameters for the free command include:
-b, -k, -m, -g Display values in bytes, kilobytes, megabytes, and gigabytes

-l Distinguishes between low and high memory

-c <count> Displays the free output <count> number of times

### Memory used in a zone

Using the -l option, you can see how much memory is used in each memory zone. Notice that 64-bit systems no longer use high memory.

## iostat

The `iostat` command shows average CPU times since the system was started (similar to uptime). It also creates a report of the activities of the disk subsystem of the server in two parts: CPU utilization and device (disk) utilization. The iostat utility is part of the sysstat package

The CPU utilization report has four sections:

`%user` Shows the percentage of CPU utilization that was taken up while executing at the user level (applications).

`%nice` Shows the percentage of CPU utilization that was taken up while executing at the user level with a nice priority.

`%sys` Shows the percentage of CPU utilization that was taken up while executing at the system level (kernel).

`%idle` Shows the percentage of time the CPU was idle.

The device utilization report has these sections:

`Device` The name of the block device.

`tps` The number of transfers per second (I/O requests per second) to the device. Multiple single I/O requests can be combined in a transfer request, because
a transfer request can have different sizes.

`Blk_read/s, Blk_wrtn/s`

Blocks read and written per second indicate data read from or written to the device in seconds. Blocks can also have different sizes. Typical sizes are
1024, 2048, and 4048 bytes, depending on the partition size.

`Blk_read, Blk_wrtn`
Indicates the total number of blocks read and written since the boot.

The `iostat` can use many options. The most useful one is -x option from the performance
perspective. It displays extended statistics. The following is sample output.

<img src="https://i.imgur.com/LGGbdxS.png">

`rrqm/s, wrqm/s`

The number of read/write requests merged per second that were issued to the device. Multiple single I/O requests can be merged in a transfer request, because a transfer request can have different sizes.

`r/s, w/s` The number of read/write requests that were issued to the device per second.

`rsec/s, wsec/s` The number of sectors read/write from the device per second.

`rkB/s, wkB/s` The number of kilobytes read/write from the device per second.

`avgrq-sz` The average size of the requests that were issued to the device. This value is displayed in sectors.

`avgqu-sz` The average queue length of the requests that were issued to the device.

`await` Shows the percentage of CPU utilization that was used while executing at the system level (kernel).

`svctm` The average service time (in milliseconds) for I/O requests that were issued to the device.

`%util` Percentage of CPU time during which I/O requests were issued to the device (bandwidth utilization for the device). Device saturation occurs when this
value is close to 100%.

## sar

The `sar` command is used to collect, report, and save system activity information. The `sar` command consists of three applications: sar, which displays the data, and `sa1` and `sa2`, which are used for collecting and storing the data. The `sar` tool features a wide range of options so be sure to check the man page for it. The sar utility is part of the sysstat package.

With `sa1` and `sa2`, the system can be configured to get information and log it for later analysis.

To accomplish this, add the lines to /etc/crontab (Example 2-14). Keep in mind that a default
cron job running sar daily is set up automatically after installing sar on your system.

Example of starting automatic log reporting with cron

```
# 8am-7pm activity reports every 10 minutes during weekdays.
*/10 8-18 * * 1-5 /usr/lib/sa/sa1 600 6 &
# 7pm-8am activity reports every an hour during weekdays.
0 19-7 * * 1-5 /usr/lib/sa/sa1 &
# Activity reports every an hour on Saturday and Sunday.
0 * * * 0,6 /usr/lib/sa/sa1 &
# Daily summary prepared at 19:05
5 19 * * * /usr/lib/sa/sa2 -A &
```

The raw data for the sar tool is stored under /var/log/sa/ where the various files represent the days of the respective month. To examine your results, select the weekday of the month and the requested performance data.

<img src="https://i.imgur.com/m7HjJxk.png">

## mpstat

The `mpstat` command is used to report the activities of each of the available CPUs on multiprocessor server. Global average activities among all CPUs are also reported. The `mpstat` utility is part of the sysstat package. The `mpstat` utility enables you to display overall CPU statistics per system or per processor. `mpstat` also enables the creation of statistics when used in sampling mode analogous to the `vmstat` command with a sampling frequency and a sampling count.

<img src="https://i.imgur.com/yzeRqHm.png">


## pmap

The pmap command reports the amount of memory that one or more processes are using. You can use this tool to determine which processes on the server are being allocated memory and whether this amount of memory is a cause of memory bottlenecks. For detailed information, use pmap -d option.

`pmap -d <pid>`

<img src="https://i.imgur.com/Pj8VU5o.png">

Some of the most important information is at the bottom of the display. The line shows:
- mapped: total amount of memory mapped to files used in the process

- writable/private: the amount of private address space this process is taking

- shared: the amount of address space this process is sharing with others

You can also look at the address spaces where the information is stored. You can find an interesting difference when you issue the pmap command on 32-bit and 64-bit systems. For the complete syntax of the pmap command, issue:

`pmap -?`

## netstat

`netstat` is one of the most popular tools. If you work on the network. you should be familiar with this tool. It displays a lot of network related information such as socket usage, routing, interface, protocol, network statistics, and more. Here are some of the basic options:

`-a` Show all socket information

`-r` Show routing information

`-i` Show network interface statistics

`-s` Show network protocol statistics

<img src="https://i.imgur.com/9ioVmRk.png">

### Socket information

`Proto` The protocol (tcp, udp, raw) used by the socket.

`Recv-Q` The count of bytes not copied by the user program connected to this socket.

`Send-Q` The count of bytes not acknowledged by the remote host.

`Local Address` Address and port number of the local end of the socket. Unless the --numeric (-n) option is specified, the socket address is resolved to its canonical host name (FQDN), and the port number is translated into the corresponding service name.

`Foreign Address` Address and port number of the remote end of the socket.

`State` The state of the socket. Since there are no states in raw mode and usually no states used in UDP, this column may be left blank.

## iptraf

`iptraf` monitors TCP/IP traffic in a real time manner and generates real time reports. It shows TCP/IP traffic statistics by each session, by interface, and by protocol. The iptraf utility is provided by the iptraf package.

The iptraf give us reports like the following:

- IP traffic monitor: Network traffic statistics by TCP connection

- General interface statistics: IP traffic statistics by network interface

- Detailed interface statistics: Network traffic statistics by protocol

- Statistical breakdowns: Network traffic statistics by TCP/UDP port and by packet size

- LAN station monitor: Network traffic statistics by Layer2 address

Following are a few of the reports iptraf generates.

`yum install iptraf`

`iptraf-ng -i interface_name_here`

<img src="https://i.imgur.com/IVwKILA.png">

## tcpdump / ethereal

The `tcpdump` and `ethereal` are used to capture and analyze network traffic. Both tool use the `libpcap` library to capture packets. They monitor all the traffic on a network adapter with promiscuous mode and capture all the frames the adapter has received. To capture all the packets, these commands should be executed with super user privilege to make the interface
promiscuous mode. You can use these tools to dig into the network related problems. You can find TCP/IP retransmission, windows size scaling, name resolution problem, network misconfiguration, and more. Just keep in mind that these tools can monitor only frames the network adapter has received, not entire network traffic.

## nmon

`nmon`, short for Nigel's Monitor, is a popular tool to monitor Linux systems performance developed by Nigel Griffiths. Since `nmon` incorporates the performance information for several subsystems, it can be used as a single source for performance monitoring. Some of the tasks that can be achieved with `nmon` include processor utilization, memory utilization, run queue information, disks I/O statistics, network I/O statistics, paging activity, and process metrics.
In order to run `nmon`, simply start the tool and select the subsystems of interest by typing their one-key commands. For example, to get CPU, memory, and disk statistics, start nmon and type c m d.
A very useful feature of `nmon` is the ability to save performance statistics for later analysis in a comma separated values (CSV) file. The CSV output of `nmon` can be imported into a spreadsheet application in order to produce graphical reports. In order to do so `nmon` should be started with the -f flag (see `nmon -h` for the details). For example running `nmon` for an hour capturing data snapshots every 30 seconds would be achieved using the command

`nmon -f -s 30 -c 120`

## strace

The `strace` command intercepts and records the system calls that are called by a process, and the signals that are received by a process. This is a useful diagnostic, instructional, and debugging tool. System administrators find it valuable for solving problems with programs.

To trace a process, specify the process ID (PID) to be monitored:

`strace -p <pid>`

**Attention**: While the strace command is running against a process, the performance of the PID is drastically reduced and should only be run for the time of data collection.

Here’s another interesting use. This command reports how much time has been consumed in the kernel by each system call to execute a command.

`strace -c <command>`

<img src="https://i.imgur.com/b7TMuSJ.png">

## Proc file system

The proc file system is not a real file system, but nevertheless it is extremely useful. It is not intended to store data; rather, it provides an interface to the running kernel. The proc file system enables an administrator to monitor and change the kernel on the fly. Most Linux tools for performance measurement rely on the information provided by /proc.

<img src="https://i.imgur.com/y6YDH1v.png">

Looking at the proc file system, we can distinguish several subdirectories that serve various purposes, but because most of the information in the proc directory is not easy for people to read, you are encouraged to use tools such as `vmstat` to display the various statistics in a more readable manner. Keep in mind that the layout and information contained within the proc file system varies across different system architectures.

### Files in the /proc directory

The various files in the root directory of proc refer to several pertinent system statistics.

Here you can find information taken by Linux tools such as vmstat and cpuinfo as the source of their output.

- Numbers 1 to X

The various subdirectories represented by numbers refer to the running processes or their respective process ID (PID). The directory structure always starts with PID 1, which refers to the init process, and goes up to the number of PIDs running on the respective system. Each numbered subdirectory stores statistics related to the process. One example of such data is the virtual memory mapped by the process.

- acpi

ACPI refers to the advanced configuration and power interface supported by most modern desktop and notebook systems. Because ACPI is mainly a PC technology, it is often disabled on server systems. For more information about ACPI refer to:
http://www.apci.info

- bus

This subdirectory contains information about the bus subsystems such as the PCI bus or the USB interface of the respective system.

- irq

The irq subdirectory contains information about the interrupts in a system. Each subdirectory in this directory refers to an interrupt and possibly to an attached device such as a network interface card. In the irq subdirectory, you can change the CPU affinity of a given interrupt.

- net

The net subdirectory contains a significant number of raw statistics regarding your network interfaces, such as received multicast packets or the routes per interface.

- scsi

This subdirectory contains information about the SCSI subsystem of the respective system, such as attached devices or driver revision. The subdirectory ips refers to the IBM ServeRAID controllers found on most IBM System x servers.

- sys

In the sys subdirectory you find the tunable kernel parameters such as the behavior of the virtual memory manager or the network stack.

-  tty

The tty subdirectory contains information about the respective virtual terminals of the systems and to what physical devices they are attached.
