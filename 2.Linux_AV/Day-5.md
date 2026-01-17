## Linux Zero to Hero Series – Episode 5: Process Management, Monitoring, Networking, and Disk Management

### Overview 🖥️
In this episode, Abhishek covers four critical sections of Linux administration: process management, system monitoring, networking fundamentals, and disk management. The tutorial demystifies core Linux concepts essential for administrators and DevOps engineers, focusing especially on how processes run and compete for resources, how to monitor system performance efficiently, the importance of networking as a foundation for connectivity, and handling storage volumes effectively. Abhishek uses clear examples, practical commands, and analogies to explain these interconnected concepts, while sharing useful resources for deeper learning.

### Summary of Core Knowledge Points ⏱️

- **00:00 - 02:45: Introduction to Process Management**  
  - A process in Linux is any running instance of a program (e.g., Python application, shell script).  
  - The operating system acts as an intermediary between software and hardware, managing CPU, memory, file system, and network resources.  
  - CPU and memory intensive processes can dominate hardware resources, affecting the performance of others. Example: a CPU-heavy Python ML program consuming 90% CPU leaves only 10% for other processes.  

- **02:45 - 07:30: Process Priority and Resource Allocation**  
  - Linux CPU scheduling algorithm prioritizes processes based on certain criteria similar to a doctor prioritizing ICU patients.  
  - Administrators can view running processes, kill, stop, resume, and change priority of processes to manage resource allocation.  
  - Process management involves these activities to maintain system stability.

- **07:30 - 15:00: Viewing and Understanding Running Processes**  
  - Use `ps aux` to list all running processes with details like user, process ID (PID), CPU, and memory usage.  
  - The difference between `ps aux` and `ps -ef`: `ps aux` shows memory utilization while `ps -ef` does not.  
  - Columns explained: user who started the process, PID (unique identifier), CPU/memory usage, start time, and command name.

- **15:00 - 23:40: Killing, Stopping, and Resuming Processes**  
  - To kill a process: `kill PID`. If unresponsive, force kill with `kill -9 PID`.  
  - For thread dumps (especially Java apps), use `kill -3 PID`.  
  - Temporarily stop processes using `kill -STOP PID` and resume with `kill -CONT PID`.  
  - Use grep to filter and find specific processes (e.g., `ps aux | grep java`).

- **23:40 - 28:30: Prioritizing and Deprioritizing Processes Using renice**  
  - The `renice` command adjusts process priority from -20 (highest) to +19 (lowest).  
  - Example: `renice -n 10 -p PID` lowers priority; `renice -n -5 -p PID` raises priority.  
  - Caution: Only use these commands if you understand running processes and own the machine.

- **28:30 - 34:50: Special Processes – Services**  
  - Services are background processes that start automatically at boot (e.g., Apache, Nginx web servers).  
  - They differ from regular processes which do not auto-start after reboot.  
  - Managing services is done via `systemctl` commands: list (`systemctl list-units --type=service`), start, stop services.

- **34:50 - 40:40: Monitoring Linux Systems**  
  - Monitoring tools help identify resource-intensive processes, CPU, memory, and disk usage.  
  - Key commands:  
    - `top` for real-time CPU and memory usage display, dynamically updates output.  
    - `htop`, an enhanced top with graphical interface and better usability.  
    - `vmstat` reports system performance metrics useful to debug slow systems.  
    - `free -h` shows free and used memory in human-readable format.  
    - `nproc` returns number of CPUs.

- **40:40 - 46:15: Checking Disk Usage and Folder Sizes**
