1 data struct
 （1） bucket

```c
		struct bucket {
		       atomic_t  pin;
			   uint16_t  prio; /*每次hit都会增加，然后所有的bucket的优先级编号都会周期性地减少，不常用的会被回收，这个优先级编号主要是用来实现lru替换的*/
		       uint8_t   gen; //generation，用来invalidate bucket用的。
		       uint8_t  last_gc; /* Most out of date gen in the btree */
		       uint16_t gc_mark; /* Bitfield used by GC. See below for field */
		};
```

​	bucket大小和ssd的擦除大小一致，一般建议128K~2M。bucket内空间是追加的，只记录当前分配到哪个偏移了，下次分配
​	从当前记录位置往后分配。优先考虑io连线性，即使io来自不同生产者；其次考虑相关性，同一进程的数据尽量缓存到相同的bucket。

  （2） bkey
	  bkey记录缓存设备和后端设备对应关系

```c
 struct bkey {	  
		__u64    high;

	    __u64    low;

	    __u64    ptr[];

};
```
![img](http://www.sysnote.org/2014/06/20/bcache-analysis/bkey.png)

