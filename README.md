# Pipex
Pipex is a project that re-creates the behavior of the shell pipe mechanism in C. It handles the redirection of standard input and output through pipes, allowing the execution of multiple commands where the output of one serves as the input to the next.

## Usage
After compiling the project, run it with an input file, two commands, and an output file:

./pipex infile "ls -l" "wc -l" outfile

The program will execute the first command using the input file as stdin, pipe the result to the second command, and save the final output in the specified file. This is equivalent to:

< infile ls -l | wc -l > outfile

## Available Operations
Command Description
**pipe** Creates a unidirectional data channel for inter-process communication
**fork** Creates a new child process by duplicating the calling process
**dup2** Duplicates a file descriptor to a specific standard stream
**execve** Replaces the current process image with a new process (runs the command)
**access** Checks the user's permissions for a specific file or path
**waitpid** Suspends execution until a child process has changed state

## Algorithm Choice
This project was implemented using a multi-process execution model. The decision to use this architecture was based on several factors:

* **Process Isolation:** By forking separate processes for each command, we ensure that the execution environment for one command does not interfere with the other.
* **Standard UNIX Behavior:** This approach directly mirrors how Bash handles pipelines, ensuring high compatibility with system binaries.
* **Sequential Data Flow:** The use of a pipe ensures a strictly linear flow of data, minimizing memory overhead by not storing command output in strings.
* **Efficient Redirection:** Using dup2 allows for seamless switching between file-based I/O and pipe-based I/O.
* **Path Resolution:** The implementation includes custom path-parsing logic to find executables within the environment variables.



## How It Works
The pipex implementation follows these steps:
1. Parsing and validation: The program checks for the correct number of arguments and verifies file permissions.
2. Pipe initialization: A pipe is created to establish a communication bridge between the child processes.
3. Process creation: Child processes are forked: one for the first command and one for the second.
4. Redirection: In the children, dup2 is used to swap stdin and stdout with the pipe ends and the files.
5. Execution: The execve function is called to find and run the commands using the system's environment path.
6. Cleanup: The parent process closes all file descriptors and waits for the children to terminate.

## Key Learnings
* Implementing shell-like redirections using file descriptors.
* Working with inter-process communication via pipes.
* Managing child processes and handling concurrent execution.
* Environment variable parsing and path resolution in C.
* Handling system call errors and ensuring clean exits.
* Strengthening low-level systems programming skills in C.
