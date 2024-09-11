# Compare between static variable in an funtion, static global variable, and global variable, when use (list all), give example....

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
  - **Storage**: Data Segment (Read-Only) (for initialized variables) or the**BSS segment** (for uninitialized variables).
- **Local Static Variable**: 
  - **Definition**: Maintains its value between function calls.
  - **Lifetime**: Exists until the program ends.
  - **Initialization**: Must be initialized; defaults to 0 if not initialized.


### Constant Variables
- **Old Method (C)**: 
  - **Directive**: `#define` used to create constants.
  - **Issues**: Potential conflicts with multiple definitions across files.
- **New Method (C++)**: 
  - **Keyword**: `const` used to create constant variables.
  - **Characteristics**: Variable value cannot be changed after initialization.
  - **Initialization**: A constant variable must be assigned a value upon creation. the same ease of access as global variables, which can be directly manipulated from multiple locations in the program. In terms of memory, both static and global variables consume memory for the entire runtime of the program. For real-time performance, excessive reliance on global variables can lead to complexity in managing state and ensuring consistency across interrupts or multitasking environments.

# Memory layout:
https://www2.it.uu.se/education/course/homepage/os/vt18/module-0/mips-and-mars/mips-memory-layout/ 

