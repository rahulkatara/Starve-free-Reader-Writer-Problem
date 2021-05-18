# Starve-free-Reader-Writer-Problem

Consider a situation where we have a file shared between many people.

  * If one of the people tries editing the file, no other person should be reading or writing at the same time, otherwise changes will not be visible to him/her.
  * However if some person is reading the file, then others may read it at the same time.

Precisely in **OS** we call this situation as the **Readers-Writers Problem**.

The Readers-Writers problem is one of the classic Process Synchronization problems, and it has been used to test nearly every new synchronization primitive. In
this problem there are two type of users the Readers, who wants to read the shared resource and the Writers, who wants to modify the shared resourcse. There are three
variations to this problem:

## 1. First Readers-Writers Problem

This problem allows that the when there is atleast one reader accessing the resource, then new readers can also enter the critical section to read that resource. However, in this case the writer may lead to starvation as it may not get a chance to modify the resourcse as new readers may continiously enter to read the resource.

## 2. Second Readers-Writers Problem

In this solution, preference is given to the writers.However here the readers may starve as the reader must wait until the last writer exits the critical section by modifying the resource and release the lock to allow readers to acces the resource.

> The solutions implied by both problem statements can result in starvation — the first one may starve writers in the queue, and the second one may starve readers. Therefore, the **third readers–writers problem** is proposed, which adds the constraint that no thread shall be allowed to starve.

## 3. Third Readers-Writers Problem

This problem overcomes the drawbacks of previous two problems of starvation and therefore is also known as the **Starve-Free-Readers-Writers-Problem**. It will give priority to the resources in the order of their arrival. For example, if a writer wants to write to the resource then it will wait until the current readers execute their tasks. Meanwhile, other readers accessing the resourcse would not be allowed to do so.

## How Third Readers-Writers Problem solve the problem of starvation?

Third Readers-Writers Problem make use of **FIFS(First Come First Serve)** strategy to overcome the problem of starvation. Suppose processes come as RRRWRWRRR. Now, in third readers-writers problem, the first three readers will first read. Then, when the next process which is a writer starts to execute, it won't leave the resource until it's done. Now, suppose by the time this writer is done writing, the next writer has already arrived then it will be queued up with other processes. Thus, it won't get to start writing before the process before it completed i.e. the reader processes are completed. Thus, when the first writer completes its process then the reader process will start as they are ahead of the second writer in the queue. And when the queued readers complete their task then only the second writer process will begin which will block any more processes from starting and so on.

In this way, it saves both the readers and writers from starvation by its fairness of not giving them priority on the basis of reader/writer, but giving priority the process which reaches first to it.

## References

* <https://tutorialspoint.dev/computer-science/operating-systems/readers-writers-problem-set-1-introduction-and-readers-preference-solution>
* https://en.wikipedia.org/wiki/Readers-writers_problem
