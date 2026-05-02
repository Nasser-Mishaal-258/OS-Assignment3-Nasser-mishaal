# Assignment 3 - Complete Documentation

**Student Name**: [Nasser mishaal almutiri]  
**Student ID**: [445050258]  
**Date Submitted**: [2/5/2026]

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

### Entry 1 - [May 1, 9:00 PM]
**What I implemented**: 
I began by comprehending the current code and looking for possible race situations in common resources like the execution log and counters.
**Challenges encountered**: 
Finding every crucial part of the code was challenging.
**How I solved it**: 
I follwed every shared variables that were accessed by several threads while closely examining the code.
**Testing approach**: 
To see inconsistent behavior, run the program several times.
**Time spent**: 
3 hours
---

### Entry 2 - [May 2, 12:15 AM]
**What I implemented**: 
Added reentrantLock to protect shared counters which is contextSwitchCount, completedProcessCount and totalWaitingTime
**Challenges encountered**: 
Understanding where exactly to place lock and unlock.
**How I solved it**: 
Used try-finally blocks to ensure locks are always released.
**Testing approach**: 
Verified no incorrect counter values appear.
**Time spent**: 
1 hour
---

### Entry 3 - [May 2, 1:00 AM]
**What I implemented**: 
Added a separate lock to protect executionLog.
**Challenges encountered**: 
Understanding why ArrayList is not thread-safe.
**How I solved it**: 
Studied concurrent collections and applied locking around log access.
**Testing approach**: 
Checked that no ConcurrentModificationException occurs.
**Time spent**: 
45 min
---

### Entry 4 - [May 2, 1:30 AM]
**What I implemented**: 
Implemented Semaphore to control CPU access.
**Challenges encountered**: 
Understanding how semaphore differs from lock.
**How I solved it**: 
Used a binary semaphore with 1 permit.
**Testing approach**: 
Ensured only one process executes at a time.
**Time spent**: 
30 min
---

### Entry 5 - [May 2, 2:00 AM]
**What I implemented**: 
Final testing and verification.
**Challenges encountered**: 
Ensuring consistent results across runs.
**How I solved it**: 
Ran program multiple times and verified outputs.
**Testing approach**: 
Repeated execution and compared results.
**Time spent**: 
30 min
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:


[The shared variable contextSwitchCount is where the first race situation takes place. This variable is incremented by many threads without synchronization, which may lead to concurrent access and lost updates. The executionLog ArrayList has a second race condition when several threads add elements at once, which could result in inconsistent state or a ConcurrentModificationException. Without synchronization, threads could distort the data structure or overwrite each other's updates. Inaccurate statistics and untrustworthy logs result from this. As a result, both resources need to be properly secured using locks.]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:
[ReentrantLock ensures that only one thread can access shared variables at a time by using mutual exclusion to safeguard crucial parts. Conversely, a semaphore limits the number of threads that can access a segment, hence controlling access to a resource. Because shared counters and execution logs require exclusive access, I utilized ReentrantLock in my code to safeguard them. I simulated CPU access by using a binary semaphore, which only permits one process to run at a time. This division enhances clarity and aligns with each mechanism's intended purpose.]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[When several threads are waiting endlessly for resources owned by one another, this is known as deadlock. Using try-finally blocks to guarantee that locks are always released is one preventative strategy. Keeping consistent lock ordering or avoiding nested locks are two other strategies. To ensure that locks and semaphores are released even in the event of an exception, I employed try-finally in my code. This guarantees system stability and avoids resource blockage.]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[I applied a single lock to each of the three counters as part of a coarse-grained locking strategy. This makes it easier to install and less complicated to manage several locks. However, since only one thread may update any counter at a time, the trade-off is decreased concurrency. By enabling many threads to update various counters at the same time, fine-grained locking would increase concurrency. Fine-grained locking would perform better because the counts are independent. But for simplicity and accuracy, I went with coarse-grained locking, which is appropriate for this task.]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, totalWaitingTime
**Why they need protection**: 
They are shared across multiple threads and can lead to incorrect values due to race conditions.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
// Paste your implementation here
counterLock.lock();
try {
    contextSwitchCount++;
} finally {
    counterLock.unlock();
}
```

**Justification**: 
Ensures mutual exclusion and prevents inconsistent updates.
---

### Critical Section #2: Execution Log

**What resource**: 
executionLog ArrayList
**Why it needs protection**: 
ArrayList is not thread-safe and concurrent access may corrupt data.
**Synchronization mechanism used**: 
ReentrantLock
**Code snippet**:
```java
// Paste your implementation here
logLock.lock();
try {
    executionLog.add(message);
} finally {
    logLock.unlock();
}
```

**Justification**: 
Prevents concurrent modification and ensures data integrity.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
To limit CPU access to one process at a time.
**Number of permits and why**: 
1 permit to simulate single CPU core.
**Where implemented**: 
Inside run() method before execution.
**Code snippet**:
```java
SharedResources.cpuSemaphore.acquire();
try {
    // execution
} finally {
    SharedResources.cpuSemaphore.release();
}
```

**Effect on program behavior**: 
Ensures controlled execution and prevents overlapping CPU usage.
---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

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
(Every run yielded consistent outcomes. 20 processes were always finished, and the execution flow stayed accurate. In each run, the scheduling behavior correctly adhered to the Round Robin method. There were no missing or erroneous values in the logs or counters.)

**Why synchronization is necessary**: 
(Without synchronization, shared variables such as contextSwitchCount, completedProcessCount, and totalWaitingTime could produce incorrect values due to race conditions. For example, two threads might increment the same counter simultaneously, leading to lost updates. and, the executionLog (ArrayList) could become corrupted due to concurrent modifications, potentially causing runtime exceptions or inconsistent logs.)

**Conclusion**: 
Synchronization ensures that all shared resources are accessed safely, resulting in consistent and reliable outputs across multiple runs.
---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 
I ran the program multiple times under normal conditions and observed the behavior of executionLog while multiple threads were adding entries concurrently.
**Results**: 
No ConcurrentModificationException occurred during execution.
**What this proves**: 
This proves that the executionLog is properly synchronized using a lock, preventing concurrent access issues and ensuring thread-safe operations.
---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 
Total completed processes should equal number of processes (20)
Context switches should match the number of quantum executions
Waiting times should be logical and non-negative
**Actual values**: 
Total Completed Processes: 20
Total Context Switches: 37
Total Waiting Time: 1115312 ms
Average Waiting Time: 55765 ms
**Analysis**: 
The results match expected behavior. All processes completed successfully, and context switches reflect proper scheduling. Waiting times are reasonable given the number of processes and quantum size.
---

### Test 4: Different Scenarios
**Scenario tested**: [Different random values for time quantum and number of processes.]

**Purpose**: 
To verify that synchronization works correctly under different scheduling conditions.
**Results**: 
The program maintained correct behavior regardless of changes in time quantum or number of processes.
**What I learned**: 
Synchronization mechanisms like locks and semaphores ensure system stability even when system parameters change.
---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[In multithreaded systems, synchronization is necessary to avoid race situations and guarantee data consistency. I discovered that concurrent, unprotected access to common resources can result in unanticipated behavior. I was able to establish mutual exclusion for crucial areas by using ReentrantLock, and I was able to manage access to scarce resources like CPU by using Semaphore. I also realized how crucial try-finally blocks are for avoiding deadlocks. My comprehension of concurrent programming and thread safety has improved as a result of this project. It also demonstrated to me how minor synchronization errors might result in major system problems. All things considered, synchronization is a key idea in operating systems and practical applications.]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: Banking systems where multiple transactions access the same account balance. Synchronization ensures that deposits and withdrawals are processed correctly without data corruption.

**Example 2**: Database management systems where multiple users access and modify shared data simultaneously. Locks and semaphores ensure consistency and prevent data anomalies.

---

### How I would explain synchronization to others:

[Synchronization is like controlling access to a shared resource. Imagine a single printer in an office: only one person can use it at a time. If multiple people try to print at the same time without coordination, documents may get mixed or corrupted. Locks act like a key that allows only one person to use the printer at a time, while semaphores act like a ticket system that limits how many people can access a resource simultaneously. In programming, synchronization ensures that multiple threads work safely without interfering with each other.]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/Nasser-Mishaal-258/OS-Assignment3-Nasser-mishaal.git

**Number of commits**: 9

**Commit messages**: 
1. Changing to my id
2. We add some ReentrantLock to the shared counters
3. Add semaphore for shared counters
4. protect contextSwitchCount
5. protect completedProcessCount
6. protect totalWaitingTime
7. protect executionLog
8. Add semaphore for CPU control
9.adding Semaphore to runToCompletion
---
1. In multithreaded systems, synchronization is crucial to avoiding race situations.
2. While semaphores regulate resource access, locks offer mutual exclusion.
3. To guarantee consistent and appropriate conduct, proper testing is necessary.
## Summary

**Total time spent on assignment**: 7 hours

**Key takeaways**: 
1. In multithreaded systems, synchronization is crucial to avoiding race situations.
2. While semaphores regulate resource access, locks offer mutual exclusion.
3. To guarantee consistent and appropriate conduct, proper testing is necessary.

**Most challenging aspect**: 
Understanding how to correctly place locks and ensure they are always released without causing deadlocks.
**What I'm most proud of**: 
putting synchronization methods into practice successfully and getting accurate, consistent program behavior over several runs.
---

**End of Documentation**
