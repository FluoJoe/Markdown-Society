*This project has been created as part of the 42 curriculum by cjegouss*

# Milestone 3 - PHILOSOPHERS

![Philosophers_img](https://www.athensinsiders.com/storage/1629/conversions/Greek-philosopher-card_big.jpg)

## Description

Philosophers is a C programming project based on the classic dining philosophers problem. Philosophers sit around a circular table and must eat, sleep, and think in turn. To eat, each philosopher needs two forks: the one on the left and the one on the right.

The main goal of this project is to learn thread management, since each philosopher is represented by a separate thread running concurrently with the others. Because they all share the same resources, synchronization becomes essential. Mutex management is therefore a key concept, as it allows control over access to shared data and prevents race conditions.

In my implementation, the right fork belongs to each philosopher. However, forks are shared between neighbors, so every fork must be protected by a mutex, since it can be borrowed by the philosopher seated next to it. This ensures that only one philosopher can use a fork at a time.

Synchronization and precise time management are the third and final key concepts to understand, as the program must accurately control actions such as eating, sleeping, and detecting when a philosopher dies.

<img src="https://www.adit.io/imgs/dining_philosophers/at_the_table.png" alt="Ron is philosophing" width="500"/>


### THREADS

A thread is a a sequence of instructions executed within a process. Modern operating systems allow multiple threads to run in parallel within the same program. Each thread has its own context, including a unique identifier, its own stack, instruction pointer, and CPU registers. However, since all threads belong to the same process, they share the same virtual address space: the same code, heap, shared libraries, and open file descriptors (has represented in the image below). 

Since threads share the same memory space, communication between them is easier than between separate processes. However, this shared memory can also lead to synchronization issues if not managed properly.

![thread memory ](https://miro.medium.com/v2/resize:fit:640/format:webp/1*fCuzTojSqkUJgfBZYRxe_w.gif)

<p align="center">
Monitoring thread is checking if everyone is alive, he is vigilant like this guy:
</p>

<p align="center">
 <img src="https://static.wikia.nocookie.net/villains-fr/images/e/ed/5ef093d7d792d8a3f7e27316_kirikou_5.jpg/revision/latest?cb=20231001155437&path-prefix=fr" /> 
</p>

### DATA RACE 

A data race occurs when two or more threads access the same memory location at the same time, and at least one of them modifies it, without proper synchronization. According to common definitions, such as the one found on Wikipedia, a data race is a type of race condition where the program’s behavior depends on the unpredictable timing of concurrent threads.

For example, imagine a shared counter currently set to 10. Thread 1 reads the value 10 into its register and increments it to 11. Before it can write the new value back to memory, Thread 2 also reads the value 10 and increments it to 11. Both threads then store their result. Instead of the counter becoming 12, it ends up as 11. One increment is lost because the operations overlapped.

This problem happens because the threads access and modify the same memory simultaneously. The final result depends on how the operating system schedules the threads, which is not predictable. Without synchronization mechanisms such as mutexes, data races can lead to incorrect and inconsistent results.

example : 

What we want 

| Step | Thread 1       | Thread 2       | value |
|------|----------------|----------------|-------|
| 0    |                |                |   0   |
| 1    | read value     |                |   0   |
| 2    | increase value |                |   0   |
| 3    | write back     |                |   1   |
| 4    |                | read value     |   1   |
| 5    |                | increase value |   1   |
| 6    |                | write back     |   2   |

What is actually happening if we have no protection 

| Step | Thread 1       | Thread 2       | value |
|------|----------------|----------------|-------|
| 0    |                |                |   0   |
| 1    | read value     |                |   0   |
| 3    | increase value |                |   0   |
| 2    | read value     |                |   0   |
| 4    |                | increase value |   0   |
| 5    | write back     |                |   1   |
| 6    |                | write back     |   1   |

### MUTEX 
<img src="https://media.adeo.com/media/1424944/cadenas5d4c1bfdf4fc290001a26ca9.jpg" alt="cadena" width="400"/> <br>

A mutex (short for mutual exclusion) is a synchronization primitive used to protect shared resources when multiple threads run concurrently. It acts like a lock that ensures only one thread can access a critical section or shared data at a time, helping prevent data races and maintain correct program behavior.

You can think of a mutex like a single checkout line at a store with only one cashier available. Only one customer (thread) can be served at a time, while the others must wait in line until the resource becomes available again.

To avoid deadlocks, it is essential to unlock every mutex that has been initialized and previously locked.

### SEMAPHORE

<img src="https://cdn.britannica.com/72/19172-050-9D91EBCF/Semaphore-tower.jpg" alt="semaphore" width="500"/> <br>

A semaphore is a data structure which is maintained by the operating system and contains:

- an integer which stores the value, positive or zero, of the semaphore.
- a waiting line which contains pointers to the threads which are blocked waiting on this semaphore. <br>

A semaphore implementation generally consists of four functions:

- An initialization function that creates the semaphore and assigns it an initial value of zero or a positive number. By Posix convention semaphors name's started by a /. 
- A function that destroys a semaphore and frees the resources associated with it.
- A post function that is used by threads to modify the value of the semaphore. If there are no threads waiting in the waiting line associated with the semaphore, its value is incremented by one. Otherwise, one of the waiting threads is released and moves to the Ready state.
- A wait function that is used by threads to test the value of a semaphore. If the value of the semaphore is positive, it is decremented by one and the function succeeds. If the semaphore has a value of zero, the thread is blocked until another thread unblocks it by calling the post function.

Semaphores are used to solve many coordination problems. Since they can store an integer value, they are more flexible than mutexes, which are mainly useful for mutual exclusion problems.

## Instructions

Philosophers works with 4 to 5 arguments.<br>

First argument → number of philosophers (also number of forks). <br>  
Second argument → time to die (in milliseconds). If a philosopher has not started eating within this time since the start of their last meal or since the start of the simulation, they die.<br>  
Third argument → time to eat (in milliseconds), which is the time it takes for a philosopher to eat. During this time, they must hold two forks.<br>  
Fourth argument → time to sleep (in milliseconds), which is the time a philosopher will spend sleeping.<br>  
Fifth argument → This argument is optional, it represents the number of times each philosopher must eat. If all philosophers have eaten at least this number of times, the simulation stops. If not specified, the simulation stops when a philosopher dies. <br>

### Serie of tests and expected behavior

| Arguments                      | Expected Result                                                |
|--------------------------------|----------------------------------------------------------------|
| ./philo 1 200 200 200          | Philosopher 1 takes only one fork and dies after ~200 ms       |
| ./philo 2 800 200 200          | No philosopher dies                                            |
| ./philo 5 800 200 200          | No philosopher dies                                            |
| ./philo 5 800 200 200 7        | Simulation stops when each philosopher has eaten 7 times       |
| ./philo 4 410 200 200          | No philosopher dies                                            |
| ./philo 4 310 200 200          | One philosopher dies                                           |
| ./philo 4 500 200 1.2          | Invalid argument                                               |
| ./philo 4 0 200 200            | Invalid argument                                               |
| ./philo 4 -500 200 200         | Invalid argument                                               |
| ./philo 4 500 200 2147483647   | One philosopher dies after 500 ms                              |
| ./philo 4 2147483647 200 200   | No philosopher dies                                            |
| ./philo 4 214748364732 200 200 | Invalid argument                                               |
| ./philo 4 200 210 200          | One philosopher dies (death must be printed before 210 ms)     |

## Road to bonuses: an epic journey 

Good evening, voyager. Let me tell you a little tale. Our story begins in 2026, on the 5th of March, when I failed this project for the first time…
<p align="center">
<img src="https://images.meme-arsenal.com/69ee4d968202d61cd2ecd5c6cb6b1b2e.jpg" alt="on the ground" width="700"/>
</p>

My pride was wounded, but my determination remained intact. Alas, philosophers were not eating properly, once one had finished eating, he would send a signal to the others. As one, they would shut down, dropping their forks and their ideas, returning to oblivion. That was my first mistake… and not my last. I came back stronger, carrying with me one foolish idea: *"Since I have to try again, why not attempt the bonuses as well?"* 

<p align="center">
<img src="https://focus.huffingtonpost.fr/2022/07/23/0/35/558/314/1820/1023/75/0/a33f860_1658549214195-5c936fec230000c800e94709.jpeg" alt="Maximus" width="700"/>
</p>

I was like a thoughtless child, yanking leaves in a garden without care. But then reality struck me hard. On March 12th, I validated the project my philosophers finally eating properly, leaving the table content, their bellies full. Yet the bonuses were nowhere near being validated.

<img src="https://media.tenor.com/4SP8JrTcZdYAAAAM/despair.gif" alt="despear" width="200"/>

The bonuses were leaking like the tears running down my face as I write these words (just kidding, if you haven’t noticed by now, I do enjoy being dramatic). I now have one mission, one goal in life -> to validate the bonuses. Along the way, I met friends who showed me the way. I hope, I will myself lead the way for others...

You who read these words, be assured that I take this project very seriously. I only hope that the 125 validation is near.

<p align="center">
<img src="https://pbs.twimg.com/media/Gi8HGwiXcAAemjt.jpg" alt="Yoda" width="700"/>
</p>

## Ressources

### Ressources to develop and undertand the project <br> 

1. Thread basis: https://www.codequoi.com/threads-mutex-et-programmation-concurrente-en-c/
2. Crash course on the project by a 42 student: https://www.youtube.com/watch?v=UGQsvVKwe90
3. Wikipeda: https://en.wikipedia.org/wiki/Race_condition#Data_race / https://en.wikipedia.org/wiki/Mutual_exclusion / https://fr.wikipedia.org/wiki/S%C3%A9maphore_%28informatique%29 / 
4. Serie of videos about threads semaphores and mutexes: https://www.youtube.com/watch?v=d9s_d28yJq0&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2 <br> 
5. Introduction to threads, semaphores and mutexes: https://sites.uclouvain.be/SyllabusC/notes/Theorie/Threads/coordination.html
6. Semaphore documentation: https://tala-informatique.fr/index.php?title=C_semaphore
7. Video about semaphores: https://www.youtube.com/watch?v=ukM_zzrIeXs
8. Wait and kill process: https://www.codequoi.com/en/creating-and-killing-child-processes-in-c/#waiting-to-avoid-zombies

### Linux man allowed function <br> 

Functions I haven't detailed underneath because we used them a lot before : printf / malloc / write / free.

1. Memory:
- memset() - fill memory with constant byte https://man7.org/linux/man-pages/man3/memset.3.html <br> 
2. Time functions: 
- usleep() - Suspend execution for microseconds https://man7.org/linux/man-pages/man3/usleep.3.html
- gettimeofday() - Get current time https://man7.org/linux/man-pages/man2/gettimeofday.2.html <br> 
3. Threads: 
- pthread_create() - Create a new thread https://man7.org/linux/man-pages/man3/pthread_create.3.html
- pthread_detach() - Detach a thread from main execution https://man7.org/linux/man-pages/man3/pthread_detach.3.html
- pthread_join() - Wait for thread termination https://man7.org/linux/man-pages/man3/pthread_join.3.html <br> 
4. Mutex functions
- pthread_mutex_init() - Initialize a mutex https://man7.org/linux/man-pages/man3/pthread_mutex_init.3.html
- pthread_mutex_destroy() - Destroy a mutex https://man7.org/linux/man-pages/man3/pthread_mutex_destroy.3.html
- pthread_mutex_lock() - Lock a mutex https://man7.org/linux/man-pages/man3/pthread_mutex_lock.3.html
- thread_mutex_unlock() - Unlock a mutex https://man7.org/linux/man-pages/man3/pthread_mutex_unlock.3.html<br>

### Linux man allowed function for the bonuses <br> 

Functions I haven't detailed underneath because we used them before or they are detailled above : memset / printf / malloc / free / write/ fork / exit / printf / write / free / thread_create / pthread_detach / pthread_join / usleep / gettimeofday / waitpid.

1. Semaphores :
- Overview of semaphores https://man7.org/linux/man-pages/man7/sem_overview.7.html 
- sem_open() - initialize and open a named semaphore https://man7.org/linux/man-pages/man3/sem_open.3.html <br>
- sem_close() -  close a named semaphore https://man7.org/linux/man-pages/man3/sem_close.3.html <br>
Return value  On success sem_close() returns 0. On error it is returning -1, with errno set to indicate the error.
- sem_post() - unlock a semaphore https://man7.org/linux/man-pages/man3/sem_post.3.html <br>
Return value:  sem_post() returns 0 on success; on error, the value of the semaphore is left unchanged, -1 is returned, and errno is set to indicate the error.
- sem_wait() - lock a semaphore https://man7.org/linux/man-pages/man3/sem_wait.3.html <br>
Return value:  All of these functions return 0 on success; on error, the value of  the semaphore is left unchanged, -1 is returned, and errno is set to indicate the error.
- sem_unlink() - remove a named semaphore https://man7.org/linux/man-pages/man3/sem_unlink.3.html <br>
Return value: On success sem_unlink() returns 0; on error, -1 is returned, with errno set to indicate the error.<br>
2. Kill :
- kill() - send a signal to the process https://man7.org/linux/man-pages/man2/kill.2.html / https://www.youtube.com/watch?v=qXP4F49K1XM / https://www.youtube.com/watch?v=83M5-NPDeWs <br>
Return value: On success, zero is returned.  If signals were sent to a process group, success means that at least one signal was delivered.  On error, -1 is returned, and errno is set to indicate the error.

### Pop culture references

<img src="https://substackcdn.com/image/fetch/$s_!6GYP!,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fsubstack-post-media.s3.amazonaws.com%2Fpublic%2Fimages%2Fecb7e1c3-8104-433c-b599-6badd6b4ee35_1024x576.jpeg" alt="Yoda" width="700"/>

- Parks and Recreation (good tv show)
- Kirikou
- Papyrus https://www.youtube.com/watch?v=jVhlJNJopOQ
- Optimus prime (For my inspirational story) https://www.youtube.com/watch?v=dJszUl1EI4A
- Gladiator (If you want to see a very cringe Joaquin Phoenix)
- Star Wars
- Arakawa Under the Bridge https://fr.wikipedia.org/wiki/Arakawa_Under_the_Bridge
- The hunger game
- Wait a minute https://www.youtube.com/shorts/KyQ6VeywlSI  
- Internet (in general)

