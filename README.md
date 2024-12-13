# xv6


### **System Calls**

#### **1. Gotta count ‘em all** (`getSysCount` and `syscount`)
- **Goal:** Count the number of times a specific system call is executed by a process and its children during the execution of a command.  
- **How:**  
  - Implement `getSysCount(int mask)` to track syscall counts based on a bitmask.
  - Use `syscount <mask> <command> [args]` to run a command and count the specified syscall.
  - Output example:  
    ```
    PID <caller_pid> called <syscall_name> <n> times.
    ```

---

#### **2. Wake me up when my timer ends** (`sigalarm` and `sigreturn`)
- **Goal:** Add functionality to alert a process periodically during its CPU time consumption.
- **How:**  
  - Implement `sigalarm(interval, handler)` to register a user-space function `handler` to be called every `interval` CPU ticks.
  - Add `sigreturn()` to reset the process state after the handler is executed, resuming the program from where it was interrupted.

---

### **Scheduling Policies**

#### **1. Lottery-Based Scheduling (LBS)**  
- **Goal:** Assign CPU time slices randomly based on the number of tickets a process holds.  
- **How:**  
  - Add a system call `settickets(int number)` to set the number of tickets for a process.
  - Processes with the same tickets are prioritized based on arrival time (earlier processes win ties).
  - Child processes inherit parent tickets.
  - Only processes in the `RUNNABLE` state participate in the lottery.

---

#### **2. Multi-Level Feedback Queue (MLFQ)**  
- **Goal:** Implement a priority-based scheduler using four queues with decreasing priorities.  
- **How:**  
  - **Queue Priorities and Time Slices:**  
    - Queue 0: 1 tick  
    - Queue 1: 4 ticks  
    - Queue 2: 8 ticks  
    - Queue 3: 16 ticks  
  - Processes start in the highest priority queue (Queue 0).  
  - Preempt lower-priority processes if higher-priority ones arrive.  
  - Demote processes using their entire time slice to a lower-priority queue.  
  - Processes returning from I/O resume in the same queue.
  - Use round-robin within the lowest priority queue (Queue 3).
  - **Priority Boosting:**  
    - Every 48 ticks, reset all processes to Queue 0 to prevent starvation.

---

### **Additional Notes**
1. **Debugging with `procdump`:**  
   - Extend `procdump` to print the current status of processes, including tickets, queues, and states, for debugging scheduling behavior.

2. **Compilation with Scheduler Flag:**  
   - Use `make clean; make qemu SCHEDULER=<policy>` to compile with either `LBS` or `MLFQ` as the scheduling policy.

---

