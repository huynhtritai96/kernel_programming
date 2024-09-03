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




