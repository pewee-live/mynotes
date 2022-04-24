# 内存模型
***
## Happens-Before 规则

1. **程序的顺序性规则**:在一个线程中，按照程序顺序，前面的操作 Happens-Before 于后续的任意操作。
2. **volatile 变量规则**: volatile 变量的写操作， Happens-Before 于后续对这个 volatile 变量的读操作
3. **传递性**:如果 A Happens-Before B，且 B Happens-Before C，那么 A Happens-Before C。
  
    如图:  
    ![传递性](concurrent/1.jpg)
    
    在线程B中2若是看到了V=true,那么x=42

4. **管程中锁的规则**：一个锁的解锁 Happens-Before 于后续对这个锁的加锁（管程在java中实现为synchronized ）