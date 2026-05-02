Student Name: [abdullah fahad algemlas]
Student ID: [445050192]
Date Submitted: [2026/5/2]

🎥 VIDEO DEMONSTRATION LINK (REQUIRED)
⚠️ IMPORTANT: This section is REQUIRED for grading!

Upload your 3-5 minute video to your PERSONAL Gmail Google Drive (NOT university email). Set sharing to "Anyone with the link can view". Test the link in incognito/private mode before submitting.

Video Link: [https://drive.google.com/file/d/19BAvKLK4E78x8PQyvoltk1jDy0E26_ve/view?usp=sharing]

Video filename: [YourStudentID]_Assignment3_Synchronization.mp4

Verification:

 Link is accessible (tested in incognito mode)
 Video is 3-5 minutes long
 Video shows code walkthrough and commits
 Video has clear audio
 Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)
 -------------------------------------------------------------------
Part 1: Development Log (1 mark)
Document your development process with minimum 3 entries showing progression:

Entry 1 - [2026/5/1, 8pm]
What I implemented:
I configured the GitHub login to complete the assignment.
Challenges encountered:
I had difficulty logging in because I didn't receive a verification email.
How I solved it:
I verified via my phone number, not my email.
Testing approach:
I logged into the site after checking and the assignment appeared.
Time spent:
30 minute
Entry 2 - [2026/5/1, 9.30 pm]
What I implemented:
I reviewed the assignment to understand the requirements.
Challenges encountered:
Understanding the synchronization method and how to apply it with the code
How I solved it:
I reviewed Unit 5 to learn how to apply it to the code.
Testing approach:
I applied a code found in the module to NetBeans
Time spent:
1.5 hours
Entry 3 - [2026/5/2, 1 pm]
What I implemented:
solving the code
Challenges encountered:
Add a ReentrantLock(s) and Add a Semaphore to limit concurrent process and give me erorr
How I solved it:
add import java.util.concurrent.Semaphore;
import java.util.concurrent.locks.ReentrantLock;
Testing approach:
visual studio code 
Time spent:
1 hours 
-------------------------------------------------------------------------
Part 2: Technical Questions (1 mark)
Question 1: Race Conditions
Q: Identify and explain TWO race conditions in the original code. For each:

What shared resource is affected?
Why is concurrent access a problem?
What incorrect behavior could occur?
Your Answer:
The affected sources are contextSwitchCount and completedProcessCount. Why is concurrent access a problem?
Because it gives me incorrect data and causes a race condition issue, and many process threads can update them at the same time. What incorrect behavior could occur?
It's possible that it might give me different data because it could be recording old data with the same values.
Question 2: Locks vs Semaphores
Q: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

Your Answer:

[Your answer here - The main difference between ReentrantLock and Semaphore is that a ReentrantLock allows only one thread to enter a critical section at a time  and take boolean variable  takes two opeartions acquire() and release(), while a Semaphore allows a limited number of threads to access a resource based on the number of permits and takes a integer variable takes two opeartions wait() and singal() 
In my code, I used ReentrantLock to protect shared variables such as contextSwitchCount and totalWaitingTimeLock, because updates to these resources must be synchronized to avoid race conditions and incorrect values
I used a Semaphore (cpuSemaphore) to control access to the CPU, allowing only one process to execute at a time]
----------------------------------------------------------------------------------
Question 3: Deadlock Prevention
Q: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.
[Your answer here - Deadlock is a situation where two or more threads are blocked forever because each one is waiting for a resource held by another thread, so none of them can continue execution.
One prevention technique is using try-finally blocks to always release locks or semaphores. In my code, I used try { ..lock. } finally { unlock(); } and finally { cpuSemaphore.release(); }, which guarantees that the resource is released even if an exception occurs, preventing threads from being stuck waiting.
The second technique is avoiding circular wait by using a consistent locking strategy. In my implementation, each shared resource has its own independent ReentrantLock, and locks are held for a very short time (only around the critical section). This reduces the chance of threads waiting on each other in a cycle.]
Question 4: Lock Granularity Design Decision
Q: For Task 1 (protecting the three counters), explain your lock design choice:

Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
Explain WHY you made this choice
What are the trade-offs between the two approaches?
Given that the three counters are independent, which approach provides better concurrency and why?
Your Answer:
i used Option B: Fine-Grained Locking (Separate Lock per Counter) beacause its , Higher concurrency - Multiple threads can update different counters simultaneously
 Better performance - Less waiting, more parallelism
 Industry best practice - Fine-grained locking when resources are independent
 Demonstrates deeper understanding of synchronization concepts
the trade offs is Option A , Simple, easier to manage but Unnecessary contention - 
Option B The features I mentioned and Cons: Slightly more code to write (but worth it!) 
Given that the three counters are independent, which approach provides better concurrency and why?
Option B because  If Thread A is incrementing contextSwitchCount, Thread B must wait even though it only wants to increment completedProcessCount. The counters are independent, so this creates artificial bottleneck!

-----------------------------------------------------------------------------------------------------------------------
Part 3: Synchronization Analysis (1 mark)
Critical Section #1: Counter Variables
Which variables:
completedProcessCountLock
Why they need protection:
to avoid race condition 
Synchronization mechanism used:
mutex locks 
Code snippet:
       completedProcessCountLock.lock();
        try {
            completedProcessCount++;
        } finally {
            completedProcessCountLock.unlock();
        }
    }

// Paste your implementation here
Justification:
This critical section needs synchronization because completedProcessCount is a shared variable accessed and modified by multiple threads at the same time. The increment operation (completedProcessCount++) is not atomic
Using a mutex lock ensures that only one thread can enter this section at a time, guaranteeing correct and consistent updates to the counter The use of a try-finally block is important because it ensures the lock is always released, even if an exception occurs, preventing deadlocks 
Critical Section #2: Execution Log
What resource:
executionLog
Why it needs protection:
to avoid race condition 
Synchronization mechanism used:
mutex locks 
Code snippet:
       logLock.lock();
        try {
            executionLog.add(message);
        } finally {
            logLock.unlock();
        }
    }
}
// Paste your implementation here
Justification:
The executionLog is a shared ArrayList that is accessed by multiple threads concurrently. Since ArrayList is not thread safe, simultaneous calls to executionLog.add(message) can lead to race conditions 
Using mutex lock ensures that only one thread can modify the list at a time, preserving data integrity and preventing corruption The try finally block guarantees that the lock is always released after the operation, even if an exception occurs,
Critical Section #3: CPU Semaphore
Purpose of semaphore:
The semaphore is used to control access to the CPU resource and ensure that only a limited number of threads can execute at the same time.
Number of permits and why:
1 because its one thread can access cpu at same time 
Where implemented:
It is implemented in the run() and runToCompletion() methods using cpuSemaphore.acquire() before execution and cpuSemaphore.release() in the finally block after execution.
Code snippet:
      try {
            SharedResources.cpuSemaphore.acquire();
        try {
            if (startTime == -1) {
                startTime = System.currentTimeMillis();
            }
             } finally {
            // Always release in finally block to prevent deadlocks!
            SharedResources.cpuSemaphore.release();
            }
        } catch (InterruptedException e) {
            System.out.println(Colors.RED + "  ✗ " + name + " semaphore interrupted." + Colors.RESET);
        }
    }
// Paste your implementation here
Effect on program behavior:
The semaphore ensures that processes execute one at a time, preventing overlapping execution. This maintains correct scheduling behavior, avoids conflicts on the CPU
----------------------------------------------------------------------------------------------------------------
Part 4: Testing and Verification (2 marks)
Test 1: Consistency Check
What I tested: Running program multiple times to verify consistent results

Testing procedure:
# In Visual Studio Code terminal
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
# Commands used (run the program at least 5 times)
Results: (Show that running multiple times produces consistent, correct results)
I ran the program five times using the terminal in Visual Studio Code. In each run, the program completed successfully, and the final statistics were consistent
Total Completed Processes: 14 matched the total number of processes 
Why synchronization is necessary:
Synchronization is required because several threads can access shared resources at the same time. completedProcessCount, contextSwitchCount, and totalWaitingTime could produce incorrect values if two threads update them simultaneously. Also, executionLog needs protection because ArrayList is not thread-safe. Without synchronization, the program could lose updates, show wrong create inconsistent log entries.

Conclusion:
This test confirms that the synchronization mechanisms used in the code work correctly. The ReentrantLock protects shared data, while the Semaphore controls CPU access so only one process runs at a time.
Test 2: Exception Testing
What I tested: Checking for ConcurrentModificationException
Testing procedure:
I executed the program several times and focused on scenarios where multiple threads write to the executionLog at the same time. I verified that each logging operation is protected using logLock.lock() and released with unlock() inside a finally block.
Results:
No ConcurrentModificationException or other errors occurred during any run. The program executed normally, and all log entries were recorded correctly without any missing or corrupted data.
What this proves:
This confirms that the synchronization mechanism using mutex locks  is working correctly. It ensures that only one thread accesses the executionLog at a time, preventing concurrent modification
Test 3: Correctness Verification
What I tested: Verifying correct final values (total burst time, context switches, etc.)

Expected values:
The expected outcome is that all processes finish execution, so the completed process count should equal the total number of created processes. The context switch counter should reflect how many times the CPU was assigned, and the total/average waiting time should be calculated correctly based on process timings.
Actual values:
The program produced correct and consistent results. The number of completed processes matched the total processes
Analysis:
The results confirm that the synchronization is functioning properly. The use of ReentrantLock prevents incorrect updates to shared counters, and the Semaphore ensures controlled CPU access. As a result, the program maintains accurate statistics and behaves correctly in a multithreaded environment.
Test 4: Different Scenarios
Scenario tested: [e.g., different time quantum, more processes, etc.]
I tested the program with different time quantum values and a higher number of processes.
Purpose:
The goal was to analyze how these changes impact CPU scheduling, especially in terms of context switches and waiting time
Results:
With a smaller time quantum, the number of context switches increased, and processes were interrupted more often. With more processes, the overall waiting time increased. Despite these changes, the program ran correctly
What I learned:
I learned that the choice of time quantum significantly affects system performance. Smaller values improve responsiveness but increase overhead, while larger values reduce switching but may delay some processes
----------------------------------------------------------------------------------------------------------------------
Part 5: Reflection and Learning
What I learned about synchronization:
[6-8 sentences about key concepts, challenges, insights]
I learned that synchronization is crucial for ensuring correctness in multithreaded programs. When multiple threads share data, race conditions can easily occur if access is not controlled properly. I realized that even simple operations like updating a counter can cause errors if executed concurrently without protection. Using tools like mutex locks helps ensure that only one thread accesses a critical section at a time, while Semaphore is useful for limiting access to shared resources such as the CPU.

I also understood the importance of always releasing locks using try-finally blocks to avoid deadlocks and ensure smooth execution. Another key insight is that synchronization should be applied only where necessary to avoid reducing performance. Keeping critical sections small improves efficiency while still maintaining safety
Real-world applications:
Give TWO examples where synchronization is critical:
Example 1:
Online shopping systems – when multiple users try to buy the same product at the same time, synchronization ensures that the stock quantity is updated correctly and prevents overselling.
Example 2:
File systems – when multiple programs try to read/write to the same file, synchronization is required to prevent data corruption and ensure the file content remains consistent.
How I would explain synchronization to others:
I would explain synchronization using a CPU scheduling example with processes and context switching. Imagine the CPU is like a single worker, and multiple processes are waiting to use it. Since the CPU can only handle one process at a time, it switches between them using context switching.
Now, if there were no synchronization, two processes might try to use the CPU at the same time, causing conflicts and incorrect execution. Synchronization acts like a control system that ensures only one process uses the CPU at a time
---------------------------------------------------------------------------------------------------------------------
Part 6: GitHub Repository Information
Repository URL:
https://github.com/Abdullah-fahad-192/OS-Assignment3-Abdullah-algemlas/blob/main/ASSIGNMENT_DOCUMENTATION.md
Number of commits:
8
Commit messages: 1: Set my student ID: 445050192
2:// grained locks for independent counters
3:// Method to increment completed process
4:public static void addWaitingTime(long time) {
5:public static void logExecution(String message) {
6:// TODO #3: Acquire CPU semaphore before executing
7:public void runToCompletion() {
8:} finally {
----------------------------------------------------------------------------------------------------------------------
Summary
Total time spent on assignment: about 5.30 hours 

Key takeaways:
1:Managing shared data in multithreaded programs requires proper synchronization to ensure accurate results.
2:Choosing the right mechanism  locks vs. semaphores depends on whether the goal is data protection or resource control.
3:Writing safe concurrent code involves careful handling of locks, especially releasing them correctly and keeping critical sections small to maintain performance.
Most challenging aspect:
The most difficult challenge I faced was determining the locations of the try and catch points for each operation. I encountered many errors, but I resolved them by identifying the error within the program.
What I'm most proud of:
I'm proud to be doing this work now, preparing for it, and understanding some of the important basics that I will face in the future, especially dealing with GitHub.
