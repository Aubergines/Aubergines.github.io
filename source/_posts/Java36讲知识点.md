---
title: Java36讲知识点 
tags: Java,知识点,基础
date: 2018-11-25 20:11:10
grammar_cjkRuby: true
---
* ClassNotFoundException和NoClassDefFoundError的区别
	* ClassNotFoundException的产生原因：就是找不到指定的class,发生在加载阶段。
	* 常见的场景就是：
		* 调用class的forName方法时，找不到指定的类
		* ClassLoader 中的 findSystemClass() 方法时，找不到指定的类
		* ClassLoader 中的 loadClass() 方法时，找不到指定的类
    * NoClassDefFoundError发生在初始化阶段
    * 常见的场景就是：
		* 类依赖的class或者jar不存在
		* 类文件存在，但是存在不同的域中
		* 大小写问题，javac编译的时候是无视大小的，很有可能你编译出来的class文件就与想要的不一样！这个没有做验证。
* JAVA 的语法糖
	* 泛型与类型擦除 
	* 自动装箱与拆箱，变长参数、 
	* 增强for循环 
	* 内部类与枚举类
* Vector、ArrayList、LinkedList的区别
	* Vector 是 Java 早期提供的线程安全的动态数组，如果不需要线程安全，并不建议选择，毕竟同步是有额外开销的。Vector 内部是使用对象数组来保存数据，可以根据需要自动的增加容量，当数组已满时，会创建新的数组，并拷贝原有数组数据。Vector 是矢量队列，它是JDK1.0版本添加的类。继承于AbstractList，实现了List, RandomAccess, Cloneable这些接口。Vector 继承了AbstractList，实现了List；所以，它是一个队列，支持相关的添加、删除、修改、遍历等功能。Vector 实现了RandmoAccess接口，即提供了随机访问功能。RandmoAccess是java中用来被List实现，为List提供快速访问功能的。在Vector中，我们即可以通过元素的序号快速获取元素对象；这就是快速随机访问。Vector 实现了Cloneable接口，即实现clone()函数。它能被克隆。
	* ArrayList 是应用更加广泛的动态数组实现，它本身不是线程安全的，所以性能要好很多。与 Vector 近似，ArrayList 也是可以根据需要调整容量，不过两者的调整逻辑有所区别，Vector 在扩容时会提高 1 倍，而 ArrayList 则是增加 50%。
	* LinkedList 顾名思义是 Java 提供的双向链表，所以它不需要像上面两种那样调整容量，它也不是线程安全的。
* 抽象类、抽象方法、接口的区别及实现
	* 用abstract修饰的类，即抽象类；用abstract修饰的方法，即抽象方法。
	* 抽象方法不能有方法主体。
	* 抽象类不能被实例化。因为抽象类中方法未具体化，这是一种不完整的类，所以直接实例化也就没有意义了。
	* 抽象类中不一定要包含abstrace方法。也就是了，抽象中可以没有abstract方法。
	* 一旦类中包含了abstract方法，那类该类必须声明为abstract类。
* 对比 Hashtable、HashMap、TreeMap
	* Hashtable 是早期 Java 类库提供的一个哈希表实现，本身是同步的，不支持 null 键和值，由于同步导致的性能开销，所以已经很少被推荐使用。
	* HashMap 是应用更加广泛的哈希表实现，行为上大致上与 HashTable 一致，主要区别在于 HashMap 不是同步的，支持 null 键和值等。通常情况下，HashMap 进行 put 或者 get 操作，可以达到常数时间的性能，所以它是绝大部分利用键值对存取场景的首选，比如，实现一个用户 ID 和用户信息对应的运行时存储结构。
	* TreeMap 则是基于红黑树的一种提供顺序访问的 Map，和 HashMap 不同，它的 get、put、remove 之类操作都是 O（log(n)）的时间复杂度，具体顺序可以由指定的 Comparator 来决定，或者根据键的自然顺序来判断。

![Map继承类图][1]

* IO相关类图

![IO相关类][2]

* LinkedHashMap的用法，当自定义删除策略的时候，会保留最近使用的key
``` java
	LinkedHashMap<String, String> accessOrderedMap = new LinkedHashMap<String, String>(16, 0.75F, true) {
	   // 实现自定义删除策略，否则行为就和普遍 Map 没有区别
	   @Override
		protected boolean removeEldestEntry(Map.Entry<String, String> eldest) { 
			return size() > 3;
		}
	};
	accessOrderedMap.put("a", "a");
	accessOrderedMap.put("b", "b");
	accessOrderedMap.put("c", "c");
	accessOrderedMap.forEach((k, v) -> {
		System.out.println("init status:" + k + ":" + v);
	});
	// 模拟访问
	accessOrderedMap.get("c");
	accessOrderedMap.get("a");
	accessOrderedMap.get("b");
	accessOrderedMap.forEach((k, v) -> {
		System.out.println("after get:" + k + ":" + v);
	});
	// 触发删除
	accessOrderedMap.put("d", "d");
	// 遍历顺序不变
	accessOrderedMap.forEach((k, v) -> {
		System.out.println("after remove:" + k + ":" + v);
	});
```
* 使用entrySet遍历Map类集合KV，而不是keySet方式进行遍历。keySet其实是遍历了2次，一次是转为Iterator对象，另一次是从hashMap中取出key所对应的value。而entrySet只是遍历了一次就把key和value都放到了entry中，效率更高。如果是JDK8，使用Map.foreach方法。
* 合理利用好集合的有序性(sort)和稳定性(order)，避免集合的无序性(unsort)和不稳定性(unorder)带来的负面影响。有序性是指遍历的结果是按某种比较规则依次排列的。稳定性指集合每次遍历的元素次序是一定的。如：ArrayList是order/unsort；HashMap是unorder/unsort；TreeSet是order/sort。
* Redis注意点
	* 使用 Redis 有什么缺点
		* 缓存和数据库双写一致性问题
		* 缓存雪崩问题
		* 缓存击穿问题
		* 缓存的并发竞争问题
	* 单线程的 Redis 为什么这么快
		* 纯内存操作
		* 单线程操作，避免了频繁的上下文切换
		* 采用了非阻塞 I/O 多路复用机制
	* Redis 的数据类型，以及每种数据类型的使用场景
		* String:这个没啥好说的，最常规的 set/get 操作，Value 可以是 String 也可以是数字。一般做一些复杂的计数功能的缓存。
		* Hash:这里 Value 存放的是结构化的对象，比较方便的就是操作其中的某个字段。
		* List:使用 List 的数据结构，可以做简单的消息队列的功能。另外还有一个就是，可以利用 lrange 命令，做基于 Redis 的分页功能，性能极佳，用户体验好。
		* Set:因为 Set 堆放的是一堆不重复值的集合。所以可以做全局去重的功能。另外，就是利用交集、并集、差集等操作，可以计算共同喜好，全部的喜好，自己独有的喜好等功能。
		* Sorted Set:Sorted Set多了一个权重参数 Score，集合中的元素能够按 Score 进行排列。可以做排行榜应用，取 TOP N 操作。Sorted Set 可以用来做延时任务。最后一个应用就是可以做范围查找。
	* 如何应对缓存穿透和缓存雪崩问题
		* 缓存穿透解决方案
			* 利用互斥锁，缓存失效的时候，先去获得锁，得到锁了，再去请求数据库。没得到锁，则休眠一段时间重试。
			* 采用异步更新策略，无论 Key 是否取到值，都直接返回。Value 值中维护一个缓存失效时间，缓存如果过期，异步起一个线程去读数据库，更新缓存。需要做缓存预热(项目启动前，先加载缓存)操作。
			* 提供一个能迅速判断请求是否有效的拦截机制，比如，利用布隆过滤器，内部维护一系列合法有效的 Key。迅速判断出，请求所携带的 Key 是否合法有效。如果不合法，则直接返回。
		* 缓存雪崩，即缓存同一时间大面积的失效，这个时候又来了一波请求，结果请求都怼到数据库上，从而导致数据库连接异常。
			* 给缓存的失效时间，加上一个随机值，避免集体失效。
			* 使用互斥锁，但是该方案吞吐量明显下降了。
			* 双缓存。我们有两个缓存，缓存 A 和缓存 B。缓存 A 的失效时间为 20 分钟，缓存 B 不设失效时间。自己做缓存预热操作。
	* 借用慕课网的一个案例，计数器的使用，感觉这是一个很好的面试题

![enter description here][3]

![enter description here][4]




  [1]: http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2018/8/14/java.util.map%E7%B1%BB%E5%9B%BE.png "java.util.map类图"
  [2]: http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2018/8/15/4338e26731db0df390896ab305506d8b.png "IO相关类"
 
  [3]: http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2018/10/30/5bd859d80001a0ce19201080.jpg "有问题的"
  [4]: http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2018/10/30/5bd85ad000019ebc19201080.jpg "严谨的使用"