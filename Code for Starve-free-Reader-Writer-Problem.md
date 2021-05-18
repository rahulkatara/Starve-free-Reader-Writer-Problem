# Code for Starve-free-Reader-Writer-Problem

## Semaphore Implementation

```cpp
// The code for a FIFO semaphore.
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
            int pid = Q->pop();
            wakeup(pid); //this function will wakeup the process with the given pid.
        }
    }
};

//The code for the queue which will allow us to make a FIFO semaphore.
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

// A queue node: Each node is a reader/writer process to be processed in future.
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
Semaphore* in_sem = new Semaphore(1);
Semaphore* out_sem = new Semaphore(1);
Semaphore* writer_sem = new Semaphore(0);
int num_started = 0; // a count of how many readers have started reading.
int num_completed = 0;// a count of how many readers have completed reading.
bool writer_waiting = false; // this indicated whether a writing is waiting.
```

