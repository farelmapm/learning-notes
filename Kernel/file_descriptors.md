### FILE DESCRIPTORS

File Descriptors (fd) is a non-negative integer (or more specifically, [handle]()) that represents an index in a process's [file descriptor table](<>). This index points to an entry in the file descriptor table that contains metadata for a file or other I/O resource (pipe or network sockets) like file offset, access mode, etc and a reference to the [inode]() or device. 

For example, when a process opens a file, the kernel assigns a unique integer as a file descriptor. This integer is unique within that process and can be used to identify which file is being used. A different process can open or create a new file that gets assigned the same file descriptor integer as another process' file descriptor integer while pointing at a different file altogether. 

The most common ways for processes to obtain a file descriptor is through the [`open`](</home/farel/Documents/Personal Projects/Learning Notes/C/c_open.md>) or [`creat`](</home/farel/Documents/Personal Projects/Learning Notes/C/c_creat.md>) function or through inheritance from the parent process. When a process creates a children process through [`fork`](</home/farel/Documents/Personal Projects/Learning Notes/C/c_fork.md>) for example, the descriptor table is copied from the parent process to the child process, meaning that the child process has the same access to the file descriptors assigned on the parent process.

File Descriptors are part of the [POSIX API](). 

Each process should have three standard POSIX file descriptors which are assigned to the standard [streams]():
| Integer Value | Name           | <unistd.h> symbolic constant | <stdio.h> file stream|
| --------------| -------------- | -----------------------------| ---------------------|
| 0             | Standard Input | STDIN_FILENO       | stdin                 |
| 1             | Standard Output| STDOUT_FILENO      | stdout                |
| 2             | Standard Error | STDERR_FILENO      | stderr                |


