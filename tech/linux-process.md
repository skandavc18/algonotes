# What are processes, threads, and file descriptors in Linux?


![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**


**-----------**

When it comes to processes, probably the most common interview question is about the relationship between threads and processes. So here's the answer up front: **in Linux, processes and threads are almost the same thing**.

A process in Linux is just a data structure. Once you understand this, you can grasp the underlying mechanics of file descriptors, redirection, and pipes. At the end we'll look at why, from the OS perspective, threads and processes are basically the same.

### 1. What is a process?

First, abstractly, our computer looks like this:

![](https://labuladong.online/algo/images/linuxProcess/1.jpg)

The big rectangle represents the **memory space** of the computer. The small rectangles inside it are **processes**. The circle in the bottom-left is the **disk**. The shape in the bottom-right represents **input/output devices** like the keyboard, mouse, and monitor. Note the memory space is divided into two halves: **user space** on top and **kernel space** on the bottom.

User space holds the resources user processes need — for example, when you allocate an array in your program, that array lives in user space. Kernel space holds system resources the kernel process loads, and these are usually inaccessible to users. Note that some user processes share certain kernel resources, like dynamically linked libraries.

If we write a `hello` program in C, compile it, and run it on the command line, it prints "hello world" and then exits. From the OS perspective, a new process was created, the executable was loaded into memory, executed, and exited.

**The executable file you compiled is just a file**, not a process. It must be loaded into memory and wrapped in a process to actually run. Processes are created by the OS, and each one has intrinsic attributes — process ID (PID), state, open files, and so on. After the process is created, it loads your program and the system runs it.

So how does the OS create a process? **To the OS, a process is just a data structure**. Let's look at the Linux source directly:

```cpp
struct task_struct {
	// process state
	long			  state;
	// virtual memory struct
	struct mm_struct  *mm;
	// process ID
	pid_t			  pid;
	// pointer to the parent process
	struct task_struct __rcu  *parent;
	// list of child processes
	struct list_head		children;
	// pointer holding file system info
	struct fs_struct		*fs;
	// an array containing pointers to files this process has opened
	struct files_struct		*files;
};
```

`task_struct` is the Linux kernel's description of a process — it can also be called the "process descriptor". The source is fairly complex; I've taken just a small, common subset.

The interesting parts are the `mm` pointer and the `files` pointer. `mm` points to the process's virtual memory — where resources and the executable are loaded. `files` points to an array of pointers to all the files the process has opened.

### 2. What is a file descriptor?

Let's start with `files` — it's an array of file pointers. Generally, a process reads input from `files[0]`, writes output to `files[1]`, and writes errors to `files[2]`.

For example, from our perspective, C's `printf` writes characters to the command line, but from the process's perspective it's just writing data to `files[1]`. Likewise `scanf` is the process trying to read data from the file at `files[0]`.

**When each process is created, the first three slots of `files` are filled with default values pointing to standard input, standard output, and standard error. The "file descriptor" we talk about is just the index into this file-pointer array** — by default 0 is input, 1 is output, 2 is error.

We can redraw the picture:

![](https://labuladong.online/algo/images/linuxProcess/2.jpg)

For a typical computer, the input stream is the keyboard, the output stream is the monitor, and the error stream is also the monitor. So this process has three lines connected to the kernel. Because hardware is managed by the kernel, our processes need to make a "system call" to ask the kernel to access hardware resources.

> [!NOTE]
> Don't forget: in Linux, everything is abstracted as a file — even devices are files you can read from and write to.

If our program needs another resource, say opening a file for reading and writing, that's easy — make a system call so the kernel opens the file, and that file gets placed in the 4th slot of `files`:

![](https://labuladong.online/algo/images/linuxProcess/3.jpg)

With this principle in mind, **input redirection** is easy to understand: when the program wants to read data, it goes to `files[0]`. So if we point `files[0]` at a file, the program will read from that file instead of the keyboard:

```shell
$ command < file.txt
```

![](https://labuladong.online/algo/images/linuxProcess/5.jpg)

Likewise, **output redirection** points `files[1]` to a file, so the program's output goes to that file instead of the monitor:

```shell
$ command > file.txt
```

![](https://labuladong.online/algo/images/linuxProcess/4.jpg)

Error redirection works the same way — no need to repeat.

**Pipes** are conceptually identical: you connect one process's output stream to another process's input stream through a "pipe", and the data flows through it. It's a beautiful design idea:

```shell
$ cmd1 | cmd2 | cmd3
```

![](https://labuladong.online/algo/images/linuxProcess/6.jpg)

You can probably see the elegance of "everything in Linux is a file" by now. Whether it's a device, another process, a socket, or an actual file, all of them can be read and written. They're all packed into a simple `files` array, and the process accesses the right resource through a simple file descriptor. The OS handles the details. Effective decoupling — elegant and efficient.

### 3. What is a thread?

First, we should be clear: both multi-process and multi-thread are forms of concurrency, both of which can improve processor utilization. So the key question now is: what's the difference?

Why do I say threads and processes are basically the same in Linux? Because from the kernel's perspective, Linux doesn't really treat threads and processes differently.

The system call `fork()` creates a new child process; the function `pthread()` creates a new thread. **But both threads and processes are represented by the `task_struct` struct — the only difference is which data regions are shared**.

Put another way, a thread looks no different from a process — it's just that some of a thread's data regions are shared with its parent, while a child process gets a copy instead of sharing. For example, the `mm` and `files` structs are shared in threads. Two diagrams will make this clear:

![](https://labuladong.online/algo/images/linuxProcess/7.jpg)

![](https://labuladong.online/algo/images/linuxProcess/8.jpg)

So our multi-threaded programs need locking to avoid multiple threads writing to the same area at the same time, which would otherwise corrupt the data.

You might ask: **if processes and threads are pretty much the same, and multi-process doesn't share data (so there's no data-race issue), why is multi-thread used so much more than multi-process?**

Because in reality, concurrency with shared data is much more common. For example, ten people simultaneously withdrawing 10 dollars from an account — what we want is for the shared account balance to correctly decrease by 100 dollars, not for each person to get their own copy of the account each of which decreases by 10.

Of course, only Linux treats threads as data-sharing processes without special handling. Many other operating systems do treat threads and processes differently, with threads having their own dedicated data structures. Personally I find that less elegant than Linux's design, and it adds complexity.

In Linux, both creating threads and creating processes are very efficient. For the memory copy problem when creating processes, Linux uses copy-on-write — it doesn't actually copy the parent's memory until a write happens. **So creating processes and threads in Linux is both very fast**.


<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [Pitfalls of Linux pipes](https://labuladong.online/algo/fname.html?fname=linux技巧3)
 - [Tips for using the Linux shell](https://labuladong.online/algo/fname.html?fname=linuxshell)

</details><hr>


**＿＿＿＿＿＿＿＿＿＿＿＿＿**

