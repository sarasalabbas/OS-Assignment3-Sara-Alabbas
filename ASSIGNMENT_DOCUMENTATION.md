# Assignment 3 - Complete Documentation

**Student Name**: Sarah Saad Alabbas
**Student ID**: 444052644   
**Date Submitted**: 6 May 2026

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [May 5, 2026 - 7:30 PM]
**What I implemented**: 
Forked the repository, cloned it using VS Code, and updated my student ID in the source code.

**Challenges encountered**: 
I needed to make sure the repository was public and connected correctly with GitHub.

**How I solved it**: 
I followed the README instructions carefully and verified the repository visibility settings.

**Testing approach**: 
I checked that the project opened correctly in VS Code and verified the student ID change.

**Time spent**: 
30 minutes
---

### Entry 2 - [May 5, 2026 - 8:00 PM]
**What I implemented**: 
Implemented the required synchronization tasks in the code by adding ReentrantLock for shared counters and execution log protection.

**Challenges encountered**: 
Identifying which shared resources needed synchronization and understanding where race conditions could occur.

**How I solved it**: 
I analyzed the shared variables and protected the critical sections using mutex locks with try-finally blocks.

**Testing approach**: 
I compiled and executed the program several times to verify that the counters and execution log worked correctly without errors.

**Time spent**: 
40 minutes
---

### Entry 3 - [May 5, 2026 - 9:00 PM]
**What I implemented**: 
I first implemented synchronization using one shared ReentrantLock for all counter variables and the execution log.

**Challenges encountered**: 
Using one lock for all shared resources reduced concurrency because threads had to wait even when accessing independent variables.

**How I solved it**: 
After reviewing the synchronization design, I changed the implementation to use separate locks for each counter and a dedicated lock for the execution log.

**Testing approach**: 
I compared the program behavior before and after the modification and verified that the program still produced correct results.

**Time spent**: 
20 minutes

---

### Entry 4 - [May 6, 2026 - 9:30 PM]
**What I implemented**: 
Performed final testing for the synchronization mechanisms and verified the correctness of the program output.

**Challenges encountered**: 
Ensuring that all threads completed correctly without synchronization problems or inconsistent statistics.

**How I solved it**: 
I carefully reviewed the execution output and synchronization statistics after running the program multiple times.

**Testing approach**: 
I ran the program several times and compared the results to confirm consistent behavior.

**Time spent**: 
20 minutes
---

### Entry 5 - [May 6, 2026 - 9:15 PM]
**What I implemented**: 
Completed the assignment documentation and prepared the video demonstration requirements.

**Challenges encountered**: 
Organizing the explanation of synchronization concepts clearly and summarizing the implementation steps.

**How I solved it**: 
I reviewed the README instructions and documented each synchronization mechanism with explanations and testing results.

**Testing approach**: 
I checked the repository, commits, and documentation to ensure everything was ready for submission.

**Time spent**: 
2 hours
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:
One race condition in the original code was related to the shared counter variables such as `contextSwitchCount`. Multiple threads could update the counter at the same time using `contextSwitchCount++`, which is not an atomic operation. This could cause incorrect values because some increments may be lost when threads run concurrently.

Another race condition was in the `executionLog` ArrayList. Since ArrayList is not thread-safe, multiple threads adding log messages at the same time could cause inconsistent data or `ConcurrentModificationException`. Concurrent access is dangerous because threads may try to modify the same shared resource simultaneously without coordination.

To solve these problems, I used `ReentrantLock` to protect the critical sections and ensure that only one thread accesses the shared resource at a time.
---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:
`ReentrantLock` is used to protect critical sections and allow only one thread to access shared data at a time. I used it for the shared counters and the execution log because these resources could be modified by multiple threads simultaneously.
A `Semaphore` is used to control access to a resource using permits. Unlike a lock, a semaphore can allow more than one thread depending on the number of permits. In my code, I used a binary semaphore with one permit to control CPU access so that only one process can execute at a time.

I used locks for protecting shared variables and used the semaphore for controlling process execution and CPU access.

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:
Deadlock happens when two or more threads wait for each other forever and none of them can continue execution. This usually happens when locks are not managed correctly.

One technique to prevent deadlock is always releasing locks properly. In my code, I used `try-finally` blocks to make sure every lock and semaphore is released even if an exception occurs.

Another technique is keeping critical sections small and simple. I only locked the shared resources when necessary and released the lock immediately after the protected operation finished.

These techniques helped make the program safer and prevented threads from getting stuck.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

At first, I used one shared lock for all three counters because it was simpler to implement. However, I realized that this approach reduced concurrency because threads had to wait even when they were accessing different independent counters.

After that, I changed the design and used separate locks for each counter (`contextSwitchLock`, `completedProcessLock`, and `waitingTimeLock`). I chose this approach because the counters are independent and do not depend on each other.

Using separate locks is called fine-grained locking. It improves concurrency because multiple threads can update different counters at the same time without unnecessary waiting.

The main advantage of using one lock is simplicity, but it can create more contention between threads. Fine-grained locking is slightly more complex but provides better performance and better thread concurrency.

Since the three counters in this assignment are independent, fine-grained locking was the better choice.

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
`contextSwitchCount`, `completedProcessCount`, and `totalWaitingTime`

**Why they need protection**: 
These variables are shared between multiple threads. If more than one thread updates them at the same time, incorrect values may occur because increment and addition operations are not atomic.

**Synchronization mechanism used**: 
ReentrantLock with separate locks for each counter.

**Code snippet**:
```java
public static final ReentrantLock contextSwitchLock = new ReentrantLock();

public static void incrementContextSwitch() {
    contextSwitchLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        contextSwitchLock.unlock();
    }
}
```

**Justification**: 
Using locks ensures that only one thread updates a shared counter at a time and prevents race conditions.
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog ArrayList

**Why it needs protection**: 
ArrayList is not thread-safe, so concurrent modifications by multiple threads may cause inconsistent data or runtime exceptions.

**Synchronization mechanism used**: 
ReentrantLock (logLock)

**Code snippet**:
```java
public static void logExecution(String message) {
    logLock.lock();
    try {
        executionLog.add(message);
    } finally {
        logLock.unlock();
    }
}
```

**Justification**: 
The lock protects the shared execution log and ensures safe access by one thread at a time.

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
To control CPU access and limit process execution concurrency.

**Number of permits and why**: 
I used one permit (Semaphore(1)) to allow only one process to execute at a time.

**Where implemented**: 
Inside the run() and runToCompletion() methods.

**Code snippet**:
```java
SharedResources.cpuSemaphore.acquire();

try {
    // process execution
} finally {
    SharedResources.cpuSemaphore.release();
}
```

**Effect on program behavior**: 
The semaphore prevents multiple processes from executing on the CPU simultaneously and keeps execution synchronized.

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: 
Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
java SchedulerSimulationSync
```

**Results**: 
The program completed successfully in every run. The synchronization statistics and completed process count were correct and stable.

**Why synchronization is necessary**: 
Without synchronization, multiple threads could modify shared counters and the execution log at the same time, causing race conditions and inconsistent results.

**Conclusion**: 
The synchronization mechanisms successfully protected the shared resources and produced consistent output.

---

### Test 2: Exception Testing
**What I tested**: 
Checking for ConcurrentModificationException and synchronization-related errors.

**Testing procedure**: 
I executed the program multiple times while monitoring the execution log behavior.

**Results**: 
No ConcurrentModificationException or thread synchronization errors occurred.

**What this proves**: 
The execution log protection using ReentrantLock worked correctly.

---

### Test 3: Correctness Verification
**What I tested**: 
Verifying final synchronization statistics and process completion.

**Expected values**: 
All processes should complete successfully and synchronization statistics should be consistent.

**Actual values**: 
The program completed all processes correctly and displayed stable statistics for context switches, waiting time, and completed processes.

**Analysis**: 
The locks and semaphore correctly protected shared resources and controlled concurrent execution.

---

### Test 4: Different Scenarios
**Scenario tested**: 
Running the program with different randomly generated process values.

**Purpose**:
To verify that synchronization works correctly under different execution conditions. 

**Results**: 
The program continued to work correctly and produced stable output in all scenarios.

**What I learned**: 
Synchronization is important for maintaining correct behavior in concurrent programs.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

In this assignment, I learned how race conditions happen when multiple threads access shared resources at the same time. I understood the importance of protecting critical sections using synchronization mechanisms such as ReentrantLock and Semaphore. I also learned the difference between coarse-grained and fine-grained locking and how lock granularity affects concurrency and performance.

Another important thing I learned was using try-finally blocks to safely release locks and semaphores. Before this assignment, I knew the basic idea of multithreading, but now I better understand how synchronization keeps programs stable and prevents incorrect behavior.

Testing the program multiple times also helped me understand why synchronization is necessary even if race conditions do not always appear immediately.

---


### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 
Banking systems where multiple users access and update the same account balance at the same time.

**Example 2**: 
Operating systems where multiple processes compete for CPU and memory resources.

---

### How I would explain synchronization to others:
I would explain synchronization as a way to organize threads so they do not interfere with each other while using shared resources. It is similar to having one key for a room, where only one person can enter at a time. Locks and semaphores help prevent conflicts and keep the program working correctly when many threads run together.

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/sarasalabbas/OS-Assignment3-Sara-Alabbas

**Number of commits**: 4

**Commit messages**: 
1. Set my student ID
2. Add synchronization using locks and semaphore
3. Improve synchronization with fine-grained locks
4. Completed assignment documentation and final testing

---

## Summary

**Total time spent on assignment**: 4 hours

**Key takeaways**: 
1. Synchronization is necessary to protect shared resources.
2. Locks and semaphores help prevent race conditions.
3. Fine-grained locking can improve concurrency and performance.

**Most challenging aspect**: 
Understanding where race conditions could happen and choosing the best synchronization design.

**What I'm most proud of**: 
Successfully implementing synchronization mechanisms and improving the lock design from one shared lock to fine-grained locks.

---

**End of Documentation**
