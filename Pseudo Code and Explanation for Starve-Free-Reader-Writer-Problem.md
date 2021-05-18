# Pseudo Code and Explanation for Starve-Free-Reader-Writer-Problem

## Semaphores

Semaphore is simply a variable that is non-negative and shared between threads. This variable is used to solve the critical section problem and to achieve process synchronization in the multiprocessing environment. Three Semaphores are used in this problem:

   - **serviceQueue:** Preserves the ordering of requests (signaling must be FIFO).
   - **rmutex:** Semaphore used to provide mutual exclusion to readcount variables currently executing in critical section.
   - **resource:** Controls access (read/write) to the resource and prevent readers and writer/s from simultaneously executing Critical Section Code.

#### Shared Variables:
   * int read_started_count = 0; // a count of how many readers have started reading.
   * int read_completed_count = 0;// a count of how many readers have completed reading.
   * bool writer_waiting = false; // this indicated whether a writing is waiting.

### Pseudo Code for Semaphores
```cpp
strcuct Semaphore{
  int value;
  Queue* Q = new Queue();
  
  Semaphore(int val)          //Semaphore initialisation
  void wait(int process_id);  //wait function
  void signal();              //signal function
}

strcut Queue{
  Node* Front, Rear;
  
  Queue();                     //Queue initialisation
  void push(int process_id);   //Function to push new process in the queue
  int pop();                   //Function to pop out the front process from the queue
}

struct Node{
  //Node implementation
}
```

## Reader Process Code

### Pseudo Code for Reader Process
```cpp
void reader(){
<ENTRY Section>
  wait(serviceQueue);           // wait in line to be serviced
  wait(rmutex);                 // request exclusive access to readcount
  readcount++;                // update count of active readers
  if (readcount == 1)         // if I am the first reader
    wait(resource);             // request resource access for readers (writers blocked)
  release(serviceQueue);           // let next in line be serviced
  release(rmutex);                 // release access to readcount
    
<CRITICAL Section>
//reading is performed
    
<EXIT Section>
  wait(rmutex);                 // request exclusive access to readcount
  readcount--;                // update count of active readers
  if (readcount == 0)         // if there are no readers left
    resource.V();             // release resource access for all
  rmutex.V();                 // release access to readcount
}
```

### Explanation:

#### Reader Process
- **Entry section** :
  - Reader enters the request queue and waits till it acquires the "serviceQueue" Semaphore so that it can enter in the process queue.
  - Now reader wait to aquire "rmutex" Semaphore so that it can increase the read_start_count i.e. activated reader count.
  - Now if it is the only reader then it waits until it acquire "resouce" Sempahore.
  - It releases "serviceQueue" and "rmutex" Semaphores before entering critical section so that it can be available to other processes now.

- **Critical section** : Reader reads the shared data variable.

- **Exit section** : 
  - Reader waits till it aquires "rmutex" Semaphore to modify read_completed_count variable which shows how many readers have completed their reading.
  - It releases "resource" Semaphore if it is the last reader to leave.
  - "rmutex" Semaphore is released to allow others to change the readcount variable.

## Writer Process Code

### Pseudo Code for Writer Process
```cpp
void writer(){
<ENTRY Section>
   wait(serviceQueue);                      //wait in line to be serviced
   wait(rmutex);                            //block rmutex to change readcount variables to avoid deadblock
   if (readstartcount==readcompletedcount)  //If all read processes completed, release rmutex
      then release(rmutex); 
   else                                     //Else wait for writing and request exclusive access to resource
       wait=1 
       release(rmutex); 
       wait(resource); 
       wait=0 

<CRITICAL Section>
// writing is performed
    
<EXIT Section>
  release(serviceQueue);               // let next in line be serviced
}
```

### Explanation:

#### Writer Process
- **Entry section** :
  - Writer enters the request queue and waits till it acquires the "serviceQueue" Semaphore so that it can enter the process queue.
  - Now it block the change in readcount variable to prevent deadblock.
  - If all read processes are completed then it release "rmutex" Semaphore and enter in the critical section.
  - Else it will on the wait variable indicating that a writing process is waiting for execution.
  - Then it releases "rmutex" Semaphore and request for exclusive access to resources.
  - When it get access, it turn off the wait variable indicating that it no longer waiting and enter in the critical section.

- **Critical section** : Writer modifies the shared data.

- **Exit section** :
  - Writer releases the "serviceQueue" Semaphore.
