---
title: ReentrantLock
date: 2019-09-11 13:30:58
tags:
- ReentrantLock
- J.U.C
- Concurrency
categories:
- Concurrency
---
# Data Structure 
![basic structure](/images/reentrantlock1.png)
As shown in above picture, `ReentrantLock` has only one property: `sync` of type `Sync`, which extends from `AbstracQueueSynchronizer`, `AQS` in brief. `AQS` is a synchronzier for many classes in J.U.C, allowing programmers implementing their own Lock without dealing with the detail of maintainning a synchronizing queue.

# AbstractQueueSynchronizer
`AQS` maintains a `FIFO` queue ,  a `int` and a `Thread` type properties inside. The `state` is set to be `0` when no one owns the synchronizer. Every thread try to get the ownership of synchronizer by an atomic instruction `CAS` provided by CPU. Threads will try to set  `state` to `1` by `unsafe.compareAndSwapInt(this, stateOffset, 0, 1);`, the first and second parameters together make the address of `state`, the third parameter `0` indicates the old value of `state`, and the last indicates the value we'd like to set. If `state` is not `0`, this function won't set it to `1` and returns false, otherwise it returns ture. This operation is atomic so only one thread can set `state` successfully, and it will set the ownership of the synchronizer by `exclusiveOwnerThread = Thread.currentThread();`.

By far, we have make clear how a thread get the ownership of the synchronizer sucessfully, but what will the most threads that fails in the competition do? They just wrap themseleves up as a `Node` and append to the waiting queue in the synchronizer, and then they will choose to spin themselves or sleep.

`spin` is actually a infinite loop with an operation in it. Here is an example:

```
for(;;)
if(compareAndSetState(0,1))break;
```

The thread tries to set state again and again if they fails. It works well if the competition of threads is not too fierce. Nevertheless, think about one thread holds the synchronizer for a long time, and during that time, other threads have to spin again and again, it's a waste of CPU resource! So only the thread next to head node in the waiting queue will do so. All others threads will invoke `LockSupport.park()` to sleep themselves until the thread before it wake them up by `LockSupport.unpark(thread)`(in the queue, they are predecessor node and sucessor node).

# ReentrantLock
`AQS` has done most jobs for `ReentranLock`:
- the mutex: `state`
- how to get the mutex: `CAS`
- what will a thread do after fails to get the mutex: wrap it up as `Node` , push it into the waiting queue, and then choose to spin or sleep.
- what will the thread do after get the mutex: wrap it up as a `Node`, set itself as the head node of waiting queue. When it finishes its task, it will set the `state` to `0` and wake the successor `Node` up in the waiting queue. 

`ReentrantLock` means a thread can get the synchronizer many times. The crutial part is it overrided the method for acquire `state` in `AQS`. After fails to set `state` from 0 to 1, the current thread will check if `exclusiveOwnerThread == Thread.currentThread()`. If so, increament `state` by 1, and decrease it after one thread finished its task and try to release the synchronizer.