# MemoryPool NOTE

## 设计过程

1. `MemoryPool`类

   - 静态成员方法`void* allocate(size_t size)`，
   - 静态成员方法`static void deallocate(void* ptr, size_t size)`，通过`ThreadCache`类型实例调用`deallocate`方法

2. `ThreadCache`类

   - 提供`static ThreadCache* getInstance()`方法，返回一个指向`ThreadCache`类型的实例的引用，并指定`thread_local`
   - 提供`void* allocate(size_t size);`方法，
   - 提供`void deallocate(void* ptr, size_t size);`方法，
   - 提供从中心缓存获取内存的成员方法
   - 提供将内存归还到中心缓存的成员方法
   - 提供判断是否需要归还内存到中心缓存的成员方法
   - 成员变量：
     - 记录线程内存块的地址的**隐式**链表`std::array<void*, FREE_LIST_SIZE>  freeList_`，利用内存块的头部存储下一节点地址
     - 记录线程内存块的数量的链表`std::array<size_t, FREE_LIST_SIZE> freeListSize_`

   特性：

   - **每个线程独立维护成员变量，无需锁**？？？
   - 每个线程独立实例，完全无锁操作

   ```cpp
   static thread_local ThreadCache instance;	//TLS 
   ```

   - 批量获取内存块链表而非单个内存块：获取了A内存块，将后续BCD等存入本地的内存块链表，并增加内存块计数；这样可以**减少与中心缓存的交换次数**
   - 大小分级策略：通过`sizeclass`类实现内存大小和索引的映射，**以及对齐内存大小？？**

3. `CentralCache`类

   - 提供`static CentralCache& getInstance()`方法，返回一个`CentralCache`类型的实例的引用
   - 提供`fetchRange(size_t index)`方法
   - 提供`returnRange(void* start, size_t size, size_t bytes)`方法
   - 提供从页缓存获取内存的成员方法`void* fetchFromPageCache(size_t size)`
   - 成员变量：
     - 记录每个桶的头指针的自由链表`std::array<std::atomic<void*>, FREE_LIST_SIZE> centralFreeList_`，原子指针是实现无锁链表的核心，确保了读写操作不会被打断，并通过内存序控制精确控制操作顺序
     - 中心缓存用于同步每个桶的独立的自旋锁`std::array<std::atomic_flag, FREE_LIST_SIZE> locks_`

   特性：

   链接线程缓存和页缓存；批量处理线程的内存请求，按照内存大小分级管理

4. `PageCache`类

   - 提供`static PageCache& getInstance()`成员方法，返回一个`PageCache`类型的实例的引用，`static`关键字的作用是控制临时变量的生命周期
   - 提供分配指定页数的成员方法
   - 提供释放指定页数的成员方法
   - 提供向系统申请内存的私有成员方法`systemAlloc(size_t numPages)`：计算需要申请的字节数，通过`mmap`向系统申请内存，并将内存清零，返回申请到内存的首地址
   - 成员变量：
     - 页大小`PAGE_SIZE = 4KB`
     - 结构体`span`：包含页起始地址、页数、链表指针。`span`是连续内存块的抽象
     - 页数到`span*`的映射`std::map<size_t, Span*>`按照页数管理span
     - 页地址到`span*`的映射`std::map<void*, Span>`用于内存回收
     - 互斥锁





## 流程说明

1. 申请内存过程：
   首先申请线程本地内存，如果线程本地自由链表不为空，代表线程本地有可用内存，返回可用内存块的地址；如果线程本地自由链表为空，则从中心缓存获取内存；
   从中心缓存获取内存后，取一个返回给申请内存的线程，其余的放入线程的自由链表，并更新自由链表的内存块数量，返回自由链表的首个内存块地址；
2. 若中心缓存为空，从页缓存获取新的内存块；
   首先互斥锁加锁，然后查找合适并且空闲的span；如果没有，则调用`systemAlloc`向系统申请；

## 具体知识点

1. constexpr

2. thread_local(threadcache)：消除多线程竞争

3. 内存中页的概念：

   内存页是将虚拟内存划分为固定大小的块，以便于管理和映射到物理内存，从而提高内存利用率

4. 自旋锁（centralcache)

   引入：

   多线程编程中，只有获取到锁的线程才能够对资源进行访问，那么没有获取到锁的线程有两种处理方式：一是阻塞起来（互斥锁），二是一直循环等待判断该资源是否已经释放锁而不用将线程阻塞（自旋锁）

   概念：

   当一个线程尝试去获取某一把锁的时候，如果这个锁此时已经被别人获取(占用)，那么此线程就无法获取到这把锁，该线程将会等待，间隔一段时间后会再次尝试获取。这种采用循环加锁 -> 等待的机制被称为`自旋锁(spinlock)`

   原理：

   如果持有锁的线程能够在短时间释放锁资源，那么等待线程就无需进行状态切换，只需要等待锁资源被释放即可，这样避免了用户进程和内核切换的消耗

   场景：

   适用于占用锁资源时间较短的场景；

   **不适用于需要长时间占有锁资源以及锁竞争激烈的情况**

   ```cpp
   #include <iostream>
   #include <atomic>
   #include <thread>
   using namespace std;
   /*
   1. atomic_flag: C++ 中最基础的原子类型
   特性：
       - 只有两种状态；set(true) clear(false)
       - 保证操作是原子的、无锁的
   方法：
       - test_and_set(): 测试当前值并设置为true
           - 返回值：false(锁未被占用，成功获取锁) true(锁已经被占用)
       - clear(): 将标志设置为false
   
   2. 内存序 memory order 在加锁过程中的应用
       - test_and_set(std::memory_order_acquire)   加锁时
       - clear(std::memory_order_release)  解锁时
   作用：
       确保多线程条件下，内存操作的顺序性
   
   */
   
   // 自旋锁的实现
   class spin_lock {
   public:
       spin_lock() = default;
       ~spin_lock() = default;
       spin_lock(const spin_lock&) = delete; 
       spin_lock& operator=(const spin_lock&) = delete;
   
       void lock() {   // acquire spin lock
           while (flag.test_and_set(std::memory_order_acquire))
           {}
       }
       void unlock() {   // release spin lock
           flag.clear(std::memory_order_release);
       }
   private:
       atomic_flag flag;
   };
   
   int num = 0;
   spin_lock splock;
   
   void addFunc(int index) {
       for (int i = 0; i < 10; ++i) {
           splock.lock();
           ++num;
           cout << "num = " << num << ", Thread: " << std::this_thread::get_id() << endl;
           splock.unlock();
       }
   }
   
   int main() {
       cout << "程序开始执行" << endl;
       thread t1(addFunc, 1);
       thread t2(addFunc, 2);
       thread t3(addFunc, 3);
       t1.join();
       t2.join();
       t3.join();
       return 0;
   }
   ```

   

5. 内存序

   内存序是多线程编程中控制内存访问顺序的机制，确保了在多线程编程中，数据的可见性和一致性；避免了编译器进行指令重排时可能导致的错误

6. 单例模式

   概念：

   确保一个类只有一个实例，并且提供全局访问点来获取实例

   实现：

   私有构造函数，防止外部创建实例；

   静态访问，提供全局访问点；

   静态局部变量，确保唯一实例；

   禁止拷贝构造/赋值，防止复制实例；

   ```cpp
   class PageCache
   {
   public:
       // 获取单例实例
       static PageCache& getInstance()
       {
   		static PageCache instance;	// 静态局部变量
           return instance;
       }
   private:
   	PageCache() = default;	// 私有构造函数
   };
   ```

   

7. `map`数据结构

8. TLS 线程本地化

   概念：

   线程本地存储是一种**内存管理机制**，它允许每个线程拥有变量的独立副本，这些副本在**线程生命周期内**保持独立，线程间互不可见。

   为什么需要TLS：

   - **避免锁竞争**：消除多线程访问共享数据时的同步开销
   - **提升性能**：减少缓存一致性协议导致的性能损失
   - **简化编程**：使线程特定数据的管理更直观
   - **保持状态**：维护线程特有的上下文信息

   [C++ 11 中的 Thread Local Storage (TLS)应用——实际效果就是将全局变量在各个线程都copy一份，互不干扰独立使用 - bonelee - 博客园 (cnblogs.com)](https://www.cnblogs.com/bonelee/p/17715357.html)

9. 隐式链表：基于隐式链表的无锁分配机制

   优势：不需要额外的空间存储链表节点，单线程环境下无需同步

   关键设计：

   每个内存块头部的8字节用于存储下一个块的地址，形成单链表

   分配时直接取链表的头节点，并更新头节点指针`freelist_[index]`指向下一节点

   `reinterpret_cast`安全地进行指针操作（不改变值，重新解释）

   ```cpp
   // 隐式链表
   if (void* ptr = freeList_[index]) // 获取链表头节点地址并赋值给ptr
   {
       freeList_[index] = *reinterpret_cast<void**>(ptr);	// 获取内存块头部的指针值（即下一个节点的地址）
       return ptr;
   }
   
   /*
   操作前： Head → A → B → C → NULL
   操作后： Head → B → C → NULL
           返回A
   */
   ```

10. 类型转换

    [C++类型转换之reinterpret_cast - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/33040213)





## 可能的问题：

1. Q: 为什么自旋锁使用atomic_flag而不是atomic bool

   A: "atomic_flag 是唯一保证无锁的原子类型，符合自旋锁的需求：

   - 极致轻量：通常只需1字节

   - 严格无锁：不依赖系统锁

   - 操作精简：只有 test_and_set 和 clear 方法，而 atomic<bool> 在某些平台可能使用锁实现"

2. Q: 自旋锁和互斥锁如何选择

   A: 当临界区操作的时间**小于**上下文切换的时间选择自旋锁（即尽量不切换线程，线程切换开销较低）

3. Q: 为什么需要内存序参数

   A: 使用内存序参数`std::memory_order_acquire`和`std::memory_order_release`保证临界区的内存可见性，防止了多线程环境下内存的重排问题；具体来说，A线程修改后释放锁，B线程看到的是最新的链表状态；获取锁时，使用`acquire`保证临界区的读写操作不会被重排到锁之前，`release`保证临界区的读写操作不会被重排到锁之后

4. Q: 为什么需要使用`std::this_thread::yield()`：

   A: 纯自旋会占用CPU全部时间片，降低系统性能；当锁竞争激烈时，yield() 出让当前线程的时间片,允许将时间片分配给其他就绪线程；

5. Q: 为什么需要线程本地缓存

   A: 避免全局锁竞争，减少系统调用开销

6. Q: 为什么用mmap而不是new

   A: 

   - **`new` 的局限性**：`new` 是 C++ 运算符，底层依赖堆内存管理器（如 glibc 的 `ptmalloc`）。堆内存管理器通过维护空闲块链表等方式管理内存，适合小内存块的快速分配（如几 KB 到几 MB）。但对于大内存块（如几十 MB 以上）或需要精确控制内存对齐的场景，堆管理器的额外开销（如块分割、合并）会降低效率。
   - **`mmap` 的优势**：`mmap` 是 Linux 系统调用，直接通过虚拟内存机制向内核申请匿名内存（或文件映射）。它可以**按页（通常 4KB）对齐**分配内存，且支持任意大小的连续虚拟地址空间（受限于系统虚拟内存限制）。对于 `PageCache` 这种需要按页管理内存的场景（如操作系统的页缓存机制），`mmap` 能更精准地匹配“页”这一最小内存管理单元。