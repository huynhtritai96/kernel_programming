# Compare between static variable in an funtion, static global variable, and global variable, when use (list all), give example....
Reference: https://faculty.cs.niu.edu/~freedman/241/241notes/241var2.htm
### Local Variables
- **Definition**: Variables defined within a specific function.
- **Scope**: Only accessible within the function where they are created.
- **Automatic Creation**: Created automatically when the function starts and destroyed when the function ends.
- **Keyword**: `auto` can be used explicitly but is optional as it is the default.
- **Storage**: Stack


### Global Variables and `extern`
- **Definition**: Variables defined outside all functions, accessible from any function.
- **Scope**: Not limited by function scope; exists until the program ends.
- **Cross-File Access**: To use a global variable across different files, declare it in both files with `extern` in the second file.
- **Linkage:** External linkage (visible across translation units)
- **Use Case**: When you want to share a variable across multiple files
- **Lifetime**: Exists until the program ends.
- **Storage**: Data Segment (Read-Only) (for initialized variables) or the **BSS segment** (for uninitialized variables).
### Static Variables
- **Global Static Variable**: 
  - **Definition**: A global variable with restricted access to the file in which it is defined (file scope).
  - **Use Case**: When you want to restrict a variable to one file
  - **Linkage:** Internal linkage (visible only in the same file)
  - **Lifetime**: Exists until the program ends.
  - **Storage**: Data Segment (for initialized variables) or the**BSS segment** (for uninitialized variables).
- **Local Static Variable**: 
  - **Definition**: Maintains its value between function calls.
  - **Lifetime**: Exists until the program ends.
  - **Initialization**: Must be initialized; defaults to 0 if not initialized.


### Constant Variables
  - **Keyword**: `const` used to create constant variables.
  - **Characteristics**: Variable value cannot be changed after initialization.
  - **Initialization**: A constant variable must be assigned a value upon creation. the same ease of access as global variables, which can be directly manipulated from multiple locations in the program. In terms of memory, both static and global variables consume memory for the entire runtime of the program. For real-time performance, excessive reliance on global variables can lead to complexity in managing state and ensuring consistency across interrupts or multitasking environments.
  - **Storage**: Text Segment (Code Segment) (Read-only)
  - 
# Memory layout:
https://www2.it.uu.se/education/course/homepage/os/vt18/module-0/mips-and-mars/mips-memory-layout/ 

Here's a typical memory layout of a C/C++ program, showing how different types of variables are stored in memory. Note that the exact layout can vary depending on the operating system, compiler, and system architecture, but the general structure is quite similar.

### Memory Layout of a C/C++ Program

1. **Text Segment (Code Segment)**
   - **Purpose**: Stores the compiled code of the program (instructions).
   - **Characteristics**: Read-only and Executable.

2. **Data Segment**
   - **Purpose**: Stores global and static variables.
   - **Sub-segments**:
     - **Initialized Data Segment**: 
       - **Purpose**: Stores global and static variables that are explicitly initialized.
       - **Characteristics**: Writable.
     - **BSS Segment**:
       - **Purpose**: Stores global and static variables that are not explicitly initialized or initialized to zero.
       - **Characteristics**: Writable, initialized to zero by default.

3. **Heap**
   - **Purpose**: Stores dynamically allocated memory (e.g., with `malloc`, `calloc`, `new`).
   - **Characteristics**: 
     - Grows towards higher memory addresses as more memory is allocated.
     - Managed manually by the programmer or garbage collector.

4. **Stack**
   - **Purpose**: Stores local variables, function parameters, return addresses, and the call stack.
   - **Characteristics**:
     - Grows towards lower memory addresses as more functions are called.
     - Managed automatically (allocation and deallocation) based on function calls and returns.

### Diagram of Memory Layout

```
+---------------------------+
|        Text Segment        |  <-- Program Code (Read-Only)
+---------------------------+
|   Initialized Data Segment |  <-- Global and Static Variables (Initialized)
+---------------------------+
|           BSS Segment      |  <-- Global and Static Variables (Uninitialized)
+---------------------------+
|           Heap             |  <-- Dynamic Memory Allocation
|                           |
|                           |
+---------------------------+
|           Stack            |  <-- Local Variables, Function Call Management
|                           |
|                           |
+---------------------------+
```
