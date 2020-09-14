### 项目中使用到多线程的场景:

CPM20 一边做Sample_code进行压力测试 另一边从sys/kernel/debuginfo 中读取 ringinfo和deviceinfo

设置线程的优先级

```java
t1.setPriority(5);           
t2.setPriority(10); 
```

> 不过CPU执行哪个线程的时间和顺序是不确定的，即使设置了线程的优先级
>
> java 中的线程优先级的范围是1～10，默认的优先级是5。10极最高.
>
> 若有时间片轮播机制
>
> “高优先级线程”被分配CPU的***概率***高于“低优先级线程”
>
> 若没有事件片轮转机制
>
> 高级别的线程优先执行













































