---
title: Redis应用之SortedSet的使用
tags: redis,sortedSet
date: 2018-12-24 10:0:6
categories: redis
grammar_cjkRuby: true
---
##### 前言：相对来说我们是比较深度使用redis的，在推荐瀑布流和排行榜等好多的场景我们主要是用的是`SortedSet`,今天来说一说`SortedSet`

![redis集群监控](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2018/12/2018-12-24_115803.png)

一、基本的用法
1. 插入数据，常用的有`ZADD`和`ZINCRBY`
	* 使用方法`Long zadd(byte[] key, double score, byte[] member)`插入数据，比我们的推荐场景中，根据用户的行为数据，推荐出来的数据会存放到redis中，就是调用这个方法，插入要推荐的数据
	* 使用方法`Double zincrby(byte[] key, double score, byte[] member)`对已有的数据的score的值进行增减，比如我们的搜索词排行，用户输入一个搜索词之后，经历一定的清洗后会把对应的词存入redis中，score记入1,以后每多搜索一次，调用`ZINCRBY`对这个搜索词加1

2. 删除数据，常用的有`ZREM`,`ZREMRANGEBYSCORE`
	* 使用方法`Long zrem(byte[] key, byte[]... member)`删除集合中给定的元素，可以是多个，不存在的成员将被忽略
	* 使用方法`Long zremrangeByScore(byte[] key, double start, double end)`删除集合中给定score区间的元素，其中start可以是'-inf',end可以是‘+inf’

3. 查询数据，常用的有`ZRANGE`,`ZRANGEBYSCORE`,`ZREVRANGE`,`ZREVRANGEBYSCORE`
	* 使用方法`Set<byte[]> zrange(byte[] key, long start, long end)`返回有序集 key 中，指定区间内的成员，其中成员的位置按 score 值递增(从小到大)来排序
	* 使用方法`Set<byte[]> zrevrange(byte[] key, long start, long end)`返回有序集 key 中，指定区间内的成员，其中成员的位置按 score 值递增(从大到小)来排序
	* 使用方法`Set<byte[]> zrangeByScore(byte[] key, double min, double max)`返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。有序集成员按 score 值递增(从小到大)次序排列
	* 使用方法`Set<byte[]> zrevrangeByScore(byte[] key, double min, double max)`返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员。有序集成员按 score 值递增(从大到小)次序排列
	* 使用方法`Set<Tuple> zrangeByScoreWithScores(byte[] key, double min, double max)`返回有序集 key 中，所有 score 值介于 min 和 max 之间(包括等于 min 或 max )的成员及score。有序集成员按 score 值递增(从小到大)次序排列
	
二、注意点
1. `ZINCRBY`可以对当前键值的score进行增减操作，减的操作传入负数的score值即可，如果插入的数据不存在，当前key不存在，score即为传入的score,如果key存在则当前的score加上redis本身存在的score为最终的结果
2. 查询数据，慎用全量的操作，尤其是在不确定当前的键中的结果集合的大小的情况，如果一定要用最好先判断一下当前的键中结果集合的大小，根据大小分批次获取，一般建议一次查询返回的数据量在10KB一下，不过到几百KB也是可以接受的，如果再大的话，需要考虑一下性能

三、问题
1. redis如何保证查询的数据的顺序性？
	通过查看源代码，我们可以知道，redis的返回的结果集使用的是`LinkedHashSet`，保存返回结果集合的
	``` java
	private Set<Tuple> getTupledSet() {
		checkIsInMultiOrPipeline();
		List<String> membersWithScores = client.getMultiBulkReply();
		if (membersWithScores == null) {
		  return Collections.emptySet();
		}
		if (membersWithScores.isEmpty()) {
		  return Collections.emptySet();
		}
		Set<Tuple> set = new LinkedHashSet<Tuple>(membersWithScores.size() / 2, 1.0f);
		Iterator<String> iterator = membersWithScores.iterator();
		while (iterator.hasNext()) {
		  set.add(new Tuple(iterator.next(), Double.valueOf(iterator.next())));
		}
		return set;
	  }
	```