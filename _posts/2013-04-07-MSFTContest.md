---
layout: post
title: 几道编程题
categories:
- Algorithm
---
##传话游戏
####时间限制: 1000ms 内存限制: 256MB

####描述
Alice和Bob还有其他几位好朋友在一起玩传话游戏。这个游戏是这样进行的：首先，所有游戏者按顺序站成一排，Alice站第一位，Bob站最后一位。然后，Alice想一句话悄悄告诉第二位游戏者，第二位游戏者又悄悄地告诉第三位，第三位又告诉第四位……以此类推，直到倒数第二位告诉Bob。两位游戏者在传话中，不能让其他人听到，也不能使用肢体动作来解释。最后，Bob把他所听到的话告诉大家，Alice也把她原本所想的话告诉大家。
由于传话过程中可能出现一些偏差，游戏者越多，Bob最后听到的话就与Alice所想的越不同。Bob听到的话往往会变成一些很搞笑的东西，所以大家玩得乐此不疲。经过几轮游戏后，Alice注意到在两人传话中，有些词汇往往会错误地变成其他特定的词汇。Alice已经收集到了这样的一个词汇转化的列表，她想知道她的话传到Bob时会变成什么样子，请你写个程序来帮助她。

####输入
输入包括多组数据。第一行是整数 T，表示有多少组测试数据。每组数据第一行包括两个整数 N 和 M，分别表示游戏者的数量和单词转化列表长度。随后有 M 行，每行包含两个用空格隔开的单词 a 和 b，表示单词 a 在传话中一定会变成 b。输入数据保证没有重复的 a。最后一行包含若干个用单个空格隔开的单词，表示Alice所想的句子，句子总长不超过100个字符。所有单词都只包含小写字母，并且长度不超过20，同一个单词的不同时态被认为是不同的单词。你可以假定不在列表中的单词永远不会变化。

####输出
对于每组测试数据，单独输出一行“Case #c: s”。其中，c 为测试数据编号，s 为Bob所听到的句子。s 的格式与输入数据中Alice所想的句子格式相同。
数据范围:1 ≤ T ≤ 100
小数据：2 ≤ N ≤ 10, 0 ≤ M ≤ 10
大数据：2 ≤ N ≤ 100, 0 ≤ M ≤ 100

####样例输入
{% highlight java %}
2  
4 3  
ship sheep  
sinking thinking  
thinking sinking  
the ship is sinking  
10 5  
tidy tiny  
tiger liar  
tired tire  
tire bear  
liar bear  
a tidy tiger is tired 
{% endhighlight %}

####样例输出
{% highlight java %}
Case #1: the sheep is thinking
Case #2: a tiny bear is bear
{% endhighlight %}

####源代码
{% highlight java %}
import java.util.Scanner;
import java.util.Map;
import java.util.HashMap;
import java.util.Set;
import java.util.Iterator;
import java.util.List;
import java.util.ArrayList;

/*
 * Author: tsui
 * Date: 2013-04-08
 */
public class Whisper {
     public static void main(String[] args)
     {
          Scanner sin = new Scanner(System.in);
          //get group number
          String s_group = sin.nextLine();
          int group = new Integer(s_group);
          // p1:people number
          // p2:word pair number
          int[] p1 = new int[group];
          int[] p2 = new int[group];

          // os:original sentence
          // ts:transformed sentence
          String[] os = new String[group];
          String[] ts = new String[group];
          // wp:word pair
          List<Map<String, String>> wpl = new ArrayList<Map<String, String>>();
          int tmp = 0;
          while (tmp < group)
          {
               Map<String, String> wp = new HashMap<String, String>();
               wpl.add(wp);
               tmp++;
          }

          //get input
          for (int gi = 0; gi < group; gi++)
          {
               String s_params = sin.nextLine();
               String[] params = s_params.split(" ");
               p1[gi] = new Integer(params[0]);
               p2[gi] = new Integer(params[1]);

               int i = 0;
               while (i < p2[gi])
               {
                    String wpline = new String();
                    wpline = sin.nextLine();
                    String[] wpline_ = wpline.split(" ");
                    wpl.get(gi).put(wpline_[0], wpline_[1]);
                    i++;
               }
               os[gi] = sin.nextLine();
          }// end of for get input

          //transform and output
          for (int gi = 0; gi < group; gi++)
          {
               int pi = 0;// people index
               int si = 0;// sentence word index

               String[] bef = os[gi].split(" ");
               int t_size = bef.length;
               String[] aft = new String[t_size];

               while (pi < p1[gi] - 1)
               {
                    si = 0;
                    while (si < t_size)
                    {
                         Set<String> key = wpl.get(gi).keySet();
                         boolean flag = false;
                         for (Iterator<String> it = key.iterator(); it.hasNext();)
                         {
                              String s = (String) it.next();
                              if (bef[si].equals(s))
                              {
                                   aft[si] = wpl.get(gi).get(s);
                                   flag = true;
                                   break;
                              }// end of if
                         }// end of for
                         if (!flag)
                         {
                              aft[si] = bef[si];
                         }
                         si++;
                    }
                    pi++;
                    bef = aft;
               }
               System.out.print("Case #" + (gi + 1) + ":");
               si = 0;
               while (si < t_size) {
                    if (si != (t_size - 1))
                    {
                         System.out.print(" " + aft[si]);
                         si++;
                    }
                    else
                    {
                         System.out.println(" " + aft[si]);
                         si++;
                    }
               }
          }//end of for transform and output
          sin.close();
     }// end of main
}// end of class
{% endhighlight %}

##相似字符串
####时间限制: 4000ms 内存限制: 256MB

####描述
对于两个长度相等的字符串，我们定义其距离为对应位置不同的字符数量，同时我们认为距离越近的字符串越相似。例如，“0123”和“0000”的距离为 3，“0123”和“0213”的距离则为 2，所以与“0000”相比，“0213”和“0123”最相似。
现在给定两个字符串 S1 和 S2，其中 S2 的长度不大于 S1。请在 S1 中寻找一个与 S2 长度相同的子串，使得距离最小。

####输入
输入包括多组数据。第一行是整数 T，表示有多少组测试数据。每组测试数据恰好占两行，第一行为字符串 S1，第二行为 S2。所有字符串都只包括“0”到“9”的字符。

####输出
对于每组测试数据，单独输出一行“Case #c: d”。其中，c 表示测试数据的编号（从 1 开始），d 表示找到的子串的最小距离。

####数据范围
1 ≤ T ≤ 100
小数据：字符串长度不超过 1000
大数据：字符串长度不超过 50000

####样例输入
{% highlight java %}
3
0123456789
321
010203040506070809
404
20121221
211
{% endhighlight %}

####样例输出
{% highlight java %}
Case #1: 2
Case #2: 1
Case #3: 1
{% endhighlight %}

####源代码
{% highlight java %}
package com.openlab.hellomaven.helloworld;

//source here
import java.util.Scanner;

public class Similar {
     public static void insertSort(int[] elements) {
          for (int i = 1; i < elements.length; i++) {
               int j = -1;
               while (j < i && elements[i] > elements[++j])
                    ;
               if (j < i) {
                    int temp = elements[i];
                    for (int k = i - 1; k >= j; k--) {
                         elements[k + 1] = elements[k];
                    }
                    elements[j] = temp;
               }
          }

          System.out.println(elements[0]);
     }

     public static int distanceCal(String sub_s1, String s2) {

          char[] s1c = stochar(sub_s1);
          char[] s2c = stochar(s2);
          int size = s2.length();
          int i = 0;
          int dis = size;
          while (i < size) {
               if (s1c[i] == s2c[i]) {
                    dis--;
               }
               i++;
          }
          return dis;
     }

     public static char[] stochar(String s) {

          char[] c1 = new char[s.length()];
          s.getChars(0, s.length(), c1, 0);
          return c1;

     }

     public static void main(String[] args) {

          Scanner sin = new Scanner(System.in);
          String s_group = sin.nextLine();
          int group = new Integer(s_group);

          int gi = 0;

          String[] s1 = new String[group];
          String[] s2 = new String[group];

          while (gi < group) {
               s1[gi] = sin.nextLine();
               s2[gi] = sin.nextLine();
               gi++;
          }

          gi = 0;

          while (gi < group) {
               int s1i = 0;
               int s1len = s1[gi].length();
               int s2len = s2[gi].length();
               int dis_num = s1len - s2len + 1;
               int[] distance = new int[dis_num];
               while (s1i < dis_num) {
                    distance[s1i] = distanceCal(s1[gi].substring(s1i, s1i + s2len),
                              s2[gi]);
                    s1i++;
               }

               System.out.print("Case #" + (gi + 1) + ": ");
               insertSort(distance);
               gi++;
          }
     }
}
{% endhighlight %}

##华为竞赛
###题目1
华为商场为了促销终端产品，决定在每月最后一个周六上午9:00开始进行秒杀活动，请你根据指定的年份和月份，计算出秒杀活动的具体日期。

####输入
输入年份和月份，中间使用空格隔开，如2013 04

####输出
该月最后一个周六对应的日期，如输入为2013 04，输出27，输入2014 02，输出22

####样例输入
 `2010 01`

####样例输出：
`30`

####源代码
{% highlight java %}
package com.huawei.contest;
import java.util.Scanner;
public class Main3 {
     static int CalculateWeekDay(int y, int m, int d)
     {
          if (m == 1 || m == 2) {
               m += 12;
               y--;
          }
          int weekDay = (d + 2 * m + 3 * (m + 1) / 5 + y + y / 4 - y / 100 + y / 400) % 7;
          return weekDay;
     }

     static int doFindLast6(int year, int mon, int daynum)
     {
          for(int i=1; i<=daynum; ++i)
          {
               if(CalculateWeekDay(year,mon,i) == 5)
               {
                    if(i+7>daynum)
                         return i;
               }
          }
          return -1;
     }
     public static void main(String[] args) {
          Scanner sc = new Scanner(System.in);
          int year = sc.nextInt();
          int mon = sc.nextInt();
          int[] days={0,31,28,31,30,31,30,31,31,30,31,30,31};
          int[] days1={0,31,29,31,30,31,30,31,31,30,31,30,31};
          int res=0;
          if(year%4 == 0 && year%100 != 0)
               res = doFindLast6(year,mon,days1[mon]);
          else if(year%400 == 0)
               res = doFindLast6(year,mon,days1[mon]);
          else
               res = doFindLast6(year,mon,days[mon]);
         
          if(res != -1)
               System.out.println(res);
     }
}
{% endhighlight %}

###题目2
在某系统中只存在1,2,4,8,16,32,64,128,256,512 KB这10种不同容量的内存块。每两个地址连续且容量一致的内存块可以合并为一个更大的
内存块，以减少内存碎片。比如两个4 KB的内存块可以合并为一个8 KB的内存块。  
合并规则如下：  
1. 每个内存块只可和前一个内存块（即地址连续且地址值比它小的内存块）合并。合并后的内存块若满足规则可继续和前一个内存块合并。
2. 两个容量相同的内存块才可以合并。512KB的内存块不可再合并。有一个数组，按照内存起始地址连续递增的方式记录了若干不同容量的内存块。需按照上述原则对内存进行整理，并把合并后的信息连续保存到输出数组中。
3. 输入的内存块数<=30，不需要考虑输入数量异常。

####输入 
内存块数量和不同容量的内存块列表。输入为若干个整数，采用空格隔开。
输入中第一个整数为内存块数量，后面的整数为内存块列表。
内存块间地址连续，且为连续递增方式保存。相同容量的内存块可重复出现。
例：
> 8 2 2 2 1 8 8 16 512 
表示输入有8个连续的内存块，内存块的容量分别为
> 2KB 2KB 2KB 1KB 8KB 8KB 16KB 512KB

####输出  
合并后的内存块列表。各元素之间用一个空格间隔。若有内存块合并，则后面的元素位置前移，否则输出元素位置要和输入中保持一致。

####样例输入
`8 2 2 2 1 8 8 16 512`

####样例输出
`4 2 1 32 512`

####源代码
{% highlight java %}
package com.huawei.contest;
import java.util.Scanner;
public class Main2 {
     public static final int MAX=30;
     public static void main(String[] args) throws Exception
     {
          Scanner sc = new Scanner(System.in);
          int num = sc.nextInt();
          if(num > MAX)
          {
               num=MAX;
               throw new Exception();
          }
          int[] a = new int[num];
          for(int i=0; i<num; i++)
          {
               a[i] = sc.nextInt();
          }
          for(int i=0; i<num-1; i++)
          {
               if((a[i] == a[i+1]) && a[i] != 512)
               {
                    a[i+1] += a[i];
                    a[i] = 0;
               }
          }
          String t = "";
          for(int i=0; i<num; i++)
          {
               if(a[i] != 0)
               {
                    if(i<num-1)
                         t+=a[i]+" ";
                    else
                         t+=a[i];
               }
          }
          System.out.print(t);
     }
}
{% endhighlight %}

###题目3
公司计划购买一批华为Ascend系列智能手机奖励优秀员工，总经费不超过20000元。因手机热销致华为商城中Ascend系列手机库存有限，公司采购人员决定从为华为商城库存清单中按照价格由高到低的顺序依次挑选购买。
购买规则：  
1. 不同型号的手机价格也不一样，每种型号的手机总购买量不能超过2台。
2. 若某型号的手机只采购了一台且还有库存，但剩余经费不够再购买一台该型号手机，则不可再购买价格更便宜的手机。
请根据库存手机价格清单，计算采购人员购买的手机数量。

####输入  
库存手机数量和所有库存手机的价格信息列表。输入为整数，采用一个空格隔开。输入中第一个整数为库存手机数量，后面的整数为所有库存手机的价格信息。列表中每个价格代表一台手机，若价格相同则为同一型号的手机。假定手机库存总量少于50台。  
例：
> 5 10000 7000 7000 7000 1000
表示库存中共有5台手机，价格分别为
> 10000 7000 7000 7000 1000
但是，只有3种型号的手机，分别为3台7000元的手机，1台10000元的手机，1台1000元的手机。

####输出
购买的手机数量。
####样例输入
`5 10000 7000 7000 7000 1000`
####样例输出
`2`
####源代码
{% highlight java %}
package com.huawei.contest;
import java.util.Scanner;
public class Main1 {
     public static final long MAX_MON=20000;

     public static final int MAX_PH=50;
     public static long[] getInput(){
          Scanner sc = new Scanner(System.in);
          int num = sc.nextInt();
          if(num > MAX_PH)
               num = MAX_PH;
          long[] ph = new long[num];
          for(int i=0; i<num; ++i)
          {
               ph[i] = sc.nextLong();
          }
          return ph;
     }
    
     public static long[] insertSort(long[] elements) {
          for (int i = 1; i < elements.length; i++) {
               int j = -1;
               while (j < i && elements[i] > elements[++j])
                    ;
               if (j < i) {
                    long temp = elements[i];
                    for (int k = i - 1; k >= j; k--) {
                         elements[k + 1] = elements[k];
                    }
                    elements[j] = temp;
               }
          }
          return elements;
     }
    
     public static void main(String[] args)
     {
          long[] a = insertSort(getInput());
          int len = a.length;
          long total=0;
          int buy=0;
          long type=0;
          type = a[len-1];
          int flag=0;
         
          if((total + a[len-1]) > MAX_MON)
               System.out.println(0);

          for(int i=len-1; i>=0; --i)
          {
               if(type == a[i])
               {
                    flag++;
                    if(flag>2)
                    {
                         continue;
                    }
               }
               else
               {
                    flag=0;
                    type = a[i];
               }
              
               if((total+a[i]) <= MAX_MON)
                    {
                    total+=a[i];
                    buy++;
                    }
              
               if((MAX_MON - total) < a[i-1])
               {
                    break;
               }
          }
          System.out.println(buy);
     }    
}
{% endhighlight %}
