# Understanding Threading in Java: A Practical Guide
## Introduction
Multithreading is a powerful concept in Java that allows you to execute multiple tasks concurrently, enhancing the performance of your programs. In this guide, we'll explore different threading approaches using a simple example.

## The Scenario
Consider a scenario where you have a list of tasks to be executed, each with a specific duration. We'll implement various threading approaches to understand their implications.

## Breaking Down the Code

### Main Class
```
public class ThreadingExample {
    public static void main(String args[]) throws Exception {
        System.out.println(" : -----------Main Program Started-----------");
        
        // Different approaches that we will look below
        // 1 - Sync Approach
        // 2 - Async Approach
        // 3 - etc ..
    }
}
```

> The MyWork class represents a task with a name and a specified duration. The ```execute()``` method simulates the task execution.
```
class MyWork {
    String name;
    int delay;
    
    MyWork(String name, int duration){
        this.name = name;
        this.delay = duration;   
    }

    public void execute(){
        util.println("Started : " + this.name);
        try{
          Thread.sleep(this.delay);
        } catch(InterruptedException e){}
        util.println("Finished : " + this.name + ", took " + (this.delay/1000) + " seconds");
    }
}
```

> The util class provides a utility method println to print messages with a timestamp.
```
class util {
    public static void println(String str){
        String timeStamp = LocalTime.now().format(DateTimeFormatter.ofPattern("HH:mm:ss"));
        System.out.println(timeStamp + " : " + str);
    }
}
```

### Sync Approach
In the synchronous approach, tasks are executed one after another in a sequential manner. This ensures simplicity but will lead to increased execution time.

```
// Sync approach
System.out.println("-------------------------Sync Approach-------------------------");
prefix = "1-";
for(int i = 0; i < tasks.size(); i++){
    MyWork work = new MyWork(prefix + tasks.get(i), durations.get(i));
    work.execute();
}
```

### Async-1 Approach
In the first asynchronous approach, each task is executed in a separate thread, enabling concurrent execution. However, there's no coordination between threads.

```
// Async-1 approach
System.out.println("-------------------------Async 1 Approach-------------------------");
prefix = "2-";
for(int i = 0; i < tasks.size(); i++){
    MyWork work = new MyWork(prefix + tasks.get(i), durations.get(i));
    Thread t = new Thread(()->work.execute());
    t.start();
}
```

### Async-2 Approach
The second asynchronous approach introduces a ```join()``` method to make each thread wait for the previous one to finish. This helps in maintaining order but sacrifices some parallelism.

```
// Async-2 approach
System.out.println("-------------------------Async 2 Approach-------------------------");
prefix = "3-";
for(int i = 0; i < tasks.size(); i++){
    MyWork work = new MyWork(prefix + tasks.get(i), durations.get(i));
    Thread t = new Thread(()->work.execute());
    t.start();
    t.join(); // each thread will wait for the previous one to finish
}
```

### Async-3 Approach
In the third asynchronous approach, we use a list of threads and join them after starting to ensure they don't run concurrently. This combines parallelism and order.

```
// Async-3 approach
System.out.println("-------------------------Async 3 Approach-------------------------");
prefix = "4-";
ArrayList<Thread> threads = new ArrayList<Thread>(); 
for(int i = 0; i < tasks.size(); i++){
    MyWork work = new MyWork(prefix + tasks.get(i), durations.get(i));
    Thread t = new Thread(()->work.execute());
    threads.add(t);
    t.start();
}
// The for loop to allow the threads to join
for(int i = 0; i < threads.size(); i++){
    threads.get(i).join(); // threads will not wait for others to finish
}
```

# Output
The above code when run together, produces this output. Have a look at how **the main thread and spawned threads interact**
> ```-----Sync Approach----``` this is getting printed from main thread.
> ```Started :```, ```Finished :``` these are getting printed from spawned threads.

```
21:36:43 :  : -----------Main Program Started-----------
-------------------------Sync Approach-------------------------
21:36:43 : Started : 1-Task A
21:36:44 : Finished : 1-Task A,took 1 seconds
21:36:44 : Started : 1-Task B
21:36:45 : Finished : 1-Task B,took 1 seconds
21:36:45 : Started : 1-Task C
21:36:46 : Finished : 1-Task C,took 1 seconds
21:36:46 : Started : 1-Task D
21:36:48 : Finished : 1-Task D,took 1 seconds
-------------------------Async 1 Approach-------------------------
21:36:48 : Started : 2-Task A
21:36:48 : Started : 2-Task C
21:36:48 : Started : 2-Task B
-------------------------Async 2 Approach-------------------------
21:36:48 : Started : 2-Task D
21:36:48 : Started : 3-Task A
21:36:49 : Finished : 2-Task A,took 1 seconds
21:36:49 : Finished : 3-Task A,took 1 seconds
21:36:49 : Started : 3-Task B
21:36:49 : Finished : 2-Task B,took 1 seconds
21:36:49 : Finished : 2-Task C,took 1 seconds
21:36:49 : Finished : 2-Task D,took 1 seconds
21:36:50 : Finished : 3-Task B,took 1 seconds
21:36:50 : Started : 3-Task C
21:36:51 : Finished : 3-Task C,took 1 seconds
21:36:51 : Started : 3-Task D
21:36:53 : Finished : 3-Task D,took 1 seconds
-------------------------Async 3 Approach-------------------------
21:36:53 : Started : 4-Task A
21:36:53 : Started : 4-Task B
21:36:53 : Started : 4-Task C
21:36:53 : Started : 4-Task D
21:36:54 : Finished : 4-Task A,took 1 seconds
21:36:54 : Finished : 4-Task B,took 1 seconds
21:36:54 : Finished : 4-Task C,took 1 seconds
21:36:54 : Finished : 4-Task D,took 1 seconds
21:36:54 :  : -----------Main Program Ended-----------
```

# Explanation
Each threading approach demonstrates a different way of handling concurrent tasks. Here's a breakdown:

> Sync Approach: Tasks are executed sequentially, one after the other. This is **straightforward** and will lead to increased execution time.

> Async-1 Approach: Each task runs in a separate thread, enabling concurrent execution. However, there's no coordination between threads.

> Async-2 Approach: Introducing join() ensures that each thread waits for the previous one to complete. This helps in maintaining order but sacrifices some parallelism.

> Async-3 Approach: Using a list of threads allows you to control the execution order. Threads are started first and then joined in sequence, combining parallelism and order.

Summary
Understanding threading in a multithreading language is crucial for optimizing performance. The synchronous approach is simple but may not be efficient for time-consuming tasks. Asynchronous approaches provide parallelism but require careful coordination to avoid issues.

1/2
## Next : Concurrency and Parallelism and Javascript.
