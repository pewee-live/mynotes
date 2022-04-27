# 内存模型
***
## Happens-Before 规则

1. **程序的顺序性规则**:在一个线程中，按照程序顺序，前面的操作 Happens-Before 于后续的任意操作。
2. **volatile 变量规则**: volatile 变量的写操作， Happens-Before 于后续对这个 volatile 变量的读操作
3. **传递性**:如果 A Happens-Before B，且 B Happens-Before C，那么 A Happens-Before C。
  
    如图:  
    ![传递性](concurrent/1.jpg)
    
    在线程B中2若是看到了V=true,那么x=42

4. **管程中锁的规则**：一个锁的解锁 Happens-Before 于后续对这个锁的加锁（管程在java中实现为synchronized ）.意思是一个同步代码块,后进入的线程能够看到前一个线程的修改.
5. **线程 start() 规则**:它是指主线程 A 启动子线程 B 后，子线程 B 能够看到主线程在启动子线程 B 前的操作。
6. **线程 join() 规则**:指主线程 A 等待子线程 B 完成（主线程 A 通过调用子线程 B 的 join() 方法实现），当子线程 B 完成后（主线程 A 中 join() 方法返回），主线程能够看到子线程对共享变量的操作。
7. **final**:``