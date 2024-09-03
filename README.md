# kernel_programming
# NOTE: You -> Userspace (application/Software) --> Shell --> Kernel --> Hardware(Pheriperal)

Reference: https://www.codingame.com/playgrounds/84444/running-u-boot-linux-kernel-in-qemu/pre-requisites 


### 1. **Process Management (CPU)**
   - **Purpose:** The kernel manages the **execution of processes**, including **multitasking**, **process creation**, **termination**, and **context switching** between processes.
   - **Details:** It allocates CPU time to processes and handles **process scheduling**, ensuring that multiple processes can run simultaneously without interfering with each other.

### 2. **Memory Management (RAM, HDD)**
   - **Purpose:** The kernel controls the system's memory, managing how memory is allocated to processes and ensuring efficient use of RAM.
   - **Details:** It manages **virtual memory**, **page swapping**, and **memory protection**, making sure each process has its own memory space and that no process can access another process's memory.

### 3. **Device Management (hard drives, printers, network interface)**
   - **Purpose:** The kernel manages **communication between the system** and its **hardware devices**, such as **hard drives**, **printers**, and **network interfaces**.
   - **Details:** It includes drivers that provide the necessary instructions for controlling hardware devices and handles I/O operations, ensuring that data is correctly transferred between devices and the system.

### 4. **File System Management**
   - **Purpose:** The kernel **organizes and manages data storage on disk drives** and **handles file operations** like **reading**, **writing**, and **deleting** files.
   - **Details:** It ensures that files are stored and retrieved efficiently, and it provides **file security and permissions** to control access.
NOTE: need a mind map
### 5. **System Calls and Security**
   - **Purpose:** The kernel provides an interface for user applications to request services from the OS, such as file operations or network communication.
   - **Details:** It also enforces security policies, ensuring that only authorized users and processes can access certain resources or perform specific actions.



Here’s a more detailed, technical look at each function of the kernel, including common command-line utilities and system call signatures relevant to each section:

### 1. **Process Management**
   - **System Call Example:**
     - `fork()` - Creates a new process by duplicating the calling process.
     - `exec()` - Replaces the current process image with a new process image.
     - `wait()` - Makes the parent process wait until its child process terminates.
   - **Command-Line Utilities:**
     - `ps` - Displays information about currently running processes.
     - `top` - Shows real-time information about system processes and resource usage.
     - `kill` - Sends a signal to a process, usually to terminate it.
     - `nice` / `renice` - Adjusts the priority of a process.
   - **Code Snippet (C):**
     ```c
     pid_t pid = fork();
     if (pid == 0) {
         // Child process
         execl("/bin/ls", "ls", NULL);
     } else if (pid > 0) {
         // Parent process
         wait(NULL);
     }
     ```

### 2. **Memory Management**
   - **System Call Example:**
     - `mmap()` - Maps files or devices into memory.
     - `brk()` - Changes the end of the data segment, effectively allocating more memory.
     - `munmap()` - Unmaps a previously mapped memory region.
   - **Command-Line Utilities:**
     - `free` - Displays the amount of free and used memory in the system.
     - `vmstat` - Reports information about processes, memory, paging, block IO, traps, and CPU activity.
     - `pmap` - Reports memory map of a process.
   - **Code Snippet (C):**
     ```c
     void *ptr = mmap(NULL, 4096, PROT_READ | PROT_WRITE, MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);
     if (ptr == MAP_FAILED) {
         perror("mmap failed");
     }
     ```

### 3. **Device Management**
   - **System Call Example:**
     - `ioctl()` - Device-specific input/output operations.
     - `read()` - Reads data from a file descriptor.
     - `write()` - Writes data to a file descriptor.
     - `open()` - Opens a file descriptor for a device.
     - `close()` - Closes a file descriptor.
   - **Command-Line Utilities:**
     - `lsblk` - Lists information about block devices.
     - `dmesg` - Prints or controls the kernel ring buffer (usually logs related to device activity).
     - `hdparm` - Gets and sets hard disk parameters.
     - `lspci` - Lists all PCI devices.
   - **Code Snippet (C):**
     ```c
     int fd = open("/dev/sda", O_RDONLY);
     if (fd < 0) {
         perror("Failed to open device");
     }
     // Read data from the device
     read(fd, buffer, sizeof(buffer));
     close(fd);
     ```

### 4. **File System Management**
   - **System Call Example:**
     - `open()` - Opens a file and returns a file descriptor.
     - `read()` - Reads data from a file descriptor.
     - `write()` - Writes data to a file descriptor.
     - `close()` - Closes a file descriptor.
     - `stat()` - Retrieves file status information.
     - `unlink()` - Deletes a file.
   - **Command-Line Utilities:**
     - `ls` - Lists directory contents.
     - `df` - Reports file system disk space usage.
     - `du` - Estimates file space usage.
     - `mount` / `umount` - Mounts and unmounts filesystems.
   - **Code Snippet (C):**
     ```c
     int fd = open("file.txt", O_RDONLY);
     if (fd < 0) {
         perror("Failed to open file");
     }
     struct stat file_stat;
     if (fstat(fd, &file_stat) < 0) {
         perror("Failed to get file status");
     }
     close(fd);
     ```

### 5. **System Calls and Security**
   - **System Call Example:**
     - `getuid()` - Returns the real user ID of the calling process.
     - `setuid()` - Sets the user ID of the calling process.
     - `chmod()` - Changes the file mode bits (permissions) of a file.
     - `chown()` - Changes the owner and group of a file.
     - `capget()` / `capset()` - Manipulates capabilities of the calling process (Linux-specific).
   - **Command-Line Utilities:**
     - `chmod` - Changes the file permissions.
     - `chown` - Changes the file owner and group.
     - `sudo` - Executes a command as another user, typically root.
     - `iptables` - Configures the Linux kernel firewall.
   - **Code Snippet (C):**
     ```c
     uid_t uid = getuid();
     printf("Current User ID: %d\n", uid);

     if (setuid(0) < 0) {
         perror("Failed to change user ID");
     }
     ```

These commands, system calls, and code snippets illustrate the kernel's role in managing processes, memory, devices, file systems, and security.
