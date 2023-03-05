# Starve Free Reader-Writer's Problem

## The Problem
The problem is to find a starve free solution to the reader-writer's problem such that neither a reader nor a writer process gets starved.

## Solution : 

To solve the problem, we introduce an extra semaphore "turn" that is common to both reader and writer. When a writer requests for the critical section, we first let all the readers that arrive earlier than the writer to complete their critical section and then process the writer request. Only after that are the other reader requests that arrive after the writer process are processed. The read_count at a time stores the number of reader requests that arrive before a writer process. So, when read_count becomes 0 , we signal using write semaphore to allow the writer process to execute.


## Pseudocode 
```
// Pseudocode :

semaphore turn = 1,write_mutex = 1,read_mutex = 1;
int read_count = 0;

//reader's process:
{
    wait(turn);  //waiting for its turn for execution
    wait(read_mutex); //ensuring only one read process gets the chance to             
                      // update read_count
    read_count++;
    if(read_count==1){
        wait(write_mutex); //first reader will ask for resource 
                            //access for readers
    }
    signal(read_mutex); //releasing access for updation of read count
    signal(turn);

    /reading is performed/
    /critical section/

    wait(read_mutex); //ensuring only one read process gets the chance
                        // to update read_cnt
    read_count--;
    if(read_count==0){
        signal(write_mutex); //freeing the resources for writers to write
    }
    signal(read_mutex);   //releasing access for updation of read count
}

//writer's process:
{
    wait(turn); //waiting for its turn for execution
    wait(write_mutex); //waiting if at present some reader(that arrived       
                        //before the writer) is reading

    /writing is performed/
    /critical section/

    signal(write_mutex); //releasing the resource for the other reader
                        // or writer process
    signal(turn); //Signalling the next reader or writer process in
                    // turn queue to wakeup 
}
```

