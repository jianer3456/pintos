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
 
 
 ## Task 2: Priority Scheduler
 ### 1. Data structures and functions

+ *Add:*


+ *Modify:* 


### 2. Algorithms

Brief description:


### 3. Synchronization
### 4. Rationale


