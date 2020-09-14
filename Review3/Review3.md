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



### Synchorized实现原理

- 在 Java 中，关键字 synchronized可以保证在***同一个时刻***，只有***一个线程***可以执行某个方法或者某个代码块(主要是对方法或者代码块中存在共享数据的操作)，同时我们还应该注意到synchronized另外一个重要的作用，synchronized可保证一个线程的变化(主要是共享数据的变化)***被其他线程***所看到（保证可见性，完全可以替代Volatile功能），这点确实也是很重要的。

- 三种应用场景
  - 普通同步方法，锁是当前实例对象
  - 静态同步方法，锁是当前类的class对象
  - 同步方法块，锁是括号里面的对象

当一个线程访问同步代码块时，它首先是需要***得到锁***才能执行同步代码，当退出或者抛出异常时必须要***释放锁***。

```java
public class SynchronizedTest {
    public synchronized void test1(){

    }

    public void test2(){
        synchronized (this){

        }
    }
}
```

![这里写图片描述](Review3.assets/20170205213401778)

从上面可以看出，同步代码块是使用***monitorenter***和***monitorexit***指令实现的，同步方法（在这看不出来需要看JVM底层实现）依靠的是方法修饰符上的ACC_SYNCHRONIZED实现。



### HashMap put()方法的返回值

源码

```java

public V put(K paramK, V paramV)
  {
    if (paramK == null) {
      return putForNullKey(paramV);
    }
    int i = hash(paramK.hashCode());
    int j = indexFor(i, this.table.length);
    for (Entry localEntry = this.table[j]; localEntry != null; localEntry = localEntry.next)
    {
      Object localObject1;
      if ((localEntry.hash == i) && (((localObject1 = localEntry.key) == paramK) || (paramK.equals(localObject1))))
      {
        Object localObject2 = localEntry.value;
        localEntry.value = paramV;
        localEntry.recordAccess(this);
        return localObject2;
      }
    }
    this.modCount += 1;
    addEntry(i, paramK, paramV, j);
    return null;
  }
```

说明：put方法返回值为***null***或者***value***；

如果key没有重复, put() 成功了 则返回null 

如果key重复了 返回的是map,.get(key) ---> 旧版key对应的value

```java

public class Test {
	public static void main(String[] args) {
		Map<String, String> map = new HashMap<String, String>();
		String p1 = map.put("11", "22");
		System.out.println("p1:" + p1);
 
		String p2 = map.put("33", "44");
		System.out.println("p2:" + p2);
 
		String value1 = map.get("11");
		System.out.println("value1:" + value1);
 
		String p3 = map.put("11", "44");
		System.out.println("p3：" + p3);
 
		String value2 = map.get("11");
		System.out.println("value2:" + value2);
	}

```

> 1. p1:null
> 2. p2:null
> 3. value1:22
> 4. p3：22
> 5. value2:44



### Java求数组中两个元素差的最大值（动态规划）

题目
求数组中两个元素差的最大值（***后面的***元素减去***前面的***元素）；

对应实际生活中的股票买卖，找出可能的***最大收益***；

思路
类似于求数组连续和的最大值；
保存最大差值和最小值，

遍历数组，

如果当前元素-min>最大差值，则更新最大差值；

如果当前元素<最小值，则更新最小值；

代码实现

```java
public static int max_difference(int[] a){
        int len=a.length;
        if(len<2){
            return 0;
        }
        int min=Math.min(a[0], a[1]);
        int max_diff=a[1]-a[0];
        for(int i=2;i<len;i++){
            if(a[i]-min>max_diff){
                max_diff=a[i]-min;
            }
            if(a[i]<min){
                min=a[i];
            }
        }
        return max_diff;
    }
```





















