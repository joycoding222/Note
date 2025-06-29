# ThreadPool



3. 条件变量设置
4. 生产者：用户；消费者：线程池
5. 线程函数定义在了线程池对象里面，需要绑定给线程bind操作

```cpp
/**
*把threadpool里面的线程函数threadfunc,用绑定器绑定成一个函数对象，把threadpool对象绑定到线程函数类型的this指针上
/
```

线程返回值不同

构建any类型



关注task对象的生命周期



注意：在Linux中result对象会析构，在windows平台好像不同，后期课程注意听一下



## 知识点

1. 线程同步：
   线程互斥：mutex（互斥锁） atomic(简单操作适用，基于CAS操作)
   线程通信：条件变量condition_variable + 信号量semaphore

   > CAS: compare and swap
   >
   > 在寄存器内加减，和内存里的值交换

2. 基于互斥锁和条件变量实现信号量类

3. 在 C++ 中，`std::condition_variable` 的 `wait()` 方法在阻塞等待时会**自动解锁关联的互斥量**，这是其设计的核心机制



## 设计细节：

1. 任务队列中存放抽象任务类型的 shared_ptr：
   用户更关注任务的 run() 方法，可能传递进来一个临时的任务对象，因此设计时需要考虑到对象的生命周期，当执行完任务的 run() 方法时，才结束生命周期，并自动释放资源

2. 任务数量的设计需要考虑到线程安全：

3. 用户线程向线程池提交任务，最多等待1s，否则提交失败（服务降级）

   ```cpp
   // C++11 条件变量
   wait
   ```

4. 调试过程中出现了

   a.out: ../nptl/pthread_mutex_lock.c:81: __pthread_mutex_lock: Assertion `mutex->__data.__owner == 0' failed.
   Aborted (core dumped)

   原因可能是：主线程结束过早，因此在Main()中加入了getchar()

   调试方法：
   使用线程检查工具（参考DS的回答）



**线程池**

1. 线程池启动函数设计 ThreadPool::start() ：
   - 设置初始化线程数量
   - 创建线程对象并放入线程池（线程对象通过unique_ptr，使用了std::move）
   - 创建线程
   
2. 线程处理函数设计 ThreadPool::ThreadFunc() ：
   - 首先获取任务队列锁，保证任务队列线程安全
   - 阻塞等待任务队列非空
   - 任务队列非空，从任务队列中获取任务
   - 通过notFull通知生产者可以生产任务
   - 释放锁，使得线程池中的其他线程也可以操作任务队列（通过局部作用域右括号析构锁对象释放锁）
   - 执行任务

3. 用户向线程池提交任务函数 ThreadPool::submitTask() ：
   - 首先获取任务队列锁
   
   - 阻塞等待任务队列不满，最多等待1s，否则提交失败，对服务进行降级处理
   
   - 当任务队列不满，向任务队列中提交任务
   
   - 提交任务后，任务队列不空，通过notEmpty通知消费者可以获取任务
   
   - 返回值：用户提交任务函数的返回值，应该返回一个被result包装的task对象，而不是返回一个**task对象的getresult()方法**，原因：当线程池中的线程从任务队列中取出任务，并执行完后，task对象就被析构掉
   
     ```cpp
         // 用户向线程池提交任务
     Result ThreadPool::submitTask(std::shared_ptr<Task> sp)
     {
         // 获取锁
         std::unique_lock<std::mutex> lock(taskQueMtx_);
     
         // 等待任务队列有空余
         // 用户线程最多等待1s
         if (!notFull_.wait_for(lock, std::chrono::seconds(1),
             [&]()->bool{return taskQue_.size() < taskQueMax_;}))
             {
                 std::cerr << "task queue is full, submit task fail." << std::endl;
                 return task->getResult();	// 不行
                 return Result(Task);		// OK！！
             }
     
         // 如果有空余，将任务放入
         taskQue_.emplace(sp);
         taskQueSize_++;
     
         // 有任务放入，任务队列不空，通知
         notEmpty_.notify_all();
     
         // 返回
         return task->getResult();	// 不行...
         return  Result(task);		// OK!!!
     }
     ```
   
     



**线程**

1. 创建线程函数
   - 创建线程
   - 设置线程分离





1. 构建Any类型

   - Any类型用于接受用户submitTask的返回值，需要可以接受任意类型数据

   - 一个类型指向其他的任一类型（基类指向派生类）
   - Any类型底层是一个基类指针，因为基类指针可以指向派生类的对象
   - 当任一类型的data返回时，可以将其包装在派生类中，就可以返回给Any

2. 构建Result类型

   - 获取用户提交任务返回值
   - 阻塞（线程通信）



**Resule类型**

1. get方法：已经获取了result对象，从中获得any对象
   - 判断Result对象有效性
   - task任务如果没有执行完，阻塞用户线程
   - 返回any对象