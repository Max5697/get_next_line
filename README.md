*This project has been created as part of the 42 curriculm by thplemyu.*

## Description

**Get Next Line** is a project at 42 School that challenges students to write a function capable of reading a line ending with a newline caracter form a file descriptor.

In c programming, reading data form a file is a common task, but standard function like `read` retrieve data in fixed-size chunks (buffer). They do not inherently handle "lines" of arbitrary lenght.

This project aims to recreate the behavior of `getline` of `fgets`, allowing the user to read a text file one line at a time, regardless of the buffer size or line lenght.

### Key Goals
* Learn about **Static Variable** in C.
* Understand file desriptor management and the `read` system call.
* Dynamic memory allocation (`malloc`, `free`) to prevent memory leaks.
* Implement a buffer management system.

---

## Usage & Instructions

### Requirements
* GCC or Clang compiler.
* Standard C libraries.

### Integration
to use `get_next_line` in your project, include the header file and compile the `.c` file with your source code.

```c
#include "get_next_line.h"

int  main(void)
{
  int fd;
  char *line;

  fd = open("example.txt", O_RDONLY);
  while ((line = get_next_line(fd)) != NULL)
  {
    printf("%s", line);
    free(line);
  }
  close(fd);
  return(0);
}
```

### Compiletion
you must define the `BUFFER_SIZE` macro at compilation time. This determines how many bytes `read()` retrieves at once.

```Bash
cc -Wall -Wextra -Werror -D BUFFER_SIZE=42 get_next_line.c get_next_line_utils.c main.c -o gnl
```

## Algorithm Explanation & Justification

### Why static Variable?
we chose to use a static variable (`stash`) because regular local variables are destroyed once a functin return.
`get_next_line`needs to "remember" the leftover data (the characters read after `\n`) for the next time the function is called.

### Workflow

1. ***Read and Stash (`ft_read_to_stash`)***
* the function reads from the file descriptor into a temporary buffer of size `BUFFER_SIZE`.
* It appends this buffer to the static `stash` string.
* This loop continues until a newline (`\n`) is found in the stash OR the End Of File (EOF) is reached.
* Justification: This ensures we have at least one full line (or the remainder of the file) stored in memory before processing.

2. ***Extract Line (`ft_get_line`)***
* Once the stash contains a newline, we allocate memory for a new string (`line`).
* We copy characters form the `stash` into `line` up to and including the first `\n`.
* We terminate `line` with `\0` and return it to the user.

3. ***Clean Stash (`ft_new_stash`)***
* After extracting the line, the `stash` still contains the old data (including the line we just returned and potentiall the start of the next line).
* We create a new string containing only the data after the `\n`.
* We `free` the old stash and replace it with this new string.
* Justification: This step is crucial for memory management. It prevents leaks and prepares the status variable for the next function call.

### Memory Management Strategy
* Helper `ft_strjoin`:Customized to free the input string (`s1`) automatically after joining. This reduces the need for manual freeing in the main loop keeps the code cleaner.

## Resources & AI Usage

### References
* Unix Manual: `man read`, `man malloc`, `man free`.
* GeeksforGeeks: Understanding Static Variables in C.

### AI Usage Disclaimer

* Debugging: AI was used to explain specific Memory Leaks reports from Valgrind
