# Code for Starve-free-Reader-Writer-Problem

## Semaphore Implementation

```cpp
// Code for a FIFO semaphore.
struct Semaphore{
    int value;
    Queue* Q = new Queue();
  
    Semaphore(int val){
      value = val;
    }
    
    void wait(int process_id){
        value--;
        if(value < 0){
            Q->push(process_id);
            block(); //block is a system call which will suspends the process which invokes it. Process will remain blocked unless it explicitly woken up by some other call.
        }
    }
    
    void signal(){
        value++;
        if(value <= 0){
            int process_id = Q->pop();
            wakeup(process_id); //wakeup is a system call which will resume the blocked process with a given process_id.
        }
    }
};

//Queue implementation for FIFO semaphore.
struct Queue{
    Node* Front, Rear;
    
    Queue(){
      Front = NULL;
      Rear = NULL;
    }
  
   	void push(int process_id){        //Function to push new process in the queue.
        Node* new_node = new Node();
        new_node->data = process_id;
        if(Rear != NULL){             //If queue is not empty then change the rear node.
            Rear->next = new_node;
            Rear = new_node;
        }else{                        //If queue is empty, new_node will be same as front and rear node.
            Front = Rear = new_node;
        }
    }
    
    int pop(){
        if(Front == NULL){
            return -1; // Queue is empty, no process can be popped out.
        }else{
            int val = Front->data;
            Front = Front->next;
            if(Front == NULL){    //If queue is empty, then front and rear both node will be NULL.
                Rear = NULL;
            }
            return val;           //Return the process_id of the popped process.
        }
    }
}

// Each node is a reader/writer process to be processed in future.
Struct Node{
    Node* next;       //Pointer to next process in t
    int data;         //Store the process_id of the process.
  
    Node(int val){
      data = val;
      next = NULL;
    }
}
```

## Global Variables

Following are the global variables and their initialization.

```cpp
//Shared data members
Semaphore* serviceQueue = new Semaphore(1);     // FAIRNESS: preserves ordering of requests (signaling must be FIFO)
Semaphore* rmutex = new Semaphore(1);           // for syncing changes to shared variable readcount
Semaphore* resource = new Semaphore(1);         // controls access (read/write) to the resource
int read_started_count = 0; // a count of how many readers have started reading.
int read_completed_count = 0;// a count of how many readers have completed reading.
bool writer_waiting = false; // this indicated whether a writing is waiting.
```

## Reader Process Code

Reader Process Implementation:

```cpp
            /*-------------- ENTRY Section ------------------*/

  serviceQueue->wait(process_id);           // wait in line to be serviced
  rmutex->wait(process_id);                 // request exclusive access to readcount
  read_start_count++;                // update count of active readers
  if (read_start_count - read_completed_count == 1)         // if I am the first reader
    resource->wait(process_id);             // request resource access for readers (writers blocked)
  serviceQueue->signal();           // let next in line be serviced
  rmutex->signal();                 // release access to readcount
    
           /*-------------- CRITICAL Section ------------------*/
           //reading is performed
    
           /*-------------- EXIT Section ------------------*/
           
  rmutex->wait(process_id);                 // request exclusive access to readcount
  read_completed_count++;                // update count of active readers
  if (read_start_count == read_completed_count)         // if there are no readers left
    resource->signal();             // release resource access for all
  rmutex->signal();                 // release access to readcount
```

## Writer Process Code

Writer Process Implementation:

```cpp
            /*-------------- ENTRY Section ------------------*/
            
  serviceQueue->wait(process_id);           // wait in line to be serviced
  rmutex->wait(process_id);                 //// Secure out semaphore to ensure no read process modifies readcount variables to prevent any deadlocks;
  if(read_start_count==read_completed_count){
    rmutex->signal();
  }else{
    writer_waiting = true;
    rmutex->signal();
    resource->wait(process_id);
    writer_waiting = false;
  }
        
           /*-------------- CRITICAL Section ------------------*/
           //writting is performed
    
           /*-------------- EXIT Section ------------------*/
  
  serviceQueue->signal();
```
