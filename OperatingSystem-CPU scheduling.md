# CPU 调度
## CPU调度的相关概念
- 按照一定的调度算法从就绪队列中选择一个进程，将CPU的使用权交给被选中的进程
- 如果没有就绪进程，系统会安排一个系统空闲进程或者IDLE进程
- 系统场景
  - n个进程就绪，等待上CPU
  - CPU有m个，m>=1
  - 需要决策，那个进程分配哪一个CPU
- what 调度算法 按什么原则选择下一个要执行的进程
- when 调度时机
- how 如何让备选的进程上CPU运行 就是调度过程
## CPU调度的时机
- 事件发生 ->当前运行的进程暂停运行->硬件机制响应后->进入操作系统，处理响应事件->结束处理后：
- 某些进程的状态会发生变化->需要进程调度根据预设的调度算法从就绪队列选一个进程
- 此时就是**CPU调度的时机**
- 典型的事件举例
  - 创建 唤醒 退出等进程控制操作
  - 进程等待I/O I/O中断
  - 时钟中断，如：时间片用完、计时器到时
  - 执行过程中出现abort异常
- 进程调度的时机总结：
  - 进程正常终止 或 由于某种错误而终止
  - 新进程创建 或 一个等待进程变成就绪
  - 当一个进程从运行态进入阻塞态
  - 当一个进程从运行态变为就绪态
  - 总之：**就是内核对中断/异常/系统调用处理后返回用户态时**
## 调度过程--进程切换
- 进程调度程序从就绪队列选择了要运行的进程：
  - 这个进程可以是刚刚被暂停的进程，也可能是另一个新的进程
- 进程切换的定义：
  - 是指一个进程让出CPU，由另一个进程占用处理器的过程
- 主要包含两部分工作：
  - **切换全局页目录**以加载一个新的地址空间
  - **切换内核站和硬件上下文** 其中硬件上下文包括了内核执行新进程所需要的全部信息，如CPU相关寄存器
  - 切换过程包括了对原来运行进程各种状态的保存和对新的进程各种状态的恢复。
- 上下文切换的具体步骤：
![](https://i.loli.net/2020/02/16/Eho1Px6N2feI8ad.png)
## 上下文切换开销
- 直接开销：内核完成切换所用的CPU时间
  - 保存和恢复寄存器
  - 切换地址空间（相关指令比较昂贵）
- 间接开销：
  - 高速缓存，缓冲区缓存 和TLB失效
## 调度算法衡量指标
- 吞吐量 -每单位时间完成的进程数目
- 周转时间 -每个进程从提出请求到运行完成的时间
- 响应时间
- CPU利用率
- 等待时间
## 进程优先级 （数）
- 两种进程就绪队列
  - 按优先级排队
  - 所有进程进入就绪队列1 如果用完时间片，就降级到下一级队列 CPU优先从就绪队列1
## 抢占与非抢占
- 抢占是优先的先上
- 不可抢占是抢不了
## I/O密集型 CPU密集型
- io密集型 频繁的进行io，通常会花费很多时间等待i/o
- CPU密集型 需要大量的CPU时间进行计算
## 时间片
- 一个时间段 分配给调度上CPU的进程
## 批处理系统中采用的调度算法
- 先来先服务
  - 公平
  - 实现简单
  - 部分短进程可能因为前面的进程太长等待时间太久
  ![](https://i.loli.net/2020/02/16/E8f9pFVuQTCGPwj.png)
- 最短作业优先
  - 最短完成时间的进程优先执行
- 最短剩余时间优先
  - 最短作业优先的抢占式版本，当一个新就绪的进程比当前进程更短完成时间时，系统抢占当前进程，选择新就绪的进程执行
  - 好处 最短的平均周转时间
  - 坏处 长任务频繁得不到执行
- 最高响应比优先
  - 是一个综合算法
  - 调度时先计算每个进程响应比R，之后，总是选择R最高的进程执行
  - 响应比R = 周转时间/处理时间= （处理时间+等待时间）/处理时间 = 1+（等待时间/处理时间）
  - 因而两种情况排的会比较靠前 **要么等待时间大，要么处理时间小**
