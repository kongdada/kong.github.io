---
title: 便利蜂技术面总结
date: 2018-04-01 19:38:46
tags: Interview
categories: Interview
---
3.30号，下午请假去北邮参加校园宣讲会，现场笔试，直接三道编程题；
<!--more-->
#### 笔试
##### 第一道：给出一个正整数数组，返回他能组成的最大整数。
举个例子arr = {19, 9, 912} 他应该组成的最大数为9 912 19；
就是说数组中每个元素都不可以分割开。
思路：首先将整数数组转换为一个字符串数组，每个元素都是一个字符串；
找到元素中长度最长的那个，在例子中就是长度为3的 "912"；
将其他的元素用他本身的最后一个字符补充至长度3，得到 "912", "999", "199";
现在将元素排序得到 "999","912","199";
最后将补充的字符都删除，即可得到：9 912 199；
代码很杂乱，不献丑了；
方法二：
用到了贪心策略，局部最优，推广到整体。引进一个比较器，自定义比较方式；
例如，在自定义之前是按字典序排序，该字符串数组排序结果为："19" "9" "912"，举例子，显然我们需要的不是"19" < "9"，我们需要的是链接后“199”<"919"的顺序,需要的是"9129" < "9912",类似于这种，就是说我们需要的是链接后的顺序，不要链接前的顺序。贪心策略的证明很难，暂时不会，代码暂时没有问题；
代码：
```
package bianlifeng;

import java.util.Arrays;
import java.util.Comparator;

public class improveSolution1 {
	public static class MyComparator implements Comparator<String>{
		@Override
		public int compare(String str1, String str2) {
			return (str1+str2).compareTo(str2+str1);
		}
	}
	public static void main(String[] args) {
		int[] arr = {18,89,12,56,4,3,7};
		String[] strArr = new String[arr.length];
		for(int i=0; i < arr.length;i++) {
			strArr[i] = arr[i] + "";
		}
		Arrays.sort(strArr);
		System.out.println("不用比较器：" + Arrays.toString(strArr));
		Arrays.sort(strArr, new MyComparator());
		System.out.println("用比较器： " + Arrays.toString(strArr));
		for(int i = strArr.length-1 ; i >= 0; i--) {
			System.out.print(strArr[i]);
		}
	}
}
```
##### 第二道：给出一个数组arr，长度为n;每一个元素代表存在的货币值，现在给出一个aim，求最少用几张货币能够组成这个整数aim，每张货币不限使用次数。
- 经典的动态规划问题，构建dp矩阵，n行aim+1列；
- 第一列：aim = 0,自然第一列都是0；
- 第一行，自然只有k倍的arr[0]的地方有对应的次数，举个例子，假设arr[0] = 2;
那么aim 只会等于2，4，6，8,这个样子，也就是在aim等于2的倍数的地方分别初始化为1，2，3，4；
- 其他位置dp[i][j] = min(dp[i-1][j], dp[i][j-arr[i]] + 1);
- 要注意的几个点，其实初始化0的地方就可以不初始化了，本来就是0；
初始化第一行包括后面的dp[i][j],都应该先初始化为max然后满足条件的地方初始化为对应的值，因为后续处理中有个比较要取小值，会有影响；
 - 代码：
 ```
package bianlifeng;
public class impoveSolution2 {

	public static void main(String[] args) {
		int[] arr = { 1, 7, 8, 9, 20, 50 };
		int aim = 15;
		int res = minCoins(arr, aim);
		System.out.println(res);
	}

	private static int minCoins(int[] arr, int aim) {
		int n = arr.length;
		int[][] dp = new int[n][aim + 1];
		int max = Integer.MAX_VALUE;
		// 初始化第一列
		for (int i = 0; i < n; i++) {
			dp[i][0] = 0;
		}
		// 初始化第一行
		for (int j = 1; j <= aim; j++) {
			dp[0][j] = max;
			if (j >= arr[0] && dp[0][j - arr[0]] != max) {
				dp[0][j] = dp[0][j - arr[0]] + 1;
			}
		}
		// 计算其他位置
		int left = 0;
		for (int i = 1; i < n; i++) {
			for (int j = 1; j <= aim; j++) {
				left = max;
				if (j >= arr[i] && dp[i][j - arr[i]] != max) {
					left = dp[i][j - arr[i]] + 1;
				}
				dp[i][j] = Math.min(left, dp[i - 1][j]);
			}
		}
		return dp[n - 1][aim];
	}
}```
##### 第三道：LRU最近最常使用；
详细：因为内存有限，所以维持一个使用频率最高的数字集。
例如有序列4 2 3 4 1 2 3
假设维持的物理块为3则有。
- 4进，维持数组 4
- 2进，维持2 4
- 3进，维持3 2 4
- 4进，维持4 3 2
- 1进，维持1 4 3
- 2进，维持2 1 4
- 3进，维持，3 2 1
- 现在需要你构造一个数据结构满足以上条件。
- 要有 get(key)若key存在则返回对应值。若不存在返回null
- 要有set（key, value），若key不存在，则直接保存，若key存在，更新value。不能使用语言中已经存在的实现类。

#### 面试
第一部分是java的面试官；
1. HashMap的底层实现。
   HashMap内部维护了一个存储数据的Entry数组，HashMap采用链表解决冲突，每一个Entry本质上是一个单向链表。当准备添加一个key-value对时，首先通过hash(key)方法计算hash值，然后通过indexFor(hash,length)求该key-value对的存储位置，计算方法是先用hash&0x7FFFFFFF后，再对length取模，这就保证每一个key-value对都能存入HashMap中，当计算出的位置相同时，由于存入位置是一个链表，则把这个key-value对插入链表头。
2. 假设我自己定义HashMap，只用数组存储，当冲突的时候向后放，这样实现的根本性的缺陷是什么？
3. HashMap的扩展过程；
- HashMap内存储数据的Entry数组默认是16，如果没有对Entry扩容机制的话，当存储的数据一多，Entry内部的链表会很长，这就失去了HashMap的存储意义了。所以HasnMap内部有自己的扩容机制。HashMap内部有：
- 变量size，它记录HashMap的底层数组中已用槽的数量；
- 变量threshold，它是HashMap的阈值，用于判断是否需要调整HashMap的容量（threshold = 容量*加载因子）    
- 变量DEFAULT_LOAD_FACTOR = 0.75f，默认加载因子为0.75
- HashMap扩容的条件是：当size大于threshold时，对HashMap进行扩容  
- 扩容是是新建了一个HashMap的底层数组，而后调用transfer方法，将就HashMap的全部元素添加到新的HashMap中（要重新计算元素在新的数组中的索引位置）。 很明显，扩容是一个相当耗时的操作，因为它需要重新计算这些元素在新的数组中的位置并进行复制处理。因此，我们在用HashMap的时，最好能提前预估下HashMap中元素的个数，这样有助于提高HashMap的性能。
- HashMap共有四个构造方法。构造方法中提到了两个很重要的参数：初始容量和加载因子。这两个参数是影响HashMap性能的重要参数，其中容量表示哈希表中槽的数量（即哈希数组的长度），初始容量是创建哈希表时的容量（从构造函数中可以看出，如果不指明，则默认为16），加载因子是哈希表在其容量自动增加之前可以达到多满的一种尺度，当哈希表中的条目数超出了加载因子与当前容量的乘积时，则要对该哈希表进行 resize 操作（即扩容）。
- 下面说下加载因子，如果加载因子越大，对空间的利用更充分，但是查找效率会降低（链表长度会越来越长）；如果加载因子太小，那么表中的数据将过于稀疏（很多空间还没用，就开始扩容了），对空间造成严重浪费。如果我们在构造方法中不指定，则系统默认加载因子为0.75，这是一个比较理想的值，一般情况下我们是无需修改的。
- 另外，无论我们指定的容量为多少，构造方法都会将实际容量设为不小于指定容量的2的次方的一个数，且最大值不能超过2的30次方
