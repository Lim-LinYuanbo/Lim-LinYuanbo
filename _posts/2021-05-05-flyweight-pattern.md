---
layout: mypost
title: 享元模式
categories: [设计模式,RTOS,编码笔记]
---

维基百科说明：[享元模式](https://en.wikipedia.org/wiki/Flyweight_pattern)

> 享元模式（英语：Flyweight Pattern）是一种软件设计模式。它使用物件用来尽可能减少内存使用量；于相似物件中分享尽可能多的资讯。当大量物件近乎重复方式存在，因而使用大量内存时，此法适用。通常物件中的部分状态(state)能够共享。常见做法是把它们放在数据结构外部，当需要使用时再将它们传递给享元。

参考项目：[https://github.com/Pithikos/C-Thread-Pool.git](https://github.com/Pithikos/C-Thread-Pool.git)

---

这是一个简单的线程池实现源码，使用方法也很简单：

1. 添加头文件 `#include "thpool.h"`；
2. 创建线程池，填入最大线程数量 `threadpool thpool = thpool_init(4);`；
3. 添加任务加入线程池 `thpool_add_work(thpool, (void*)function_p, (void*)arg_p);`；

函数名：`struct thpool_* thpool_init(int num_threads)`

函数理解：一些简单的初始化工作，其中最主要的就是根据线程池数量，创建多个线程，这些已经创建的线程，运行的任务是 `static void* thread_do(struct thread* thread_p)`。

```c
/* Initialise thread pool */
struct thpool_* thpool_init(int num_threads){

    threads_on_hold   = 0;
    threads_keepalive = 1;

    if (num_threads < 0){
        num_threads = 0;
    }

    /* Make new thread pool */
    thpool_* thpool_p;
    thpool_p = (struct thpool_*)malloc(sizeof(struct thpool_));
    if (thpool_p == NULL){
        err("thpool_init(): Could not allocate memory for thread pool\n");
        return NULL;
    }
    thpool_p->num_threads_alive   = 0;
    thpool_p->num_threads_working = 0;

    /* Initialise the job queue */
    if (jobqueue_init(&thpool_p->jobqueue) == -1){
        err("thpool_init(): Could not allocate memory for job queue\n");
        free(thpool_p);
        return NULL;
    }

    /* Make threads in pool */
    thpool_p->threads = (struct thread**)malloc(num_threads * sizeof(struct thread *));
    if (thpool_p->threads == NULL){
        err("thpool_init(): Could not allocate memory for threads\n");
        jobqueue_destroy(&thpool_p->jobqueue);
        free(thpool_p);
        return NULL;
    }

    pthread_mutex_init(&(thpool_p->thcount_lock), NULL);
    pthread_cond_init(&thpool_p->threads_all_idle, NULL);

    /* Thread init */
    int n;
    for (n=0; n<num_threads; n++){
        thread_init(thpool_p, &thpool_p->threads[n], n);
#if THPOOL_DEBUG
            printf("THPOOL_DEBUG: Created thread %d in pool \n", n);
#endif
    }

    /* Wait for threads to initialize */
    while (thpool_p->num_threads_alive != num_threads) {}

    return thpool_p;
}
```

---

函数名：`static void* thread_do(struct thread* thread_p)`

函数理解：

1. 这个任务等一个条件变量，即：`bsem_wait(thpool_p->jobqueue.has_jobs);`，当没有条件变量时，所有的任务都处于挂起休眠状态，触发这个条件变量的函数是 `int thpool_add_work(thpool_* thpool_p, void (*function_p)(void*), void* arg_p)`。
2. 当任务完成时，将任务从任务链表中删除，再次等待条件变量，如果有任务，则运行下一个任务，否则进入休眠状态；

```c
/* What each thread is doing
*
* In principle this is an endless loop. The only time this loop gets interuppted is once
* thpool_destroy() is invoked or the program exits.
*
* @param  thread        thread that will run this function
* @return nothing
*/
static void* thread_do(struct thread* thread_p){

    /* Set thread name for profiling and debuging */
    char thread_name[32] = {0};
    snprintf(thread_name, 32, "thread-pool-%d", thread_p->id);

#if defined(__linux__)
    /* Use prctl instead to prevent using _GNU_SOURCE flag and implicit declaration */
    prctl(PR_SET_NAME, thread_name);
#elif defined(__APPLE__) && defined(__MACH__)
    pthread_setname_np(thread_name);
#else
    err("thread_do(): pthread_setname_np is not supported on this system");
#endif

    /* Assure all threads have been created before starting serving */
    thpool_* thpool_p = thread_p->thpool_p;

    /* Register signal handler */
    struct sigaction act;
    sigemptyset(&act.sa_mask);
    act.sa_flags = 0;
    act.sa_handler = thread_hold;
    if (sigaction(SIGUSR1, &act, NULL) == -1) {
        err("thread_do(): cannot handle SIGUSR1");
    }

    /* Mark thread as alive (initialized) */
    pthread_mutex_lock(&thpool_p->thcount_lock);
    thpool_p->num_threads_alive += 1;
    pthread_mutex_unlock(&thpool_p->thcount_lock);

    while(threads_keepalive){

        bsem_wait(thpool_p->jobqueue.has_jobs);

        if (threads_keepalive){

            pthread_mutex_lock(&thpool_p->thcount_lock);
            thpool_p->num_threads_working++;
            pthread_mutex_unlock(&thpool_p->thcount_lock);

            /* Read job from queue and execute it */
            void (*func_buff)(void*);
            void*  arg_buff;
            job* job_p = jobqueue_pull(&thpool_p->jobqueue);
            if (job_p) {
                func_buff = job_p->function;
                arg_buff  = job_p->arg;
                func_buff(arg_buff);
                free(job_p);
            }

            pthread_mutex_lock(&thpool_p->thcount_lock);
            thpool_p->num_threads_working--;
            if (!thpool_p->num_threads_working) {
                pthread_cond_signal(&thpool_p->threads_all_idle);
            }
            pthread_mutex_unlock(&thpool_p->thcount_lock);

        }
    }
    pthread_mutex_lock(&thpool_p->thcount_lock);
    thpool_p->num_threads_alive --;
    pthread_mutex_unlock(&thpool_p->thcount_lock);

    return NULL;
}
```

---

理解总结：

1. 在某些大量并且需要及时处理的消息情况下，似乎可以使用线程池这种方式，快速的响应每条消息，每条消息使用单独的线程去完成。
2. 其实也存在某些长时间需要处理的任务，比如一些轮询任务，状态机任务，这些任务重要但不紧急，这种情况似乎可以使用一个空闲线程添加任务链的方式去完成。
3. 对于享元模式理解并不充分，后续待完善。
