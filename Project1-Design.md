# Project Design
  There are 4 tasks in this project.
## Task 1: Efficient Alarm Clock
### 1. Data structures and functions

+ *Add:*
     
     * int64_t ticks_sleep: To store the sleeping ticks plus the surrent ticks 
     * static struct list sleep_list: to store the sleeping threads
     * void check_sleep_time(struct list *sleep_list): to check whether there are some sleeping threads will be released.
     
+ *Modify:*     
     
     * void timer_sleep(int64_t ticks): to change the way to fall a thread asleep.
     * static void timer_interrupt(struct intr_frame *args UNUSED): add a step to call check_sleep_time().

### 2. Algorithms

Brief description:
    Add a new variable *ticks_sleep* in *struct thread* to record the sleeping time of a thread. Then modify the *timer_sleep()* which is blocking a thread instead of calling *thread_yield()*. When sleeping time over, unblocking it.
  
  + **timer_sleep():**
    
       At first, modify the ticks_sleep in the thread. 
       Then add the current thread into the sleep_list and blocking it.
        
  + **timer_interrupt():**
        
       Call *check_sleep_time()* to unblock the sleeping thread that should be woken up every ticks.
        
  + **check_sleep_time():**
    
       search the threads in *sleep_list* which is maintained as a ascending order list util meet the thread whose *sleep_ticks* is larger than *curr_dicks* .
        
### 3. Synchronization

   There are two shared resourse used in this function: *curr_ticks* and *sleep_list*. However, this two are both acessed in *timer_interrupt()* and *timer_sleep()* so that I disable the interruption when executing *timer_sleep()* .
    
 ### 4. Rationale
 
   I didn't think about using a order list to store sleeping threads at the first. However, when a thread call *timer_sleep()* with a sleeping thread, it dosen't make sense. So it is reasonable and necessary to use list to store sleeping thread. What's more, it is a order list so it can reduce the calculation time in *timer_interrupt()* .
 
 ## Task 2: Priority Scheduler
 ### 1. Data structures and functions

+ *Add:*


+ *Modify:* 


### 2. Algorithms

Brief description:


### 3. Synchronization
### 4. Rationale

 
 ## Task 3: MLFQS
 ### 1. Data structures and functions

+ *Add:*
    + int nice: The value of nice.                            
    + int recent_cpu: The recent time running on cpu of a thread.
    + #define NICE_MIN -20
    + #define NICE_DEFAULT 0
    + #define NICE_MAX 20
    + int load_avg: The system load average.
    + int *ready_list_end: a pointer point to the end of ready_list last 4 ticks.
    + static struct queue_list all_queues: a struct to gather 64 priority queues which is struct list.
    + fixed_point_t recalculate_per_4_ticks(struct thread \*thread): recalculate a thread's priority when per 4 ticks.
    + fixed_point_t recalculate_per_100_ticks(struct thread \*thread): recalculate a thread's priority when per 100 ticks.
    + void reorder_queue_list(struct queueList all_queues): to configure all ready threads in different piority queue.

+ *Modify:* 
    + static struct thread *next_thread_to_run (void) 
    + void thread_set_nice (int nice UNUSED)
    + int thread_get_nice (void)
    + int thread_get_load_avg (void)
    + int thread_get_recent_cpu (void) 
   

### 2. Algorithms
Brief description: The function *next_thread_to_run()* will return the first thread in the largest priority queue which is not null. The function reorder_queue_list will call *recalculate_per_4_ticks()* per 4 ticks and call *recalculate_per_100_ticks()* per 100 ticks to reorder the queue list.
    
   + **next_thread_to_run():**
       Use for loop to find the first thread in all_queues.
   + **reorder_queue_list():**
       When per 4 ticks, call the *recalculate_per_4_ticks()* to calculate the priority of current thread then insert it into the end of  corresponding priority queue. What's more, call the *recalculate_per_4_ticks()* to update the priority of the new one in ready_list by *ready_list_end* then insert. When per 100 ticks, call *thread_foreach(recalculate_per_100_ticks, null)* to update all the priority of all thread. Then configure the thread in *ready_list* into *all_queues* .
   + **recalculate_per_4_ticks():**
       Use the formular *priority = PRI_MAX - (recent_cpu/4) - (nice*2)* to calculate the priority of the thread.
   + **recalculate_per_100_ticks():**
       Use the formular *load_avg = (59/60)*load_avg + (1/60)*ready_threas* to update load_avg.
       Use the formular *recent_cpu = (2*load_avg)/(2*load_avg+1)*recent_cpu + nice* to update recent_cpu.
       Use the formular *priority = PRI_MAX - (recent_cpu/4) - (nice*2)* to calculate the priority of the thread.

### 3. Synchronization
### 4. Rationale

## Additional Questions

1. 
timer ticks|R(A)|R(B)|R(C)|P(A)|P(B)|P(C)|thread to run
--|:--:|:--:|:--:|:--:|:--:|:--:|--:
0|0|0|0|63|61|59|A
4|4|0|0|62|61|59|A
8|8|0|0|61|61|59|B (Round Robin)
12|8|4|0|61|60|59|A
16|12|4|0|60|60|59|B (Round Robin)
20|12|8|0|60|59|59|A 
24|16|8|0|59|59|59|C (Round Robin)
28|16|8|4|59|59|58|B (Round Robin)
32|16|12|4|59|58|58|A
36|20|12|4|58|58|58|C (Round Robin)


 2. The recent_time is ambiguities because when a thread running with interruption that calculate recent_cpu, priority of all threads or resort ready_list, this current thread will yield. Therefore, the recent_cpu will be less than 4 ticks per 4 ticks. However, it is hard to figure out the calculation time so it still add 4 per 4 ticks.
