---
title: 几个小问题的解答
date: 2017-06-22 23:25:38
tags: java
categories: 开发笔记
copyright: true
mathjax: true
---


> 她给我提了一下几个问题，自己一眼看上去觉得简单，但是说不出来所以然，犯了眼高手低的毛病，记录自省。

- [x] 一共10级楼梯，每次可以走一步或两步，求一共多少种走法。
- [x] 一个细胞，一个小时分裂一次，生命周期是3小时，求n小时后容器内，有多少细胞。
- [x] 斐波那契数列的实现。
- [x] 递归算法的核心



# Question1—走楼梯

## 思路

楼梯问题，一眼看上去，应该是一个递归问题跑不了了。

那么该如何递归，找到其中的规律呢？

***逆向思考。***

要想走到M(M=10)级,可以分为2种情况。 

1. 从m-2级迈两步 
2. 从m-1级迈一步 

那么对于m-2和m-1的情况也是各自分为两种，以此类推。

那么走法的和就是m-2的走法和m-1的走法之和。

那么递归到最基本的（当前人在第0阶台阶）

第0阶台阶：0

第1阶台阶：1

第2阶台阶：2（1+1或者2）

得到公式，也就是斐波那契数列。
$$
f(n)=f(n-1)+f(n-2)
$$

## 代码

```java
import java.util.Scanner;

/**
 * Created by ml on 2017/6/21.
 */
public class taijie {
    public static int countNumber(int stepsNum) {
        int sum = 0;
        if (stepsNum == 0) {
            return 0;
        }
        if (stepsNum == 1) {
            return 1;
        } else if (stepsNum == 2) {
            return 2;
        } else if (stepsNum > 2) {
            return  countNumber(stepsNum - 2) + countNumber(stepsNum - 1);
        }
        return sum;
    }
    public static void main(String[] args) {
        long startMili=System.currentTimeMillis();
        for (int i = 0; i <= 10; i++) {
            System.out.println("楼梯台阶数:" + i + ", 走法有:" + countNumber(i));
        }
        long endMili=System.currentTimeMillis();
        System.out.println("耗时:" + String.valueOf(endMili - startMili) + "毫秒");
    }
}

```

```
楼梯台阶数:0, 走法有:0
楼梯台阶数:1, 走法有:1
楼梯台阶数:2, 走法有:2
楼梯台阶数:3, 走法有:3
楼梯台阶数:4, 走法有:5
楼梯台阶数:5, 走法有:8
楼梯台阶数:6, 走法有:13
楼梯台阶数:7, 走法有:21
楼梯台阶数:8, 走法有:34
楼梯台阶数:9, 走法有:55
楼梯台阶数:10, 走法有:89
耗时:0毫秒

Process finished with exit code 0

```

## 其他的方法

该算法的运算时间是指数级增长的，还有其他方法吗？

动态规划？

其实动态规划（dynamicprogramming）是通过组合子问题而解决整个问题的解。乍一看和递归的写法差不多，都是相加。但是递归式是包含了许多重复计算的步骤，对应台阶就是每一个台阶计算前面都是重复的。动态规划[算法](http://lib.csdn.net/base/datastructure)对每个子问题只求解一次，将其结果保存起来，从而避免每次遇到各个子问题时重新计算答案。

代码如下：

```java
import java.util.Scanner;

/**
 * Created by ml on 2017/6/21.
 */
public class taijie {
    public static int climbStairs(int n) {
        if (n == 0 || n == 1 || n == 2) {
            return n;
        }
        //注意坐标起始点的区别
        int[] r = new int[n+1];
        r[1] = 1;
        r[2] = 2;
        for (int i = 3; i <= n; i++) {
            r[i] = r[i-1] + r[i-2];
        }
        return r[n];
    }

    public static void main(String[] args) {
        long startMili=System.currentTimeMillis();
        for (int i = 0; i <= 10; i++) {
            System.out.println("楼梯台阶数:" + i + ", 走法有:" + climbStairs(i));
        }
        long endMili=System.currentTimeMillis();
        System.out.println("耗时:" + String.valueOf(endMili - startMili) + "毫秒");
    }
}

```

```
楼梯台阶数:0, 走法有:0
楼梯台阶数:1, 走法有:1
楼梯台阶数:2, 走法有:2
楼梯台阶数:3, 走法有:3
楼梯台阶数:4, 走法有:5
楼梯台阶数:5, 走法有:8
楼梯台阶数:6, 走法有:13
楼梯台阶数:7, 走法有:21
楼梯台阶数:8, 走法有:34
楼梯台阶数:9, 走法有:55
楼梯台阶数:10, 走法有:89
耗时:0毫秒

Process finished with exit code 0

```



之前数学课本上如何思考的？

1. 先求出走完台阶需要几步？
2. 再求出总步数中，走一个台阶是几步，走两个台阶式几步，不同种类为排列组合计算

## 类似问题

再看一个硬币的凑法：

> 用1分、2分和5分的硬币凑成1元，共有多少种不同的凑法？（华为机试题）

它和走楼梯的区别在于，楼梯不同顺序是不同的走法，硬币则与顺序无关，程序可用穷举法。

```java
public class coin {
	
	public static int getKinds(){
		int num=0;
		for (int i = 0; i < 21; i++) {
			for (int j = 0; j <= 100-5*i; j++) {
				for (int k = 0; k <= 100-5*i-2*j; k++) {
					if(5*i+2*j+k==100){
						num++;
					}
				}
			}
		}
		return num;
	}

	public static void main(String[] args) {
		System.out.println(getKinds());
	}

}
```

那么这个问题可以推广为一般性问题吗？M阶，一次走a阶或者b阶或者。。。，求不同走法。

## 参考文章

[走楼梯问题]: http://blog.sina.com.cn/s/blog_77f7b0c80102we9e.html
[动态规划-爬楼梯问题]: http://www.cnblogs.com/maowuyu-xb/p/6166106.html

# Question2—细胞分裂

## 非递归思路

乍一看，关键是如何处理生命周期是3小时。如何在分裂的同时提出死亡的细胞。

### 代码：

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

/**
 * Created by ml on 2017/6/22.
 * 思路：每个细胞定义为一维数组的元素，元素的值为细胞的生存时间，每分裂一次，在数组末尾添加新的元素，初始值为0，同时原来的元素值加1
 * 当元素的值为3时，将该元素剔除
 * 最后返回该数组的大小
 */

public class xibao {
    public static int cellNum2(int n) {
        ArrayList<Integer> cell = new ArrayList<Integer>();
        //定义cellnum
        int cellnum = 0;
        //加入初始细胞
        cell.add(0);
        for (int i = 0; i < n; i++) {
            //记录当前数组大小（剔除完成后的数组）
            int size = cell.size();
            //计算分裂后新的数组的大小
            cellnum = 2 * cell.size();
            //原有生存时间加1
            for (int j = 0; j < cell.size(); j++) {
                cell.set(j, cell.get(j) + 1);
            }
            //遍历剔除元素值为3 PS：ArrayList中遍历可以删除的只有迭代器
            Iterator<Integer> it = cell.iterator();
            while (it.hasNext()) {
                if (it.next().equals(3))
                    it.remove();
            }
            //在数组末尾添加新的元素
            for (int j = size; j < cellnum; j++) {
                cell.add(0);
            }
        }
        return cell.size();
    }

    public static int cellNum(int n) {


        HashMap<Integer, Integer> cell = new HashMap<>();
        int cellnum = 1;
        for (int i = 1; i <= n; i++) {
            cellnum *= 2;
            int size = cell.size();
            for (int j = size; j < cellnum; j++) {
                cell.put(j, 0);
            }
            Iterator<HashMap.Entry<Integer, Integer>> iter = cell.entrySet().iterator();
            while (iter.hasNext()) {
                Map.Entry entry = iter.next();
                int key = (int) entry.getKey();
                int val = (int) entry.getValue();
                if (key < size) {
                    cell.put(key, val + 1);
                }
            }
        }
        return cell.size();
    }

    public static void main(String[] args) {
        long startMili = System.currentTimeMillis();
        int n = 6;
        System.out.println(n + "小时后容器细胞:" + cellNum2(n) + "个");
        long endMili = System.currentTimeMillis();
        System.out.println("耗时:" + String.valueOf(endMili - startMili) + "毫秒");
    }


}

```

```
3小时后容器细胞:7个
耗时:0毫秒

Process finished with exit code 0
```

### 关于ArrayList

1. 迭代器是作为当前集合的内部类实现的，当迭代器创建的时候保持了当前集合的引用；
2. 集合内部维护一个字段叫modiCount，用来记录集合被修改的次数，比如add，remove，set等都会使该字段递增；
3. 迭代器内部也维护着当前集合的修改次数的字段，迭代器创建时该字段初始化为集合的modiCount值
4. 当每一次迭代时，迭代器会比较迭代器维护的字段和modiCount的值是否相等，如果不相等就抛ConcurrentModifiedException异常；
5. 当然，如果用迭代器调用remove方法，那么集合和迭代器维护的修改字数都会递增，以保持两个状态的一致。

这就是为什么你只可以用迭代器来删除，而不能用其他方式来修改集合。



## 这类问题如何递归？

公式法：
$$
n=t/a~~~~~~~~~~~~y=2^n
$$
但是这个只适用于最简单的没有任何附加状态条件的情况。

递归如何来分析？

细胞的生存周期是3个小时，那我们就可以把细胞在题目中状态分为以下几个状态：

- a：刚分裂态——1
- b：分裂1小时态——a分裂出b和a
- c：分裂2小时态——b分裂出c和a
- d：分裂3小时态——死亡（停止分裂）

那么，我们就可以根据细胞状态设定函数。分析每一个状态的来源是哪里即可。
$$
a(t)=a(t-1)+b(t-1)+c(t-1)\\b(t)=a(t-1)\\c(t)=b(t-1)\\d(t)=d(t-1)+c(t-1)
$$
容器中存活的细胞数目就是a、b、c三种状态数量的总和。

### 代码

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

/**
 * Created by ml on 2017/6/22.
 */

public class xibao {
    public static int aStatus(int t) {
        if (t == 0) {
            return 1;
        }
        return aStatus(t - 1) + bStatus(t - 1) + cStatus(t - 1);
    }

    public static int bStatus(int t) {
        if (t == 0) {
            return 0;
        }
        return aStatus(t-1);
    }

    public static int cStatus(int t) {
        if (t == 0) {
            return 0;
        }
        if (t == 1) {
            return 0;
        }
        return bStatus(t-1);
    }

    public static int dStatus(int t) {
        if (t == 0) {
            return 0;
        }
        if (t == 1) {
            return 0;
        }
        if (t == 2) {
            return 0;
        }
        return dStatus(t-1) + cStatus(t-1);
    }

    public static void main(String[] args) {
        long startMili = System.currentTimeMillis();
        int n = 3;
        int res = aStatus(n) + bStatus(n) + cStatus(n);
        System.out.println(n + "小时后容器细胞:" + res + "个");
        long endMili = System.currentTimeMillis();
        System.out.println("耗时:" + String.valueOf(endMili - startMili) + "毫秒");
    }
}

```

```
3小时后容器细胞:7个
耗时:0毫秒

Process finished with exit code 0
```



## 参考文章

[递归求解细胞分裂问题]: http://blog.csdn.net/u013269810/article/details/50437866



# Question3—Fibonacci数列

经典问题：求Fibonacci数列前n项。
$$
{an}：a1=1，a2=1，a_{n+2}=a_{n+1}+a_n（n≥1）。
$$


PS: Markdown 中公式书写规则

- `\\`符号后接的字符为上标
- `^`符号后接的字符为上标 
- `_`符号后接的字符为下标 
- 如果同时有两个下标，则需要使用`{}`来将符号括起来



## 代码

```java
public class fab {
    public static void main(String[] args) {
        for(int i = 1; i < 10; i ++){
            System.out.print(recursion(i) + " ");
        }
        System.out.println();
        for(int i = 1; i < 10; i ++){
            System.out.print(loop(i) + " ");
        }
    }
    /**
     * 递归
     */
    public static int recursion(int n){
        if(n <= 2){
            return 1;
        }
        return recursion(n -1) + recursion(n -2);
    }
    /**
     * 循环
     */
    public static int loop(int n){
        if(n <= 2){
            return 1;
        }
        int s1 = 1,s2 = 1, sum = 0;
        for(int i = 0 ; i < n - 2; i ++){
            sum = s1 + s2;
            s1 = s2;
            s2 = sum;
        }
        return sum;
    }
}
```

```
1 1 2 3 5 8 13 21 34 
1 1 2 3 5 8 13 21 34 
Process finished with exit code 0
```

值得注意的是（递归方法有栈溢出的风险）。



## 参考文章

[Java实现斐波那契数列]: http://blog.csdn.net/bbirdsky/article/details/8964560



# Question4—递归核心

那么总结一下，递归算法的核心是什么呢？

那就是：

 - ***<u>在有限次可预见性结果中，找到结果与上一次结果之间的关系。</u>***
 - f(n)与f(n-1)的关系有时候很简单，如同走楼梯，状态单一；又有时如同细胞分裂，多种状态组合影响结果。
 - 关键在于梳理清楚本次结果和上一次结果的关系有哪些方面或是因素，刚开始看的时候可能会很乱。
 - 在草稿纸上写出前几次的结果，或者画图，这样更容易找到规律，这种规律实际上就是递归方程。
 - ***在算法的分析中，当一个算法中包含递归调用时，其时间复杂度的分析会转化成为一个递归方程的求解。而对递归方程的求解，方法多种多样，不一而足。**

动态规划就是在递归的基础上，保存每一步的数据，避免重复计算。
在递归计算调用次数过多时，可以考虑更换其他方法解答。

