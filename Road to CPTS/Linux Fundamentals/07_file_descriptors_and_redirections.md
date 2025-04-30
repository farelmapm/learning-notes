# FILE DESCRIPTORS AND REDIRECTIONS

## File Descriptors (FD)
File descriptors (`FD`) is a reference maintained by the kernel that allows the system to manage input/output (`I/O`) operations. It acts as a unique identifier for an open file, socket, or any other I/O resources. In Windows, this is known as a file handle. 

By default, the first three file descriptors are:

- `STDIN - 0` <br>
Data stream for input

- `STDOUT - 1` <br>
Data stream for output

- `STDERR - 2` <br>
Data stream for output that relates to an error occurring. 
