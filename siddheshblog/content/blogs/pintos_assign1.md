---
title: "CS 301: Operating Systems: Assignment 1"
date: 2022-08-30T23:38:03+05:30
draft: false
---

### Group Members ###
Siddhesh Kanawade: [siddhesh.kanawade@iitgn.ac.in](mailto:siddhesh.kanawade@iitgn.ac.in) \
Kushgra Jain: [kushagra.jain@iitgn.ac.in](mailto:kushagra.jain@iitgn.ac.in) \
Utkarsh Mishra: [utkarsh.mishra@iitgn.ac.in](mailto:utkarsh.mishra@iitgn.ac.in)


### Preliminaries ###
The code is highly self explanatory due to multiple comments and humanly readable variables and function names. The main goal of this assignment was to understand the Threads in the Pintos Operating System. Also, please note that, the comments in the datastructure section are drafted to act as the description of the struct, enum, or data type.

### Alarm Clock ###

#### Data Structures ####

In `thread.h`: 
```c
struct thread {
    ...
    struct list_elem sleepelem;         /* List element for blocking threads list */
    int64_t block_time;                /* Defining the remaining ticks */
    ...
}
```

In `thread.c`:
```c
static struct list sleep_list;      /* List for sleeping/blocked threads, thread will be added on call of timer_sleep() */

```
Files to be modified: 
1. threads/thread.h
2. threads/thread.c
3. threads/timer.c

#### Algorithms ####
The main shortcomings of the formal codes is that, all threads ( including the sleeping threads and normal threads ) share the same storage space.
From the theory, its pretty clear that all the threads share the same shared memory. Our aim is to modify the system call `timer_alarm(int ticks)`. This is a system call that wakes up a process in `ticks` amount of time. We define `sleep_list` to store the sleeping/blocked threads. We call the `timer_sleep` function to check whether thread has to be in sleep or in ready list. It calls the `thread_sleep` function if their is yet time for the thread to wake up.

```c
void
timer_sleep (int64_t ticks) 
{

  int64_t start = timer_ticks();
  ASSERT (intr_get_level () == INTR_ON);
  intr_disable ();
  if(timer_elapsed(start) < ticks)
  	thread_sleep (ticks);
  intr_set_level (INTR_ON);
}
```

Following is the `thread_sleep()` functions:

```c
void thread_sleep(int64_t ticks)
{
  struct thread *cur = thread_current();
  cur->block_time = ticks;
  list_push_back(&sleep_list, &cur->sleepelem);
  thread_block();
}
```
The thread_sleep() function push_back the thread into the sleep_list. We then call `thread_block` which updates the state of thread as Blocked. From `thread_block` the `schedule` function is called and then a series of calls are made to keep the track on global time and time remaining for the thread to wake. When thread is waked up, its then unblocked using `thread_unblock` and added to the ready_list.

```c
 if (t->block_time <= 0)
      {
        thread_unblock(t);
        list_remove(temp);
      }
```
Other helpful functions: 
1. `timer_ticks()` => Returns the number of timer ticks since the OS booted
2. `timer_elapsed(int64_t then)` => Returns the number of timer ticks elapsed since THEN, which should be a value once returned by `timer_clicks()`
3. `thread_yield (void)` => “Yields” the CPU and inserts the thread to the `ready_list`

#### Synchronization ####
 1. **Briefly describe what happens in a call to timer_sleep(), including the effects of the timer interrupt handler.** \
 The process switches between ready state and running state to check if the alarm time has reached or not. As a result, it keeps crunching CPU cycles while it does the checking.. When `timer_sleep` is called, it checks for the elapsed time and block time of the thread, based on that it triggers the adding of thread to `sleep_list` or not. Remaining time for thread to wake is updated at every tick and when remaining time is zero or less that zero, thread is unblocked. Also, until and unless `timer_sleep` is not called in interrupt context, our operations are thread safe.

 2. **What steps are taken to minimize the amount of time spent in the timer interrupt handler?** \
 The amount of time spent can be minmized if we use the interupt disable during the operation. This makes the process almost `atomic`.

 ```c
 enum intr_level old_level = intr_disable ();
 ...
 intr_set_level (old_level);
 ```


#### Rationale ####
Pintos had `busy waiting` by default. Busy waiting is a synchronization technique in which a process continuously checks if a condition is true before continuing to execute. In the meanwhile, it does not let go of the processor. Which is wasteful as the process keeps looping to check for the condition. Busy waiting is usually not desirable as it is not an efficient use of the Processor’s resources. Our aim here is to modify PintOS such that it uses `sleep/wakeup` for alarm. It ensures that the threads in the `sleep_list` doesn't consume any CPU cycles and are resource efficient. The threads in the `sleep_list` are simply pushed back and not placed in order, hence searching can be expensive in some cases.

The other approaches that can be thought of is like having just two lists: `ready_list` and `all_list` and the elements not in `ready_list` but in `all_list` are the elements of the `sleep_list` this approach will be inefficient when we have lot of ready state threads and very less sleeping threads, thus the searching will be expensive in that case. 


### Priority Scheduling ###

As of now, Pintos uses FIFO concept for scheduling. We need to implement the Priority scheduling so that the threads with higher priority are executed first and then the threads with lesser priority. Naive description of the process is that we select a thread from the `ready_list` whose priority is highest and execute it. Also, while inserting the new thread to the `ready_list`, we check its priority with the current running thread.

```c
if(priority_curr < priority_new_thread) {
    // Execute new thread and put curr thread to ready list
} else {
    // Keep executing curr thread
}
```
Also, when selecting a thread from the set of threads waiting for a lock, select the one with highest priority.

#### Data Structures ####
1. In `thread.h`:

```c
struct thread {
    ...
    int real_priority;     /* Real Priority while being donated*/
    struct list locks;      /*All the locks that are being held in waiting*/
    struct lock *curr_lock; /* Thread is locked by this lock*/
    ...
}
```


2. In `synch.h`:

```c
struct lock {
    ...
    struct list_elem elem; /*list elem, store in locks held in struct thread, linked list node */
    int max_priority;      /*The maximium priority of all threads locked by this lock, that is from the waiters list.*/
    ...
}
```
3. In `synch.c` :
```c
struct semaphore_elem {
    int priority;               /*The maximum priority within the waiters in WAITERS of the semaphore*/
}
```

#### Algorithms ####
1. **Next thread to run:** \
Our target is to make the threads execute based on some priority and to avoid corner cases like priority inversion. When we call `schedule()`, will call to run the next thread in sequence. The thread is supposed to be present in `ready_list`which will be sorted based on the priority of threads it contains. We update the status of thread to THREAD_READY when we add it to ready_list. We sort the `ready_list` in descending order of priority.

Following are some helper funtions: \
**compare_piority()**

Sort the threads in descending order of priority. 

```c
bool compare_priority(const struct list_elem *a, const struct list_elem *b, void *aux)
{
  struct thread *t1 = list_entry(a, struct thread, elem);
  struct thread *t2 = list_entry(b, struct thread, elem);

  return t1->priority > t2->priority;
}
```

**thread_set_priority()**

It is responsible to set/update the real_priority of the current thread. To update the priority based on the threads requesting a particular lock, we used `thread_update_priority` funtion. We also have `locks_by_priority` comparator to sort the threads based on their priorities. Also, if the waiting threads in lock has a higher priority than the current thread, there will be priority donation. 

Waiters in the semaphores are sorted in descending order(as predicted) and `sema_up` wakes up the thread from the front and due to above constraint, that thread has the highest priority


```c
void thread_set_priority(int new_priority)
{
  enum intr_level old_level = intr_disable();
  thread_current()->real_priority = new_priority;
  thread_update_priority(thread_current());
  intr_set_level(old_level);
  check_then_thread_yield();
}

```

```c
bool locks_by_priority(const struct list_elem *a, const struct list_elem *b, void *aux)
{
  return list_entry(a, struct lock, elem)->max_priority <=
         list_entry(b, struct lock, elem)->max_priority;
}
```

Sets the real priority of the currrent thread and update the priority on the basis list of thread requesting the same lock. The `thread_update_priority(`) function has been created for this. 

The `compare_locks_by_priority` comparator compares the thread on the basis of  priority. 

**thread_unblock()**

Thread unblock is called when we have to unblock/wake thread from sleep. Earlier we just pushed back the thread into ready_list, but now we need to add it in accordance with priority. 

```c
void thread_unblock(struct thread *t)
{
  enum intr_level old_level;

  ASSERT(is_thread(t));

  old_level = intr_disable();
  ASSERT(t->status == THREAD_BLOCKED);

  /* Initially we were putting the new element at end of list, but now we have to maintain order hence use list_insert_ordered */
  list_insert_ordered(&ready_list, &t->elem, compare_priority, 0);
  // list_push_back (&ready_list, &t->elem);
  t->status = THREAD_READY;
  intr_set_level(old_level);
}
```

Also, `thread_yield` has also been modified to support above logic.


 ***Note:*** We have also created `thread_create()`, `thread_set_priority()`, `update_thread_priority()` and other functions which acts as base or helper function to satisfy the above constraint. Below is quick glance to some of them.

 
```c
void check_then_thread_yield(void)
{
  enum intr_level old_level = intr_disable();
  bool check = !list_empty(&ready_list) &&
               list_entry(list_front(&ready_list), struct thread, elem)->priority > thread_get_priority();
  intr_set_level(old_level);
  if (check)
    thread_yield();
}
```

```c
void thread_update_priority(struct thread *t)
{
  enum intr_level old_level = intr_disable();
  int real_priority = t->real_priority;
  if (list_empty(&t->locks))
    t->priority = real_priority;
  else
  {
    int lock_priority = list_entry(list_max(&t->locks, locks_by_priority, NULL),
                                   struct lock, elem)
                            ->max_priority;

    /* priority donation*/
    t->priority = real_priority > lock_priority ? real_priority : lock_priority;
  }
  intr_set_level(old_level);
}
```

2. **lock_acquire():** 

The main function of lock_acquire is to do priority donation. We start by checking whether lock is held or not. Then update the priority if holder's priority is less than the priority of the current thread. Following function is responsible to rearrange the `ready_list` in accordance with the `holder_thread`. We need to repeat the process if the lock is locked by other locks.

```c
void rearrange_ready_threads(struct thread *t)
{
  ASSERT(t->status == THREAD_READY);

  enum intr_level old_level = intr_disable();
  list_remove(&t->elem);
  list_insert_ordered(&ready_list, &t->elem, compare_priority, NULL);
  intr_set_level(old_level);
}
```

`sema_down()`, then the current thread will hold the lock. We put the lock into the struct to hold the locks of the thread. Then update the priority of lock and current thread. If next thread to run in the ready_list has a higher priority than the current thread, yield the CPU.

3. **Releasing a Lock:** \
Lock is released when thread is done performing its function and now can release the lock. To perform this operation, we set the holder as NULL and remove the lock from the list of held locks. Then update the priority of the current thread, if the lock is the last lock, then restore the real priority of the current thread. 


#### Synchronization ####

Disabling the interrupt makes the operations like `thread_set_priority()` atomic which avoids the race conditions. When priority of the current thread is smaller that any of the present threads in `ready_list` there should be CPU yielding. But since we disable the interrupt while changing priority and CPU yielding, these processes can be considered as atomic.

#### Rationale ####

In this example, we should be able to use different methods to sort the list, which should increase the efficiency of the program. Also, here, we have lot of functions which are crucial but easily accessible to the user. Hence we must define functions as private(if possible in C) which are supposed to have admin/superuser authorization. 


### OutCome of the Assignment ###
1. **Alarm Clock:**

![Image alt](/img/cs301/alarm-clock.png)

2. **Priority Scheduling:**

![Image alt](/img/cs301/ps.png)
