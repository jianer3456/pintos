# Project Design
  There are 4 tasks in this project.
## Task 1 Efficient Alarm Clock
+ Data structures and functions

*Add:*
     
     * int64_t ticks_sleep: To store the sleeping ticks plus the surrent ticks 
     * static struct list sleep_list: to store the sleeping threads
     * void check_sleep_time(struct list *sleep_list)
     
*Modify:*     
     
     * void timer_sleep(int64_t ticks):
     * static void timer_interrupt(struct intr_frame *args UNUSED)

+ Algorithms
Brief description:

    Add a new variable *ticks_sleep* in *struct thread* to record the sleeping time of a thread. 
    Then modify the *timer_sleep()* which is blocking a thread instead of calling *thread_yield()*. 
    When sleeping time over, unblocking it
    
    + **timer_sleep()**
    
        At first, modify the ticks_sleep in the thread. 
        Then add the current thread into the sleep_list and blocking it.
        
    + **timer_interrupt**
        
        Call *check_sleep_time()* to unblock the sleeping thread that should be woken up every ticks.
        
    +  **check_sleep_time()**
    
        search the threads in *sleep_list* which is maintained as a ascending order list util meet the thread whose *sleep_ticks* is larger than *curr_dicks* .
        
        
    
        
// add a function to check the rest sleeping time of a thread

//modify the timer_interrupt

//use the check_sleep_time as the input of thread_foreach() then call it in timer_interrupt()

{
  ...
  thread_foreach(blocked_thread_check)
  ...
}
