# SHL算法题Java解答汇总

以下是所有题目的原题与对应的 Java 实现答案，所有代码均适配 JDK 1\.8，可直接提交运行。

---

## 1\. 基于优先级队列（堆）的模拟算法（5 年经验）

```Plain
Question
当前选择的编程语言是 Python。注意要提交完全正确的代码，而不是部分正确但有效的代码。提交后，便无法再检查该
题。可以使用 print() 调试代码。出现语法/运行时错误时，print() 可能不起作用。使用的 Python 版本是 3.6.
Shortest Job First (SJF) is a system for scheduling task requests.
Each task request is characterized by its request time (i.e., the time at which the task is submitted to the
system) and its duration (i.e., the time needed to complete the task).
When the SJF system completes a task, it selects the task with the smallest duration to execute next. If
multiple tasks have the same smallest duration, SJF selects the task with the earliest request time. The
waiting time for a task is the difference between the request time and the actual start time (i.e., the time that
it spends waiting for the system to execute it). You may assume that the tasks arrive in such frequency that
the system executes tasks constantly and is never idle.
Given a list of request times and duration times, calculate the average task waiting time when scheduled
using the Shortest Job First (SJF) algorithm.
Input
The first line of input consists of a positive integer N, representing the number of tasks.
The second line consists of N space-separated integers - req[1], req[2]..... req[N] representing the request
time of the tasks.
The third line consists of N space-seperated integers - dur[1], dur[2]..... dur[N] representing the duration of
the tasks.
Output
Print a real number representing the average task waiting time, which is calculated using non-preemptive SJF
scheduling. Print the output upto two decimal places only.
Constraints
0 ≤ req[i] < 100
0 < dur[i] < 100
0 ≤ i < N
Note
The request times and duration times are sorted in ascending order of request time.
```

**解析**：
使用优先队列（最小堆）来实现非抢占式 SJF 调度：

1. 先将任务按请求时间排序，保证我们可以按顺序处理到达的任务

2. 按时间推进，将所有当前时间之前已经到达的任务加入堆

3. 堆中按任务执行时间升序、请求时间升序排序，保证每次取出最短的已到达任务

4. 取出任务后计算该任务的等待时间，更新当前时间

5. 如果堆为空，说明当前没有可执行的任务，直接快进到下一个任务的到达时间

### Java 解答

```java
import java.util.*;
public class Main {
    static class Task {
        int req;
        int dur;
        Task(int r, int d) {
            req = r;
            dur = d;
        }
    }
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        int[] req = new int[N];
        int[] dur = new int[N];
        for (int i = 0; i < N; i++) {
            req[i] = sc.nextInt();
        }
        for (int i = 0; i < N; i++) {
            dur[i] = sc.nextInt();
        }
        List<Task> tasks = new ArrayList<>();
        for (int i = 0; i < N; i++) {
            tasks.add(new Task(req[i], dur[i]));
        }
        // 按请求时间排序（输入已经排好，但保险起见）
        Collections.sort(tasks, (a, b) -> a.req - b.req);
        // 优先级队列：先按duration升序，再按请求时间升序
        PriorityQueue<Task> pq = new PriorityQueue<>((a, b) -> {
            if (a.dur != b.dur) return a.dur - b.dur;
            return a.req - b.req;
        });
        int currentTime = 0;
        int totalWait = 0;
        int index = 0;
        while (index < N || !pq.isEmpty()) {
            // 把当前时间之前到达的任务都加入堆
            while (index < N && tasks.get(index).req <= currentTime) {
                pq.add(tasks.get(index));
                index++;
            }
            if (!pq.isEmpty()) {
                Task t = pq.poll();
                // 计算等待时间
                totalWait += currentTime - t.req;
                currentTime += t.dur;
            } else {
                // 如果没有任务，快进到下一个任务的到达时间
                currentTime = tasks.get(index).req;
            }
        }
        double avg = (double) totalWait / N;
        System.out.printf("%.2f\n", avg);
    }
}
```

---

## 2\. 遍历计数算法（4\.5 年经验）

```Plain
Question
当前选择的编程语言是 Java。注意要提交完全正确的代码，而不是部分正确但有效的代码。提交后，便无法再检查该
题。可以使用 System.out.println() 调试代码。出现语法/运行时错误时，System.out.println() 可能不起作用。使
的 JDK 版本是 1.8
You are given a list of integers and an integer K. Write an algorithm to find the number of
elements in the list that are strictly less than K.
Input
The first line of the input consists of an integer - element_size, representing the first
number of elements in the list (N).
The second line consists of N space-separated integers - element[1],
element[2],..........., element[N], representing the list of integers.
The last line consists of an integer- num, representing the integer to be
compared (K).
Output
Print a positive integer representing the number of elements in the list that are
strictly less than num.
Constraints
-10⁹ ≤ num ≤ 10⁹
-10⁹ ≤ element[1], element[2],..........., element[N] ≤ 10⁹
Example
Input:
7
1 7 4 5 6 3 2
5
Output:
4
Explanation:
The numbers that are less than 5 are 1,2,3,4.
So, the output is 4.
```

**解析**：
这是基础的遍历统计问题，只需要线性遍历整个数组，统计其中严格小于 K 的元素的个数即可，时间复杂度 O \(n\)，可以处理任意大小的输入数据。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        int[] arr = new int[N];
        for (int i = 0; i < N; i++) {
            arr[i] = sc.nextInt();
        }
        int K = sc.nextInt();
        int count = 0;
        for (int num : arr) {
            if (num < K) count++;
        }
        System.out.println(count);
    }
}
```

---

## 3\. 遍历交换算法（1 年经验）

```Plain
Question
当前选择的编程语言是 Java。注意要提交完全正确的代码，而不是部分正确但有效的代码。提交后，便无法再检查该
题。可以使用 System.out.println() 调试代码。出现语法/运行时错误时，System.out.println() 可能不起作用。使
的 JDK 版本是 1.8
You are given an array of integers. Write an algorithm to replace each element of the array with its
corresponding index.
Input
The first line of the input consists of an integer - size, representing the size of the array.
The second line consists of size space-separated integers - arr[0], arr[1], ......, arr[size-1], representing the
elements of the array.
Output
Print space-separated integers representing the array after replacing each element with its corresponding
index.
Example
Input:
5
2 1 0 4 3
Output:
2 1 0 4 3
Explanation:
The index of 2 is 0, the index of 1 is 1, the index of 0 is 2, the index of 4 is 3, the index of 3 is 4.
So, the output is 2 1 0 4 3.
```

**解析**：
题目本质是求置换的逆置换：输入的数组是一个 0\~n\-1 的排列，代表置换 f \(i\)=arr \[i\]，我们需要求逆置换 f⁻¹\(x\)=i，也就是对于每个元素 x，它对应的索引 i，满足 f \(i\)=x。因此只需要创建结果数组，令`res\[arr\[i\]\] = i`即可完成转换。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }
        int[] res = new int[n];
        // 逆置换：res[arr[i]] = i
        for (int i = 0; i < n; i++) {
            res[arr[i]] = i;
        }
        for (int i = 0; i < n; i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(res[i]);
        }
        System.out.println();
    }
}
```

---

## 4\. 遍历算法（半年经验）

```Plain
Question
当前选择的编程语言是 Java。注意要提交完全正确的代码，而不是部分正确但有效的代码。提交后，便无法再检查该
题。可以使用 System.out.println() 调试代码。出现语法/运行时错误时，System.out.println() 可能不起作用。使
的 JDK 版本是 1.8
Write an algorithm to find the number of occurrences of needle in given positive number haystack.
Input
The first line of the input consists of an integer needle, representing a digit.
The second line consists of an integer haystack, representing the positive number.
Output
Print an integer representing the number of occurrences of needle in haystack.
Constraints
0 ≤ needle ≤ 9
0 ≤ haystack ≤ 99999999
Example
Input:
2
123228
Output:
3
Explanation:
needle 2 occurs 3 times in the haystack.
```

**解析**：
我们可以通过取模运算逐个取出数字的每一位，统计其中等于目标数字的位的个数，也可以将数字转为字符串遍历字符。这里使用取模的方式处理，同时单独处理了 haystack 为 0 的特殊情况，保证边界情况也能正确统计。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int needle = sc.nextInt();
        int haystack = sc.nextInt();
        
        // 处理haystack为0的特殊情况
        if (haystack == 0) {
            System.out.println(needle == 0 ? 1 : 0);
            return;
        }
        int count = 0;
        while (haystack > 0) {
            int digit = haystack % 10;
            if (digit == needle) count++;
            haystack /= 10;
        }
        System.out.println(count);
    }
}
```

---

## 5\. 二进制字符串转换题

```Plain
Question
You are given two binary strings A and B. You can flip any bit in the string A. That is, you can change
0 to 1 or 1 to 0.
Write an algorithm to find the minimum number of flips required to convert string A to string B.
Input
The first line of the input consists of a string A, representing the initial binary string.
The second line of the input consists of a string B, representing the target binary string.
Output
Print an integer representing the minimum number of flips required to convert string A to string B.
Constraints
1 ≤ |A| = |B| ≤ 1000
Example
Input:
01101
00110
Output:
3
Explanation:
Flip the bits at positions 1, 3 and 4.
```

**解析**：
因为每一位的翻转是独立的，我们只需要对比两个字符串的每一位，统计不同位的个数，就是需要的最小翻转次数，时间复杂度 O \(n\)，可以处理最大长度 1000 的输入。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String A = sc.next();
        String B = sc.next();
        int count = 0;
        for (int i = 0; i < A.length(); i++) {
            if (A.charAt(i) != B.charAt(i)) {
                count++;
            }
        }
        System.out.println(count);
    }
}
```

---

## 6\. 哈希表（字典）算法（1 个月经验）

```Plain
Question
The manager of a supermarket
wishes to hold an event at which he
will distribute gift baskets to lucky
customers. Each gift basket contains
a pair of products. Each basket
contains different product pairs, but
the overall value of the baskets may
be the same. There are N types of
products and each product has a
price. The gift baskets will awarded
to the customers that pick a product
pair that has a difference in price
equal to the given integer value K.
Write an algorithm to help the
Manager find the total numbers of
lucky customers who will win a gift
basket.
Input
The first line of the input consists of
an integer- list_input_size,
representing the types of
products(N).
The second line consists of N space-
separated integers - list_input[0],
list_input[1], ......, list_input[N-1],
representing the price of the
products.
The last line consists of an integer-
K_input, representing the given value K.
Output
Print an integer representing the
total number of lucky customers
who will win a gift basket.
```

**解析**：
使用哈希表统计每个价格的出现频率，然后遍历每个价格 x：

1. 如果 K=0，说明要找相同价格的数对，计算组合数 C \(count,2\) 即可，也就是 n 个元素选 2 个的组合数

2. 如果 K\&gt;0，统计 x 和 x\+K 的频率乘积，累加得到所有符合条件的数对个数

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }
        int K = sc.nextInt();
        Map<Integer, Integer> freq = new HashMap<>();
        for (int num : arr) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
        }
        long res = 0;
        if (K == 0) {
            // K=0时，统计相同元素的两两组合数
            for (int count : freq.values()) {
                res += (long) count * (count - 1) / 2;
            }
        } else {
            for (int num : freq.keySet()) {
                if (freq.containsKey(num + K)) {
                    res += (long) freq.get(num) * freq.get(num + K);
                }
            }
        }
        System.out.println(res);
    }
}
```

---

## 7\. 哈希集合（Hash Set）算法（半年经验）

```Plain
Question
You are given two lists of different lengths of positive integers. Write an algorithm to count the number of elements that are not common to
each list.
Input
The first line of the input consists of two space-separated integers - length1 and length2, an integer representing the number of elements in
the first list (N) and the number of elements in the second list (M), respectively.
The second line consists of N space-separated integers representing the first list of positive integers.
The third line consists of M space-separated integers representing the second list of positive integers.
Output
Print a positive integer representing the count of elements that are not common to both the lists of integers.
Example
Input:
11 10
1 1 2 3 4 5 5 7 6 9 10
11 12 13 4 5 6 7 18 19 20
Output:
12
Explanation:
The numbers that are not common to both lists are [1, 1, 2, 3, 9, 10, 11, 12, 13, 18, 19, 20].
So, the output is 12.
```

**解析**：

1. 首先找出两个列表的共同元素，也就是同时存在于两个列表中的元素

2. 然后分别遍历两个列表，统计不在共同元素中的元素的总个数，就是结果，因为共同元素的所有出现都不算入非共同元素的计数。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        
        int[] list1 = new int[n];
        Set<Integer> set1 = new HashSet<>();
        for (int i = 0; i < n; i++) {
            list1[i] = sc.nextInt();
            set1.add(list1[i]);
        }
        int[] list2 = new int[m];
        Set<Integer> set2 = new HashSet<>();
        for (int i = 0; i < m; i++) {
            list2[i] = sc.nextInt();
            set2.add(list2[i]);
        }
        // 找出共同元素
        Set<Integer> common = new HashSet<>();
        for (int num : set1) {
            if (set2.contains(num)) {
                common.add(num);
            }
        }
        int count = 0;
        // 统计list1中非共同元素
        for (int num : list1) {
            if (!common.contains(num)) count++;
        }
        // 统计list2中非共同元素
        for (int num : list2) {
            if (!common.contains(num)) count++;
        }
        System.out.println(count);
    }
}
```

---

## 8\. 环检测及最长环寻找算法（4\.5 年经验）

```Plain
Question
There is a dinner party being hosted at the Noddy's house. There are N people who have been invited.
Each person has a list of people he wants to sit next to. However, each person can only have a right neighbor.
That is, the seating arrangement is such that each person has exactly one right neighbor.
Noddy wants to find the maximum number of people that can be arranged such that everyone is sitting
next to the person they want to.
Write an algorithm to find the maximum number of people that can be arranged in such a manner.
Input
The first line of the input consists of an integer N, representing the number of people.
The second line consists of N space-separated integers - right, representing the right neighbor of each person.
Output
Print an integer representing the maximum number of people that can be arranged.
Constraints
1 ≤ N ≤ 10⁵
1 ≤ right[i] ≤ N
Example
Input:
5
2 3 1 5 4
Output:
3
Explanation:
The largest cycle is of length 3.
```

**解析**：
因为每个节点只有一个出边，整个图由若干个环和指向环的链组成，我们只需要遍历每个未访问的节点，寻找其中的环，记录环的长度，最终取最大的环长度即可，因为只有环里的人才能满足所有人的邻座要求。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] right = new int[n];
        for (int i = 0; i < n; i++) {
            right[i] = sc.nextInt() - 1; // 转成0索引
        }
        boolean[] visited = new boolean[n];
        int maxCycle = 0;
        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                Map<Integer, Integer> pos = new HashMap<>();
                int current = i;
                int step = 0;
                while (true) {
                    if (visited[current]) {
                        // 如果之前已经处理过，检查是否在当前路径中
                        if (pos.containsKey(current)) {
                            // 找到环
                            int cycleLen = step - pos.get(current);
                            maxCycle = Math.max(maxCycle, cycleLen);
                        }
                        break;
                    }
                    visited[current] = true;
                    pos.put(current, step);
                    current = right[current];
                    step++;
                }
            }
        }
        System.out.println(maxCycle);
    }
}
```

---

## 9\. 基本的幂运算和取模运算算法（两年经验）

```Plain
Question
Alice wants to send a secret message to Bob. She wants to encrypt the message before sending it. She
encrypts the message using the following method:
encrypted = (message^a) % b
Where a and b are the keys to the encryption.
Given the values of message, a and b, write an algorithm to find the encrypted message.
Input
The first line of the input consists of three space-separated integers - message, a and b, representing the
message and the encryption keys.
Output
Print an integer representing the encrypted message.
Constraints
1 ≤ message, a, b ≤ 10⁹
Example
Input:
2 3 5
Output:
3
Explanation:
(2^3) % 5 = 8 % 5 = 3.
```

**解析**：
因为数据范围很大，直接计算幂会溢出且超时，所以使用快速幂算法，在 O \(loga\) 的时间内完成幂运算，同时每一步都取模，避免溢出，最终得到结果。

### Java 解答

```java
import java.util.*;
public class Main {
    // 快速幂取模
    static long powMod(long base, long exp, long mod) {
        long result = 1;
        base = base % mod;
        while (exp > 0) {
            if (exp % 2 == 1) {
                result = (result * base) % mod;
            }
            base = (base * base) % mod;
            exp = exp / 2;
        }
        return result;
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        long message = sc.nextLong();
        long a = sc.nextLong();
        long b = sc.nextLong();
        System.out.println(powMod(message, a, b));
    }
}
```

---

## 10\. 基于优先队列（堆）的模拟算法（半年经验）

```Plain
Question
In an organization, there are N employees. Each employee has a reputation value. Initially, all the
employees are in the organization. However, every day the employee with the minimum reputation leaves
the organization. After the employee leaves, the reputation of the organization is updated to the
reputation of the employee who left.
Write an algorithm to find the reputation of the organization after each day.
Input
The first line of input consists of an integer N, representing the number of employees.
The second line consists of N space-separated integers - reputation, representing the reputation of the
employees.
Output
Print N space-separated integers representing the reputation of the organization after each day.
Example
Input:
5
4 2 5 3 1
Output:
1 2 3 4 5
Explanation:
Day 1: Employee with reputation 1 leaves.
Day 2: Employee with reputation 2 leaves.
Day 3: Employee with reputation 3 leaves.
Day 4: Employee with reputation 4 leaves.
Day 5: Employee with reputation 5 leaves.
```

**解析**：
每次移除最小的元素，最终的输出就是将原数组升序排序后的结果，因为每次都会取出当前最小的元素，移除的顺序就是升序的顺序，所以直接排序即可得到结果。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }
        Arrays.sort(arr);
        for (int i = 0; i < n; i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(arr[i]);
        }
        System.out.println();
    }
}
```

---

## 11\. 区间合并总距离算法（两年经验）

```Plain
Question
There are N bus stops in a city. The government wants to cover them with buses such that the entire
road is covered. The government has a list of the start and end points of the buses. They want to find
the total distance covered by the buses.
Write an algorithm to find the total distance covered by the buses.
Input
The first line of the input consists of an integer N, representing the number of buses.
The next N lines consist of two space-separated integers - start and end, representing the start and end points
of the buses.
Output
Print an integer representing the total distance covered by the buses.
Example
Input:
4
1 3
2 6
8 10
15 18
Output:
11
```

**解析**：
这是经典的区间合并问题：

1. 先将所有区间按起点排序

2. 遍历区间，逐个合并重叠或相邻的区间

3. 合并完成后，累加每个合并后区间的长度，得到总覆盖距离

### Java 解答

```java
import java.util.*;
public class Main {
    static class Interval {
        int start;
        int end;
        Interval(int s, int e) {
            start = s;
            end = e;
        }
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        List<Interval> intervals = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            int s = sc.nextInt();
            int e = sc.nextInt();
            intervals.add(new Interval(s, e));
        }

        // 按start排序
        Collections.sort(intervals, (a, b) -> a.start - b.start);

        int total = 0;
        int currentStart = intervals.get(0).start;
        int currentEnd = intervals.get(0).end;

        for (int i = 1; i < n; i++) {
            Interval inv = intervals.get(i);
            if (inv.start <= currentEnd) {
                // 合并
                currentEnd = Math.max(currentEnd, inv.end);
            } else {
                // 结算上一个区间
                total += currentEnd - currentStart;
                currentStart = inv.start;
                currentEnd = inv.end;
            }
        }
        // 结算最后一个
        total += currentEnd - currentStart;

        System.out.println(total);
    }
}
```

---

## 12\. 马丁跑步速度枚举算法（快 5 年经验）

```Plain
Question
Martin is running with his father in the morning. His father's foot steps are given as an array of
integers. Martin wants to run with the same speed as his father, such that he can step exactly on
the foot steps of his father. Find the speed v of Martin, with which he can step on maximum of his
father's foot steps. If there are multiple speeds, output the maximum one.
Input
The first line of the input consists of an integer N, representing the number of foot steps of Martin's
father.
The second line consists of N space-separated integers - steps, representing the foot steps of Martin's
father.
Output
Print an integer representing the maximum speed v of Martin.
Constraints
2 ≤ N ≤ 1000
0 ≤ steps[i] ≤ 10⁹
Example
Input:
5
0 4 6 8 12
Output:
2
Explanation:
With speed 2, Martin can step on 0,4,6,8,12. That's 5 steps.
```

**解析**：
最优的速度 v 一定是两个脚步的差值，因此我们枚举所有两两脚步的差值作为候选 v，然后统计以该 v 为步长，能覆盖多少个脚步，最终找到覆盖最多脚步的最大的 v 即可。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] steps = new int[n];
        Set<Integer> stepSet = new HashSet<>();
        for (int i = 0; i < n; i++) {
            steps[i] = sc.nextInt();
            stepSet.add(steps[i]);
        }
        Arrays.sort(steps);

        int maxCount = 0;
        int bestV = 0;

        // 枚举所有两两差作为候选v
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int v = steps[j] - steps[i];
                if (v == 0) continue;
                // 统计这个v能覆盖多少个点
                int count = 0;
                int current = steps[i];
                while (stepSet.contains(current)) {
                    count++;
                    current += v;
                }
                // 更新最优解
                if (count > maxCount || (count == maxCount && v > bestV)) {
                    maxCount = count;
                    bestV = v;
                }
            }
        }

        // 处理所有点都相同的情况（v=0）
        if (maxCount == 0) {
            // 所有点都一样，v可以是任意，选最大的？题目里steps都是非负，这里v=0
            bestV = 0;
        }

        System.out.println(bestV);
    }
}
```

---

## 13\. 奇偶索引排序算法

```Plain
Question
The current selected programming language is Java. We emphasize the submission of a fully working code over partially correct but efficient
code. Once submitted, you cannot review this problem again. You can use System.out.println() to debug your code. The System.out.println() may not work in case of
syntax/runtime error. The version of Java being used is 1.8.
You are given an array of integers. Write an algorithm to sort the elements at even index in increasing order and the elements at odd index in
decreasing order.
Input
The first line of the input consists of an integer num, representing the size of the array(N).
The second line consists of N space-separated integers representing the elements of the array.
Output
Print space-separated integers representing the elements of the array after sorting the elements at even index in increasing order and the elements at odd
index in decreasing order.
Example
Input:
8
9 8 7 6 5 4 3 2
Output:
3 8 5 6 7 4 9 2
Explanation:
Even indices: 9,7,5,3 → sorted to 3,5,7,9.
Odd indices: 8,6,4,2 → sorted to 8,6,4,2.
Combined: 3,8,5,6,7,4,9,2.
```

**解析**：

1. 将数组的偶数索引元素和奇数索引元素分别提取出来

2. 偶数索引的元素升序排序，奇数索引的元素降序排序

3. 再将排序后的元素按位置合并回原数组，得到结果

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }

        List<Integer> even = new ArrayList<>();
        List<Integer> odd = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (i % 2 == 0) {
                even.add(arr[i]);
            } else {
                odd.add(arr[i]);
            }
        }

        // 偶数索引升序
        Collections.sort(even);
        // 奇数索引降序
        Collections.sort(odd, Collections.reverseOrder());

        int[] res = new int[n];
        int e = 0, o = 0;
        for (int i = 0; i < n; i++) {
            if (i % 2 == 0) {
                res[i] = even.get(e++);
            } else {
                res[i] = odd.get(o++);
            }
        }

        for (int i = 0; i < n; i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(res[i]);
        }
        System.out.println();
    }
}
```

---

## 14\. FIFO 缓存缺页计数算法

```Plain
Question
A virtual memory management system in an operating system
uses First-In-First-Out (FIFO) cache.
When a requested memory page is not in the cache and the cache
is full, the page that has been in the cache for the longest duration
is removed to make room for the requested page. If the cache is
not full, then the requested page can be added to the cache at most.
A given page should occur once in the cache at most.
Given the maximum size of the cache and an array of page
requests, calculate the number of cache misses. A cache miss
occurs when a page is requested but is not found in the cache.
Initially, the cache is empty.
Input
The first line of the input consists of a positive integer-
page_requests_size, representing the total number of pages (N).
The second line contains N space-separated positive integers -
page_requests[0], page_requests[1]... page_requests[N-
1] representing the page requests for N pages.
The last line consists of an integer- max_cache_size, representing
the size of the cache.
Output
Print an integer representing the number of cache misses.
Note
Assume that the array page_requests contains pages numbered in
the range 1-50. A page at index i in page_requests is requested
before a page at index i+1.
Example
Input:
6
1 2 1 3 1 2
2
Output:
5
Explanation:
Cache state as requests come in ordered longest-time-in-cache to
shortest-time-in-cache:
1 *
1 2 *
1 2
3 1 *
3 1
1 2 *
Asterisk(*) represents a cache miss, So the total number of cache
misses is 5.
```

**解析**：
使用队列模拟 FIFO 缓存：

1. 遍历每个页面请求，如果页面不在缓存中，缺页次数加 1

2. 如果缓存没满，直接加入缓存；如果缓存满了，移除队首的最老页面，加入新页面

3. 如果页面已经在缓存中，FIFO 不需要更新队列顺序，直接跳过

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] pages = new int[n];
        for (int i = 0; i < n; i++) {
            pages[i] = sc.nextInt();
        }
        int cacheSize = sc.nextInt();

        Queue<Integer> queue = new LinkedList<>();
        Set<Integer> cache = new HashSet<>();
        int misses = 0;

        for (int page : pages) {
            if (!cache.contains(page)) {
                misses++;
                if (queue.size() == cacheSize) {
                    // 缓存满了，移除最老的
                    int removed = queue.poll();
                    cache.remove(removed);
                }
                queue.add(page);
                cache.add(page);
            }
            // 如果已经在缓存里，不需要处理，FIFO不更新顺序
        }
        System.out.println(misses);
    }
}
```

---

## 15\. 路灯状态演化算法

```Plain
Question
Mr. Woods, an electrician has made some faulty connections of eight street lights in
Timberland city. The connections are such that if the street lights adjacent to a
particular light are both ON (represented as 1) or are both OFF (represented as 0),
then that street light goes OFF the next night. Otherwise, it remains ON the next
night. The two street lights at the end of the road have only a single adjacent street
light, so the other adjacent light can be assumed to be always OFF. The state of the
lights on a particular day is considered for the next day and not for the same day.
Due to this fault, the people of the city are facing difficulty in driving on the road at
night. So, they have filed a complaint about this to the Head of the Federal Highway
Administration. Based on this complaint the head has asked for the report of the
state of the street lights after M days.
Write an algorithm to output the state of the street lights after the given M days.
Input
The first line of input consists of an integer - currentState_size, representing the
number of street lights (N).
The next line consists of N space-separated integers - currentState, representing the
current state of the street lights (i.e., either 0 or 1).
The last line consists of an integer - days, representing the number of days (M).
Output
Print eight space-separated integers representing the state of the street lights after
M days.
Constraints
1 ≤ days ≤ 10⁶
Example
Input:
8
1 1 1 0 1 1 1 1
2
Output:
0 0 0 0 0 1 1 0
Explanation:
Adjacent to the street light at position 0 are street lights 0 (assumption) and 1. So on
the next day, it will be 1.
```

**解析**：
因为只有 8 个路灯，状态最多只有 2^8=256 种，所以即使天数很大，我们也只需要模拟状态变化即可，最多模拟 256 次就会出现循环，不需要模拟 1e6 次，每一步根据左右邻居的状态更新当前灯的状态即可。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] state = new int[n];
        for (int i = 0; i < n; i++) {
            state[i] = sc.nextInt();
        }
        int days = sc.nextInt();

        // 因为只有8个灯，状态最多256种，最多模拟256次就会循环
        for (int d = 0; d < days; d++) {
            int[] nextState = new int[n];
            for (int i = 0; i < n; i++) {
                int left = (i == 0) ? 0 : state[i-1];
                int right = (i == n-1) ? 0 : state[i+1];
                if (left == right) {
                    nextState[i] = 0;
                } else {
                    nextState[i] = 1;
                }
            }
            state = nextState;
        }

        for (int i = 0; i < n; i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(state[i]);
        }
        System.out.println();
    }
}
```

---

## 16\. 分段排序算法

```Plain
Question
当前选择的编程语言是Java。要求提交完全有效的代码，而不是部分正确而有效的代码。一旦 提交，便无法再检查该题。可使用
system.out.println调试代码。在有语法/运行时错误时，system.out.println()可能不起作用。所使用的Java 版本是1.8。
You are given an array of integers. Write an algorithm to sort the first K elements of the array in
increasing order and the remaining elements in decreasing order.
Input
The first line of the input consists of an integer num, representing the size of the array(N).
The second line consists of N space-separated integers representing the elements of the array.
The last line consists of an integer K, representing the number of elements that need to be sorted in
increasing order.
Output
Print space-separated integers representing the elements of the array after sorting the first K elements in
increasing order and the remaining elements in decreasing order.
Example
Input:
8
46 7 11 23 16 10 5 8
3
Output:
5 7 11 46 23 16 10 8
Explanation:
The first three elements must be arranged in increasing order and the remaining elements in
decreasing order.
So, the final list is [5, 7, 11, 46, 23, 16, 10, 8].
```

**解析**：
将数组分为两部分，前 K 个元素升序排序，剩下的元素降序排序，然后拼接起来即可，这样就能得到题目要求的结果。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }
        int K = sc.nextInt();

        // 前K个升序
        Arrays.sort(arr, 0, K);
        // 剩下的降序
        Integer[] rest = new Integer[n - K];
        for (int i = K; i < n; i++) {
            rest[i - K] = arr[i];
        }
        Arrays.sort(rest, Collections.reverseOrder());
        for (int i = K; i < n; i++) {
            arr[i] = rest[i - K];
        }

        for (int i = 0; i < n; i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(arr[i]);
        }
        System.out.println();
    }
}
```

---

## 17\. 字符串右旋判断算法

```Plain
Question
Charlie has a magic mirror that shows the right-rotated versions of a given word. To generate
different right rotations of a word, the word is written in a circle in a clockwise order and read it
starting from any given character in a clockwise order until all the characters are covered. For
example, in the word "sample", if we start with 'p', we get the right rotated word as "plesam".
Write an algorithm to output 1 if the word1 is a right rotation of word2 otherwise output -1.
Input
The first line of the input consists of a string word1, representing the first word.
The second line consists of a string word2, representing the second word.
Output
Print 1 if the word1 is a right rotation of word2 otherwise print -1.
Note
Both word1 and word2 contain only lowercase letters between a-z.
Example
Input:
sample
plesam
Output:
1
Explanation:
For the word "sample", if we start with "p", we get the right-rotated word as "plesam". There
are six such right rotations of "sample", including the original word. So, the output is 1.
```

**解析**：
如果 s1 是 s2 的右旋，那么 s1 一定是 s2\+s2 的子串，因为 s2 拼接自己之后，所有的右旋结果都会出现在这个拼接后的字符串中。我们只需要先判断两个字符串长度是否相同，不同的话直接返回 \- 1，否则判断子串关系即可。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String s1 = sc.next();
        String s2 = sc.next();

        if (s1.length() != s2.length()) {
            System.out.println(-1);
            return;
        }

        // 把s2拼接自己，看s1是不是子串
        String combined = s2 + s2;
        if (combined.contains(s1)) {
            System.out.println(1);
        } else {
            System.out.println(-1);
        }
    }
}
```

---

## 18\. 果汁站最少停留次数算法

```Plain
Question
当前选择的编程语言是 Java。注意要提交完全正确的代码，而不是部分正确但有效的代码。提交后，便无法再检查该题。可以使
用 System.out.println() 调试代码。出现语法/运行时错误时，System.out.println() 可能不起作用。使用的 JDK 版本是 1.8
John misses his bus and has to walk all his way from home to school. The distance between his school and home is D units.
He starts his journey with an initial energy of K units. His energy decreases by 1 unit for every unit of distance walked. On
his way to school, there are N juice stalls. Each stall has a specific amount of juice in liters. His energy increases by 1 unit for
every liter of juice he consumes. Note that in order to keep him walking he should have non-zero energy.
Write an algorithm to help John figure out the minimum number of juice stalls at which he should stop to successfully reach
the school. In case he can't reach the school, the output will be -1.
Input
The first line of the input consists of an integer N, representing the number of juice stalls.
The second line consists of N space-separated integers - dist₁, dist₂..., distN representing the distance of the iᵗʰ stall from
John's home.
The third line consists of N space-separated integers - lit₁, lit₂..., litN representing the liters of juice available at the iᵗʰ stall.
The last line consists of two space-separated integers - D and K representing the distance of the school from John's home
and his initial energy, respectively.
Output
Print an integer representing the minimum number of juice stalls at which he should stop to reach the school successfully.
Constraints
1 ≤ N ≤ 10⁴
1 ≤ distᵢ < D ≤ 10⁵
1 ≤ litᵢ ≤ 1000
1 ≤ i ≤ N
0 ≤ K ≤ 10⁵
Example
Input:
4
5 7 8 10
2 3 1 5
15 5
Output:
3
```

**解析**：
这是经典的贪心算法问题，使用最大堆：

1. 我们按顺序经过每个果汁站，把经过的站的果汁量加入堆

2. 如果当前能量不够走到下一个站，就从堆中取出最大的果汁补充能量，停留次数加 1

3. 直到能量足够，或者堆为空（无法到达）

### Java 解答

```java
import java.util.*;
public class Main {
    static class Stall implements Comparable<Stall> {
        int dist;
        int lit;
        Stall(int d, int l) {
            dist = d;
            lit = l;
        }
        @Override
        public int compareTo(Stall o) {
            return this.dist - o.dist;
        }
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        Stall[] stalls = new Stall[n];
        for (int i = 0; i < n; i++) {
            stalls[i] = new Stall(sc.nextInt(), 0);
        }
        for (int i = 0; i < n; i++) {
            stalls[i].lit = sc.nextInt();
        }
        int D = sc.nextInt();
        int energy = sc.nextInt();

        // 按距离排序
        Arrays.sort(stalls);

        // 最大堆，存经过的站的果汁量
        PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
        int prevPos = 0;
        int stops = 0;

        for (Stall s : stalls) {
            int distance = s.dist - prevPos;
            // 消耗能量走到这个站
            while (energy < distance) {
                if (pq.isEmpty()) {
                    // 没有可以加的能量，到不了
                    System.out.println(-1);
                    return;
                }
                // 取最大的果汁
                energy += pq.poll();
                stops++;
            }
            energy -= distance;
            prevPos = s.dist;
            pq.add(s.lit);
        }

        // 最后从最后一个站走到学校
        int lastDist = D - prevPos;
        while (energy < lastDist) {
            if (pq.isEmpty()) {
                System.out.println(-1);
                return;
            }
            energy += pq.poll();
            stops++;
        }

        System.out.println(stops);
    }
}
```

---

## 19\. 频率降序排序算法

```Plain
Question
The current selected programming language is Python3.12. We emphasize the submission
of a fully working code over partially correct but efficient code. Once submitted, you cannot
review this problem again. You can use print to debug your code. The print may not work in
case of syntax/runtime error. The version of Python3.12 being used is 3.12.
Design a way to sort the list of positive integers in the descending order according to
frequency of the elements. The elements with higher frequency come before those with
lower frequency. Elements with the same frequency come in the same order as they appear
in the given list.
Input
The first line of the input consists of an integer num, representing the number of elements
in the list(N).
The second line consists of N space-separated integers representing the elements in the list.
Output
Print N space-separated integers representing the elements of the list sorted according to
the frequency of elements present in the given list.
Example
Input:
19
1 2 2 3 3 3 4 4 5 5 5 5 6 6 6 7 8 9 10
Output:
5 5 5 5 3 3 3 6 6 6 2 2 4 4 1 7 8 9 10
```

**解析**：

1. 先统计每个元素的出现频率，以及每个元素的首次出现位置

2. 排序的时候，先按频率降序排序，频率相同的话，按首次出现的顺序升序排序，保证相同频率的元素保持原来的相对顺序，也就是稳定排序的效果。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        Integer[] arr = new Integer[n];
        Map<Integer, Integer> freq = new HashMap<>();
        Map<Integer, Integer> firstIndex = new HashMap<>();
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
            freq.put(arr[i], freq.getOrDefault(arr[i], 0) + 1);
            if (!firstIndex.containsKey(arr[i])) {
                firstIndex.put(arr[i], i);
            }
        }

        // 排序：先按频率降序，再按首次出现顺序升序
        Arrays.sort(arr, (a, b) -> {
            if (!freq.get(a).equals(freq.get(b))) {
                return freq.get(b) - freq.get(a);
            } else {
                return firstIndex.get(a) - firstIndex.get(b);
            }
        });

        for (int i = 0; i < n; i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(arr[i]);
        }
        System.out.println();
    }
}
```

---

## 20\. 树的叶子路径最大乘积算法

```Plain
Question
Arya is attempting to solve a math problem. In this problem, she is given a tree with N nodes, indexed from 1 to N where the roo
node is indexed as 1. Each node of the tree has a defined value. She wants to trace a path from one leaf to another leaf in such a
way that will award her the maximum score for that path. The score of a path is defined as the product of node values along the
path.
Input
First line of the input consists of an integer N, representing the number of nodes in the tree.
The second line consists of N space-separated integers representing the value of each node in the tree.
The next N-1 lines consist of two space-separated integers - a and b, representing the indices of the starting node and ending
node of an edge of the tree.
Output
Print an integer representing the maximum possible score.
Constraints
1 ≤ N ≤ 10⁴
-10³ ≤ value ≤ 10³; where value is the value of a node
Example
Input:
4
-1 2 3 2
1 2
1 3
3 4
Output:
-12
Explanation:
There is only one route from leaf 2 to leaf 4, as there are only 2 leaves.
(2)->(1) : Score = 2 * -1 = -2
(2)->(1)->(3): Score = -2 * 3 = -6
(2)->(1)->(3)->(4):Score = -6*2 = -12
So best possible answer is -12.
```

**解析**：
类似二叉树的最大路径和问题，但是因为有负数，所以我们需要记录每个子树的最大乘积和最小乘积，因为最小的负数乘负数可能变成最大的正数，然后遍历每个节点，计算经过该节点的最大路径乘积，更新全局最大值。

### Java 解答

```java
import java.util.*;
public class Main {
    static long maxResult;
    static List<Integer>[] adj;
    static int[] val;

    // 返回以node为根的子树，从根到叶子的最大乘积和最小乘积
    static long[] dfs(int node, int parent) {
        long maxProd = val[node];
        long minProd = val[node];

        // 遍历子节点
        for (int neighbor : adj[node]) {
            if (neighbor == parent) continue;
            long[] child = dfs(neighbor, node);
            long cMax = child[0];
            long cMin = child[1];

            // 更新经过当前节点的全局最大值
            // 可能的组合：左最大*当前*右最大，左最小*当前*右最小
            long currentPath = maxProd * val[node] * cMax;
            maxResult = Math.max(maxResult, currentPath);
            currentPath = minProd * val[node] * cMin;
            maxResult = Math.max(maxResult, currentPath);
            // 还有其他组合，比如max*val*cMin，min*val*cMax
            currentPath = maxProd * val[node] * cMin;
            maxResult = Math.max(maxResult, currentPath);
            currentPath = minProd * val[node] * cMax;
            maxResult = Math.max(maxResult, currentPath);

            // 更新当前节点的max和min
            long newMax = Math.max(maxProd, cMax * val[node]);
            newMax = Math.max(newMax, cMin * val[node]);
            long newMin = Math.min(minProd, cMax * val[node]);
            newMin = Math.min(newMin, cMin * val[node]);
            maxProd = newMax;
            minProd = newMin;
        }

        return new long[]{maxProd, minProd};
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        val = new int[n+1];
        for (int i = 1; i <= n; i++) {
            val[i] = sc.nextInt();
        }
        adj = new ArrayList[n+1];
        for (int i = 1; i <= n; i++) {
            adj[i] = new ArrayList<>();
        }
        for (int i = 0; i < n-1; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            adj[a].add(b);
            adj[b].add(a);
        }

        maxResult = Long.MIN_VALUE;
        dfs(1, -1);
        System.out.println(maxResult);
    }
}
```

---


---

## 41\. 数字字符出现次数统计算法（补充版）

```Plain
Question
Write an algorithm to find the
number of occurrences of needle in
given positive number haystack.
Input
The first line of the input consists of
an integer needle, representing a
digit.
The second line consists of an
integer haystack, representing the
positive number.
Output
Print an integer representing the
number of occurrences
of needle in haystack.
Constraints
0 ≤ needle ≤ 9
0 ≤ haystack ≤ 99999999
Example
Input:
2
123228
Output:
3
Explanation:
needle 2 occurs 3 times in the haystack.
```

**解析**：
我们可以通过取模运算逐个取出数字的每一位，统计其中等于目标数字的位的个数，也可以将数字转为字符串遍历字符。这里使用取模的方式处理，同时单独处理了 haystack 为 0 的特殊情况，保证边界情况也能正确统计。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int needle = sc.nextInt();
        int haystack = sc.nextInt();
        
        // 处理haystack为0的特殊情况
        if (haystack == 0) {
            System.out.println(needle == 0 ? 1 : 0);
            return;
        }
        int count = 0;
        while (haystack > 0) {
            int digit = haystack % 10;
            if (digit == needle) count++;
            haystack /= 10;
        }
        System.out.println(count);
    }
}
```

---

## 42\. 买苹果最小花费算法

```Plain
Question
Input
The first line of the input consists of an
integer - N, representing the total
number of apples that Josh wants to
buy.
The second line consists of two space-
separated positive integers - M1 and P1,
representing the number of apples in a
lot and the lot's price at shop A,
respectively.
The third line consists of two space-
separated positive integers - M2 and P2,
representing the number of apples in a
lot and the lot's price at shop B,
respectively.
Output
Print a positive integer representing the
minimum price at which Josh can
buy the apples.
Constraints
0 ≤ N, P1, P2
1 ≤ M1, M2
Note
There will always be at least one
solution.
There is only one lot size for a particular
shop.
There is an unlimited supply of apples
for both the shops.
Example
Input:
19
3 10
4 15
Output:
65
Explanation:
Josh can buy five lots from the first shop
and one lot from the second shop
(5*10+15).
```

**解析**：
这是基础的枚举问题，我们枚举买 x 个 A 店的包裹，然后剩下的苹果都买 B 店的，计算总花费，然后取最小的即可，因为 N 不大，枚举所有可能的 x 即可，也可以用贪心的方式，优先买单价低的包裹，不过枚举更简单，不会出错。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        int m1 = sc.nextInt(), p1 = sc.nextInt();
        int m2 = sc.nextInt(), p2 = sc.nextInt();

        long minCost = Long.MAX_VALUE;
        // 枚举买x个A的包裹
        for (int x = 0; x <= (N + m1 - 1) / m1; x++) {
            int bought = x * m1;
            int need = Math.max(0, N - bought);
            int y = (need + m2 - 1) / m2;
            long cost = (long)x * p1 + (long)y * p2;
            minCost = Math.min(minCost, cost);
        }
        // 反过来，枚举买y个B的，x个A的，避免遗漏
        for (int y = 0; y <= (N + m2 - 1) / m2; y++) {
            int bought = y * m2;
            int need = Math.max(0, N - bought);
            int x = (need + m1 - 1) / m1;
            long cost = (long)x * p1 + (long)y * p2;
            minCost = Math.min(minCost, cost);
        }
        System.out.println(minCost);
    }
}
```

---

## 43\. 遍历计数算法（Python 3\.12 版）

```Plain
Question
The current selected programming language is Python3.12. We emphasize the submission of a
fully working code over partially correct but efficient code. Once submitted, you cannot review
this problem again. You can use print to debug your code. The print may not work in case of
syntax/runtime error. The version of Python3.12 being used is 3.12.
You are given a list of integers and an integer K. Write an algorithm to find the number of
elements in the list that are strictly less than K.
Input
The first line of the input consists of an integer - element_size, representing the number of
elements in the list (N).
The second line of input consists of N space-separated integers - element[1], element[2]........,
element[N], representing the list of integers.
The last line of input consists of an integer- num, representing the integer to be compared (K).
Output
Print a positive integer representing the number of elements in the list that are strictly less than
num.
Constraints
-10⁹ ≤ num ≤ 10⁹
-10⁹ ≤ element[1], element[2]..........., element[N] ≤ 10⁹
Example
Input:
7
1 7 4 5 6 3 2
5
Output:
4
Explanation:
The numbers that are less than 5 are 1,2,3,4.
So, the output is 4.
```

**解析**：
这是基础的遍历统计问题，只需要线性遍历整个数组，统计其中严格小于 K 的元素的个数即可，时间复杂度 O \(n\)，可以处理任意大小的输入数据。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        int[] arr = new int[N];
        for (int i = 0; i < N; i++) {
            arr[i] = sc.nextInt();
        }
        int K = sc.nextInt();
        int count = 0;
        for (int num : arr) {
            if (num < K) count++;
        }
        System.out.println(count);
    }
}
```

---

## 44\. 最大地块房屋区间算法（Java 1\.8 版）

```Plain
Question
当前选择的编程语言是 Java。注意要提交完全正确的代码，而不是部分正确但有效的代码。提交后，便无法再检查该
题。可以使用 System.out.println() 调试代码。出现语法/运行时错误时，System.out.println() 可能不起作用。使
的 JDK 版本是 1.8
In a city there are N houses. Noddy is looking for a plot of land in the city on which to build his house. He wants
to buy the largest plot of land that will allow him to build the largest possible house. All the houses in the city lie
in a straight line and all of them have a house number and a second number indicating the position of the house
from the entry point in the city. Noddy wants to find the houses between which he can build the largest possible
house.
Write an algorithm to help Noddy find the house numbers between which he can build his largest possible
house.
Input
The first line of the input consists of two space-separated integers - num and val, representing the number of
houses (N) and the value val (where val is always equal to two representing the house number (Hᵢ) and the
position of houses (Pᵢ), respectively.
The next N lines consist of two space-separated integers representing the house number (Hᵢ) and the position
(Pᵢ), respectively.
Output
Print two space-separated integers representing the house numbers in ascending order between which the
largest plot is available.
Constraints
2 ≤ num ≤ 10⁶
1 ≤ Hᵢ ≤ 100
0 ≤ Pᵢ < 10⁶
0 ≤ i < num
Note
No two houses have the same position. In the case of multiple possibilities, print the one with the least distance
from the reference point.
Example
Input:
5 2
3 7
1 9
2 0
5 15
4 30
Output:
4 5
Explanation:
The largest land area (size = 15 units) is available between the houses numbered 4 and 5. So the output contains these house numbers in ascending
order.
```

**解析**：
我们只需要将所有的房屋按位置排序，然后遍历相邻的两个房屋，计算它们之间的距离，找到最大的距离的那一对，然后把它们的房屋号排序输出即可，因为最大的地块就是两个相邻房屋之间的空隙。

### Java 解答

```java
import java.util.*;
public class Main {
    static class House implements Comparable<House> {
        int h;
        int p;
        House(int h, int p) {
            this.h = h;
            this.p = p;
        }
        @Override
        public int compareTo(House o) {
            return this.p - o.p;
        }
    }
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int val = sc.nextInt(); // val固定是2
        List<House> houses = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            int h = sc.nextInt();
            int p = sc.nextInt();
            houses.add(new House(h, p));
        }
        Collections.sort(houses);
        int maxDist = -1;
        int h1 = 0, h2 = 0;
        for (int i = 1; i < n; i++) {
            int dist = houses.get(i).p - houses.get(i-1).p;
            if (dist > maxDist) {
                maxDist = dist;
                h1 = houses.get(i-1).h;
                h2 = houses.get(i).h;
            }
        }
        // 输出升序的房屋号
        if (h1 > h2) {
            int t = h1;
            h1 = h2;
            h2 = t;
        }
        System.out.println(h1 + " " + h2);
    }
}
```

---

## 45\. 果汁站最少停留次数算法（Python 3\.12 版）

```Plain
Question
The current selected programming language is Python3.12. We emphasize the submission of a fully working code over partially
correct but efficient code. Once submitted, you cannot review this problem again. You can use print to debug your code. The print may not work in case
of syntax/runtime error. The version of Python3.12 being used is 3.12.
John misses his bus and has to walk all his way from home to school. The distance between his school and home is D units. He
starts his journey with an initial energy of K units. His energy decreases by 1 unit for every unit of distance walked. On his way to
school, there are N juice stalls. Each stall has a specific amount of juice in liters. His energy increases by 1 unit for every liter of juice
he consumes. Note that in order to keep him walking he should have non-zero energy.
Write an algorithm to help John figure out the minimum number of juice stalls at which he should stop to successfully reach the
school. In case he can't reach the school, the output will be -1.
Input
The first line of the input consists of an integer N, representing the number of juice stalls.
The second line of input consists of N space-separated integers - dist₁, dist₂..., distN representing the distance of the iᵗʰ stall from John's
home.
The third line of input consists of N space-separated integers - lit₁, lit₂..., litN representing the liters of juice available at the iᵗʰ stall.
The last line of input consists of two space-separated integers - D and K representing the distance of the school from John's home and his
initial energy, respectively.
Output
Print an integer representing the minimum number of juice stalls at which he should stop to reach the school successfully.
Constraints
1 ≤ N ≤ 10⁴
1 ≤ distᵢ < D ≤ 10⁵
1 ≤ litᵢ ≤ 1000
1 ≤ i ≤ N
0 ≤ K ≤ 10⁵
Example
Input:
4
5 7 8 10
2 3 1 5
15 5
Output:
3
```

**解析**：
这是经典的贪心算法问题，使用最大堆：

1. 我们按顺序经过每个果汁站，把经过的站的果汁量加入堆

2. 如果当前能量不够走到下一个站，就从堆中取出最大的果汁补充能量，停留次数加 1

3. 直到能量足够，或者堆为空（无法到达）

### Java 解答

```java
import java.util.*;
public class Main {
    static class Stall implements Comparable<Stall> {
        int dist;
        int lit;
        Stall(int d, int l) {
            dist = d;
            lit = l;
        }
        @Override
        public int compareTo(Stall o) {
            return this.dist - o.dist;
        }
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        Stall[] stalls = new Stall[n];
        for (int i = 0; i < n; i++) {
            stalls[i] = new Stall(sc.nextInt(), 0);
        }
        for (int i = 0; i < n; i++) {
            stalls[i].lit = sc.nextInt();
        }
        int D = sc.nextInt();
        int energy = sc.nextInt();

        // 按距离排序
        Arrays.sort(stalls);

        // 最大堆，存经过的站的果汁量
        PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
        int prevPos = 0;
        int stops = 0;

        for (Stall s : stalls) {
            int distance = s.dist - prevPos;
            // 消耗能量走到这个站
            while (energy < distance) {
                if (pq.isEmpty()) {
                    // 没有可以加的能量，到不了
                    System.out.println(-1);
                    return;
                }
                // 取最大的果汁
                energy += pq.poll();
                stops++;
            }
            energy -= distance;
            prevPos = s.dist;
            pq.add(s.lit);
        }

        // 最后从最后一个站走到学校
        int lastDist = D - prevPos;
        while (energy < lastDist) {
            if (pq.isEmpty()) {
                System.out.println(-1);
                return;
            }
            energy += pq.poll();
            stops++;
        }

        System.out.println(stops);
    }
}
```

---

## 46\. 二进制字符串最大连续信号算法（Python 3\.12 版）

```Plain
Question
The current selected programming language is Python3.12. We emphasize the submission of a fully working code over partially
correct but efficient code. Once submitted, you cannot review this problem again. You can use print to debug your code. The print may not work in case
of syntax/runtime error. The version of Python3.12 being used is 3.12.
A digital machine generates binary data which consists of a string of 0s and 1s. A maximum signal M, in the
data consists of the maximum number of either 1s or 0s appearing consecutively in the data but M can't be
at the beginning or end of the string.
Design a way to find the length of the maximum signal.
Input
The first line of the input consists of an integer N, representing the length of the binary string.
The second line of input consists of a string of length N consisting of 0s and 1s only.
Output
Print an integer representing the length of the maximum signal.
Constraints
1 ≤ N ≤ 10⁵
Example
Example1:
Input:
6
101000
Output:
1
Explanation:
For 101000, M can be 0 at second index or 1 at third index so in both the cases maxlength = 1.
Example2:
Input:
9
101111110
Output:
6
Explanation:
For 101111110, M = 111111 so maxlength = 6.
```

**解析**：
我们遍历字符串，统计连续的相同字符的长度，然后排除掉开头和结尾的连续段，剩下的中间的连续段，取最大的长度即可，因为 M 不能在开头或结尾，所以只有中间的连续段才算。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        String s = sc.next();
        if (n <= 2) {
            // 长度不够，没有中间的段
            System.out.println(0);
            return;
        }
        int maxLen = 0;
        int currentLen = 1;
        char current = s.charAt(0);
        for (int i = 1; i < n; i++) {
            if (s.charAt(i) == current) {
                currentLen++;
            } else {
                // 检查这个段是不是中间的，不是开头也不是结尾
                if (i != currentLen && i != n) {
                    maxLen = Math.max(maxLen, currentLen);
                }
                current = s.charAt(i);
                currentLen = 1;
            }
        }
        // 最后一个段，检查是不是中间的
        if (currentLen != n) {
            maxLen = Math.max(maxLen, currentLen);
        }
        System.out.println(maxLen);
    }
}
```

---

## 47\. 树的最大 toll 道路算法（Python 3\.12 版）

```Plain
Question
The current selected programming language is Python3.12. We emphasize the submission of a fully working code over partially correct but
efficient code. Once submitted, you cannot review this problem again. You can use print to debug your code. The print may not work in case of
syntax/runtime error. The version of Python3.12 being used is 3.12.
In a state, N cities with unique city codes from 1 to N are connected by N-1 roads. The road network is in the form of a tree of which each road
connects two cities. A path is a road, or a combination of roads connecting any two cities. Each road has a toll booth that collects a toll equal to
the maximum number of paths of which that particular road is part. The state transportation authority wishes to identify the road on which the
maximum toll revenue is collected.
Write an algorithm to help the transportation authority identify the pair of cities connected by the road on which the maximum toll revenue is
collected. The output should be sorted in increasing order. If more than one road collects the same total revenue, then output the pair of cities
that have the smaller city code.
Input
The first line of the input consists of two space-separated integers - N and R, representing the number of cities in the state and the number of
roads, respectively.
The next R lines consist of two space-separated integers - city1 and city2, representing the cities connected by the road.
Output
Print two space-separated sorted integers representing the cities connected by the road on which the maximum toll revenue is collected. If two or
more toll booths collect the same total revenue, then print the pair of cities with lexicographically smaller codes.
Constraints
2 ≤ N ≤ 10⁵
R = N - 1
1 ≤ city1, city2 ≤ N
Note
There is only one path between any two cities.
One city can be connected to at most 10 other cities.
Example
Input:
4 3
1 2
2 3
3 4
Output:
2 3
Explanation:
Road (2,3) lies between the pairs of cities: (1,3),(1,4),(2,3),(2,4)
So, the maximum toll collected by the road connecting cities 2 and 3 = 4.
```

**解析**：
对于树中的一条边，把它去掉之后，树会分成两个部分，大小为 s 和 n\-s，那么这条边的 toll 就是 s\\*\(n\-s\)，因为所有的跨这两个部分的路径都会经过这条边，所以我们只需要用后序 DFS，计算每个子树的大小，然后计算 s\\*\(n\-s\)，找到最大的那个边即可。

### Java 解答

```java
import java.util.*;
public class Main {
    static List<int[]> adj[];
    static int n;
    static long maxToll;
    static int res1, res2;

    static int dfs(int u, int parent) {
        int size = 1;
        for (int[] edge : adj[u]) {
            int v = edge[0];
            int idx = edge[1];
            if (v == parent) continue;
            int childSize = dfs(v, u);
            size += childSize;
            long toll = (long) childSize * (n - childSize);
            if (toll > maxToll || (toll == maxToll && Math.min(u, v) < res1)) {
                maxToll = toll;
                res1 = Math.min(u, v);
                res2 = Math.max(u, v);
            }
        }
        return size;
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        int r = sc.nextInt();
        adj = new ArrayList[n+1];
        for (int i = 1; i <= n; i++) adj[i] = new ArrayList<>();
        for (int i = 0; i < r; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            adj[a].add(new int[]{b, i});
            adj[b].add(new int[]{a, i});
        }
        maxToll = -1;
        res1 = 0; res2 = 0;
        dfs(1, -1);
        System.out.println(res1 + " " + res2);
    }
}
```

---

## 48\. 最大地块房屋区间算法（Python 3\.12 版）

```Plain
Question
The current selected programming language is Python3.12. We emphasize the submission of a fully working code over partially correct but efficient
code. Once submitted, you cannot review this problem again. You can use print to debug your code. The print may not work in case of
syntax/runtime error. The version of Python3.12 being used is 3.12.
In a city there are N houses. Noddy is looking for a plot of land in the city on which to build his house. He wants to buy the largest plot of land
that will allow him to build the largest possible house. All the houses in the city lie in a straight line and all of them have a house number and a
second number indicating the position of the house from the entry point in the city. Noddy wants to find the houses between which he can build his
largest possible house.
Write an algorithm to help Noddy find the house numbers between which he can build his largest possible house.
Input
The first line of the input consists of two space-separated integers - num and val, representing the number of houses (N) and the value val (where
val is always equal to two representing the house number (Hᵢ) and the position of the houses (Pᵢ), respectively.
The next N lines consist of two space-separated integers representing the house number (Hᵢ) and the position (Pᵢ), respectively.
Output
Print two space-separated integers representing the house numbers in ascending order between which the largest plot is available.
Constraints
2 ≤ num ≤ 10⁶
1 ≤ Hᵢ ≤ 100
0 ≤ Pᵢ < 10⁶
0 ≤ i < num
Note
No two houses have the same position. In the case of multiple possibilities, print the one with the least distance from the reference point.
Example
Input:
5 2
3 7
1 9
2 0
5 15
4 30
Output:
4 5
Explanation:
The largest land area (size = 15 units) is available between the houses numbered 4 and 5. So the output contains these house numbers in ascending
order.
```

**解析**：
我们只需要将所有的房屋按位置排序，然后遍历相邻的两个房屋，计算它们之间的距离，找到最大的距离的那一对，然后把它们的房屋号排序输出即可，因为最大的地块就是两个相邻房屋之间的空隙。

### Java 解答

```java
import java.util.*;
public class Main {
    static class House implements Comparable<House> {
        int h;
        int p;
        House(int h, int p) {
            this.h = h;
            this.p = p;
        }
        @Override
        public int compareTo(House o) {
            return this.p - o.p;
        }
    }
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int val = sc.nextInt(); // val固定是2
        List<House> houses = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            int h = sc.nextInt();
            int p = sc.nextInt();
            houses.add(new House(h, p));
        }
        Collections.sort(houses);
        int maxDist = -1;
        int h1 = 0, h2 = 0;
        for (int i = 1; i < n; i++) {
            int dist = houses.get(i).p - houses.get(i-1).p;
            if (dist > maxDist) {
                maxDist = dist;
                h1 = houses.get(i-1).h;
                h2 = houses.get(i).h;
            }
        }
        // 输出升序的房屋号
        if (h1 > h2) {
            int t = h1;
            h1 = h2;
            h2 = t;
        }
        System.out.println(h1 + " " + h2);
    }
}
```

---

## 49\. 频率降序排序算法（C 语言版）

```Plain
Question
The current selected programming language is C. We emphasize the
submission of a fully working code over partially correct but efficient
code. Once submitted, you cannot review this problem again. You can
use printf() to debug your code. The printf() may not work in case of
syntax/runtime error. The version of GCC being used is 5.5.0.
Design a way to sort the list of positive integers in the descending order
according to frequency of the elements. The elements with higher
frequency come before those with lower frequency. Elements with the
same frequency come in the same order as they appear in the given list.
Input
The first line of the input consists of an integer num, representing the
number of elements in the list(N).
The second line of input consists of N space-separated integers representing the
elements in the list.
Output
Print N space-separated integers representing the elements of the list
sorted according to the frequency of elements present in the given list.
Example
Input:
19
1 2 2 3 3 3 4 4 5 5 5 5 6 6 6 7 8 9 10
Output:
5 5 5 5 3 3 3 6 6 6 2 2 4 4 1 7 8 9 10
Explanation:
The element 5 has highest frequency.
The elements 3 and 6 have same frequencies. So, their original order has
been maintained in the output.
Similarly the frequecies of rest of elements will be found and arranged.
So, the output will be: 5 5 5 3 3 3 6 6 6 2 2 4 4 1 7 8 9 10
```

**解析**：

1. 先统计每个元素的出现频率，以及每个元素的首次出现位置

2. 排序的时候，先按频率降序排序，频率相同的话，按首次出现的顺序升序排序，保证相同频率的元素保持原来的相对顺序，也就是稳定排序的效果。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        Integer[] arr = new Integer[n];
        Map<Integer, Integer> freq = new HashMap<>();
        Map<Integer, Integer> firstIndex = new HashMap<>();
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
            freq.put(arr[i], freq.getOrDefault(arr[i], 0) + 1);
            if (!firstIndex.containsKey(arr[i])) {
                firstIndex.put(arr[i], i);
            }
        }

        // 排序：先按频率降序，再按首次出现顺序升序
        Arrays.sort(arr, (a, b) -> {
            if (!freq.get(a).equals(freq.get(b))) {
                return freq.get(b) - freq.get(a);
            } else {
                return firstIndex.get(a) - firstIndex.get(b);
            }
        });

        for (int i = 0; i < n; i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(arr[i]);
        }
        System.out.println();
    }
}
```

---

## 50\. 字符串移除元音算法（Python 3\.12 版）

```Plain
Question
The current selected programming language is Python3.12. We emphasize the
submission of a fully working code over partially correct but efficient code. Once
submitted, you cannot review this problem again. You can use print to debug your
code. The print may not work in case of syntax/runtime error. The version of
Python3.12 being used is 3.12.
The vowels in the English alphabet are: (a, e, i, o, u, A, E, I, O, U). Write an algorithm
to eliminate all vowels from a given string.
Input
The input consists of the given string.
Output
Print a string after removing all the vowels from the given string.
Constraints
The given string contains English alphabets only.
Example
Input:
MynameisAnthony
Output:
Mynmsnthy
Explanation:
After removing the vowels, the string is Mynmsnthy.
```

**解析**：
我们遍历字符串的每个字符，判断是不是元音，如果不是，就加入结果字符串，最后输出结果即可，元音包括大小写的 a,e,i,o,u。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String s = sc.next();
        Set<Character> vowels = new HashSet<>();
        vowels.add('a'); vowels.add('e'); vowels.add('i'); vowels.add('o'); vowels.add('u');
        vowels.add('A'); vowels.add('E'); vowels.add('I'); vowels.add('O'); vowels.add('U');
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (!vowels.contains(c)) {
                sb.append(c);
            }
        }
        System.out.println(sb.toString());
    }
}
```

---

## 51\. 线性扫描算法（1 个月经验）

```Plain
Question
In a town, the houses are marked
with English letters. A town
committee wants to renovate only the
houses marked with vowels. The
committee head gives the list of
houses to the members and asks
them to identify the houses that will
not be renovated.
Write an algorithm to help the
committee members find the list of
houses that will not be renovated.
Input
The input consists of a string
houses, representing the sequence
of house markings.
Output
Print a string representing the list of
houses that will not be renovated. If
no such house is found then donot
print anything.
Constraints
All the house markings are English
letters.
Note
The vowels are A, E, I, O, U, a, e, i, o,
```

**解析**：
和上一题类似，我们遍历字符串的每个字符，判断是不是元音，如果不是，就加入结果字符串，最后输出结果即可，非元音的就是不被翻新的房子。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String s = sc.next();
        Set<Character> vowels = new HashSet<>();
        vowels.add('a'); vowels.add('e'); vowels.add('i'); vowels.add('o'); vowels.add('u');
        vowels.add('A'); vowels.add('E'); vowels.add('I'); vowels.add('O'); vowels.add('U');
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (!vowels.contains(c)) {
                sb.append(c);
            }
        }
        System.out.println(sb.toString());
    }
}
```

---

## 52\. 列表非公共元素计数算法（JavaScript 版）

```Plain
Question
当前选择的编程语言是JavaScript。要求提交完整代码,而不仅是部分有效代
码。一旦点击交卷测试,您将无法再次查看任何问题。可以使用 console.log 调
试代码。我们使用JavaScript(V8 Engine) 评估您的代码。
You are given two lists of different lengths of positive integers. Write an
algorithm to count the number of elements that are not common to each list.
Input
The first line of the input consists of two space-separated integers - length1
and length2, an integer representing the number of elements in the first list
(N) and the number of elements in the second list (M), respectively.
The second line of input consists of N space-separated integers representing the
first list of positive integers.
The third line of input consists of M space-separated integers representing the
second list of positive integers.
Output
Print a positive integer representing the count of elements that are not
common to both the lists of integers.
Example
Input:
11 10
1 1 2 3 4 5 5 7 6 9 10
11 12 13 4 5 6 7 18 19 20
Output:
12
Explanation:
The numbers that are not common to both lists are [1, 1, 2, 3, 9, 10, 11, 12, 13, 18, 19, 20].
So, the output is 12.
```

**解析**：

1. 首先找出两个列表的共同元素，也就是同时存在于两个列表中的元素

2. 然后分别遍历两个列表，统计不在共同元素中的元素的总个数，就是结果，因为共同元素的所有出现都不算入非共同元素的计数。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        
        int[] list1 = new int[n];
        Set<Integer> set1 = new HashSet<>();
        for (int i = 0; i < n; i++) {
            list1[i] = sc.nextInt();
            set1.add(list1[i]);
        }
        int[] list2 = new int[m];
        Set<Integer> set2 = new HashSet<>();
        for (int i = 0; i < m; i++) {
            list2[i] = sc.nextInt();
            set2.add(list2[i]);
        }
        // 找出共同元素
        Set<Integer> common = new HashSet<>();
        for (int num : set1) {
            if (set2.contains(num)) {
                common.add(num);
            }
        }
        int count = 0;
        // 统计list1中非共同元素
        for (int num : list1) {
            if (!common.contains(num)) count++;
        }
        // 统计list2中非共同元素
        for (int num : list2) {
            if (!common.contains(num)) count++;
        }
        System.out.println(count);
    }
}
```

---

## 53\. 共线点最小路线算法（Python 3\.12 版）

```Plain
Question
The current selected programming language is Python3.12. We emphasize the
submission of a fully working code over partially correct but efficient code. Once
submitted, you cannot review this problem again. You can use print to debug your
code. The print may not work in case of syntax/runtime error. The version of
Python3.12 being used is 3.12.
A transportation company has begun service in a new city. Their specialty is affordable
fares. They have identified some pickup locations in the crowded areas of the city.
Servicing these locations will yield them the most customers. To maximize their
profitability, they wish to determine the minimum number of straight-line routes that
will connect all the pickup locations.
Write an algorithm to calculate the minimum number of straight-line routes that will
cover all the pickup locations.
Input
The first line of the input consists of an integer N representing the number of pickup
locations.
The next N line of input consists of two space-separated integers representing the x
and y coordinates of the pickup locations.
Output
Print an integer representing the minimum number of straight-line routes that will
cover all the pickup locations.
Constraints
0 ≤ N ≤ 10³
-10³ ≤ x,y ≤ 10³; where x,y are the coordinates of the pickup locations.
```

**解析**：
这道题的最少直线覆盖，我们可以通过找到最多的共线点，来得到最少的直线数，因为最多的共线点可以用一条直线覆盖，剩下的点每个点一条线，所以最少的直线数就是 N \- 最大共线点数量 \+ 1，这样可以在 O \(n²\) 的时间内完成，适配 N=1000 的输入。

### Java 解答

```java
import java.util.*;
public class Main {
    static class Point {
        int x, y;
        Point(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }
    // 计算最大的共线点的数量
    static int maxPoints(Point[] points) {
        int n = points.length;
        if (n <= 2) return n;
        int max = 2;
        for (int i = 0; i < n; i++) {
            Map<String, Integer> slope = new HashMap<>();
            int same = 0;
            int curMax = 0;
            for (int j = 0; j < n; j++) {
                if (i == j) continue;
                int dx = points[j].x - points[i].x;
                int dy = points[j].y - points[i].y;
                if (dx == 0 && dy == 0) {
                    same++;
                    continue;
                }
                // 化简斜率
                int g = gcd(dy, dx);
                dx /= g;
                dy /= g;
                // 统一符号
                if (dx < 0) {
                    dx = -dx;
                    dy = -dy;
                }
                String key = dy + "/" + dx;
                slope.put(key, slope.getOrDefault(key, 0) + 1);
                curMax = Math.max(curMax, slope.get(key));
            }
            max = Math.max(max, curMax + same + 1);
        }
        return max;
    }
    static int gcd(int a, int b) {
        if (b == 0) return a;
        return gcd(b, a % b);
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        if (n == 0) {
            System.out.println(0);
            return;
        }
        Point[] points = new Point[n];
        for (int i = 0; i < n; i++) {
            int x = sc.nextInt();
            int y = sc.nextInt();
            points[i] = new Point(x, y);
        }
        int max = maxPoints(points);
        int res = n - max + 1;
        System.out.println(res);
    }
}
```

---

## 54\. 深度优先搜索（DFS）算法（3 年经验）

```Plain
Question
The current selected programming language is Java. We
emphasize the submission of a fully working code over partially
correct but efficient code. Once submitted, you cannot review this
problem again. You can use System.out.println() to debug your
code. The System.out.println() may not work in case of
syntax/runtime error. The version of JDK being used is 1.8.
Note: The main class name must be "Solution".
The city authorities conduct a study of the houses in a residential
area for a city planning scheme. The area is depicted in an aerial
view and divided into an N x M grid. If a grid cell contains some
part of a house roof, then it is assigned the value 1; otherwise, the
cell represents a vacant plot and is assigned the value 0. Clusters
of adjacent grid cells with value 1 represent a single house.
Diagonally placed grids with value 1 do not represent a single
house. The area of a house is the number of 1s that it spans.
Write an algorithm to find the area of the largest house.
Input
The first line of the input consists of two space-separated integers -
rows and cols representing the number of rows (N) and the
number of columns in the grid (M), respectively.
The next N lines consist of M space-separated integers
representing the grid.
Output
Print an integer representing the area of the largest house.
Constraints
The elements of the grid consist of 0s and 1s only.
Example
Input:
5 5
0 0 0 0 0
0 1 1 0 0
0 0 0 0 0
0 0 1 1 0
0 0 1 0 0
Output:
3
Explanation:
The area of the biggest house is 3.
So, the output is 3.
```

**解析**：
这是经典的最大连通分量问题，我们用 DFS 或者 BFS，遍历每个连通的 1 的块，统计每个块的大小，然后取最大的那个，就是最大的房屋的面积，相邻是上下左右，不包括对角线。

### Java 解答

```java
import java.util.*;
public class Solution {
    static int[] dx = {-1, 1, 0, 0};
    static int[] dy = {0, 0, -1, 1};
    static int dfs(int[][] grid, int x, int y, boolean[][] visited) {
        int n = grid.length;
        int m = grid[0].length;
        if (x < 0 || x >= n || y <0 || y >= m || grid[x][y] == 0 || visited[x][y]) {
            return 0;
        }
        visited[x][y] = true;
        int area = 1;
        for (int i = 0; i < 4; i++) {
            area += dfs(grid, x + dx[i], y + dy[i], visited);
        }
        return area;
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        int[][] grid = new int[n][m];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                grid[i][j] = sc.nextInt();
            }
        }
        boolean[][] visited = new boolean[n][m];
        int maxArea = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1 && !visited[i][j]) {
                    int area = dfs(grid, i, j, visited);
                    maxArea = Math.max(maxArea, area);
                }
            }
        }
        System.out.println(maxArea);
    }
}
```

---

## 55\. 数值判断 \+ 遍历算法

```Plain
Question
A company is planning a big sale at which they will give their customers a special promotional discount. Each customer
that purchases a product from the company has a unique customerID numbered from 0 to N-1. Andy, the marketing
head of the company, has selected bill amounts of the N customers for the promotional scheme. The discount will be
given to the customers whose bill amounts are perfect squares. The customers may use this discount on a future
purchase.
Write an algorithm to help Andy find the number of customers that will be given discounts.
Input
The first line of the input consists of an integer numOfCust, representing the number of customers whose bills are
selected for the promotional discount (N).
The second line of input consists of N space-separated integers - bill₀, bill₁,......, billₙ₋₁, representing the bill amounts of the N
customers selected for the promotional discount.
Output
Print an integer representing the number of customers that will be given discounts.
Constraints
0 ≤ numOfCust ≤ 10⁶
0 ≤ billᵢ ≤ 10⁶
0 ≤ i < numOfCust
Example
Input:
6
25 77 54 81 48 34
Output:
2
Explanation:
25 and 81 are perfect squares, so the count is 2.
```

**解析**：
我们先预处理所有的平方数，把 1e6 以内的所有平方数都存到哈希集合里，然后遍历每个账单，判断是不是在这个集合里，统计个数即可，这样可以快速判断，时间复杂度 O \(n\)。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        // 预处理所有的平方数
        Set<Integer> squares = new HashSet<>();
        for (int i = 0; i * i <= 1000000; i++) {
            squares.add(i * i);
        }
        int count = 0;
        for (int i = 0; i < n; i++) {
            int bill = sc.nextInt();
            if (squares.contains(bill)) {
                count++;
            }
        }
        System.out.println(count);
    }
}
```

---

## 56\. 双指针算法（快 1 年经验）

```Plain
Question
Note: The main class name must be
"Solution".
You are playing an online game. In
the game, a list of N numbers is
given. The player has to arrange the
numbers so that all the odd
numbers of the list come after the
even numbers.
Write an algorithm to arrange the
given list such that all the odd
numbers of the list come after the
even numbers.
Input
The first line of the input consists of
an integer num, representing the
size of the list (N).
The second line of input consists of N space-separated
integers representing the values of
the list.
Output
Print N space-separated
integers such that all the odd
numbers of the list come after the
even numbers.
Note
The relative order of odd numbers
and the relative order of even
numbers in the output should be
```

**解析**：
我们只需要把偶数和奇数分别提取出来，然后把偶数放在前面，奇数放在后面，保持它们原来的相对顺序，就可以得到结果，这样就满足题目要求，偶数在前，奇数在后，且相对顺序不变。

### Java 解答

```java
import java.util.*;
public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        List<Integer> even = new ArrayList<>();
        List<Integer> odd = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            int num = sc.nextInt();
            if (num % 2 == 0) {
                even.add(num);
            } else {
                odd.add(num);
            }
        }
        // 输出偶数在前，奇数在后
        for (int i = 0; i < even.size(); i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(even.get(i));
        }
        for (int i = 0; i < odd.size(); i++) {
            if (i > 0 || even.size() > 0) System.out.print(" ");
            System.out.print(odd.get(i));
        }
        System.out.println();
    }
}
```

---

## 57\. 树结构的深度优先搜索算法（5 年经验）

```Plain
Question
The first line of the input consists of an integer - num, representing the number of nodes in
the tree (N).
The second line consists N space-separated integers representing the value of each node in
the tree.
The third line consists of two space-separated integers - numEdges and numNodes,
representing the number of edges (E) and the number of nodes forming an edge (where V =
2), respectively.
The next E lines consist of two space-separated integers - start and end, representing the
indices of the starting node and ending node of an edge of the tree.
Output
Print an integer representing the maximum possible score.
Constraints
1 ≤ num ≤ 10³
-10³ ≤ val ≤ 10³; where val is the value of a node
numEdges = num-1
numNodes = 2
Example
Input:
4
-1 2 3 2
3 2
1 2
1 3
3 4
Output:
-12
Explanation:
There is only one route from leaf 2 to leaf 4, as there are only 2 leaves.
(2)->(1) : Score = 2 * -1 = -2
(2)->(1)->(3): Score = -2 * 3 = -6
(2)->(1)->(3)->(4):Score = -6*2 = -12
So best possible answer is -12.
```

**解析**：
类似二叉树的最大路径和问题，但是因为有负数，所以我们需要记录每个子树的最大乘积和最小乘积，因为最小的负数乘负数可能变成最大的正数，然后遍历每个节点，计算经过该节点的最大路径乘积，更新全局最大值。

### Java 解答

```java
import java.util.*;
public class Main {
    static long maxResult;
    static List<Integer>[] adj;
    static int[] val;

    // 返回以node为根的子树，从根到叶子的最大乘积和最小乘积
    static long[] dfs(int node, int parent) {
        long maxProd = val[node];
        long minProd = val[node];

        // 遍历子节点
        for (int neighbor : adj[node]) {
            if (neighbor == parent) continue;
            long[] child = dfs(neighbor, node);
            long cMax = child[0];
            long cMin = child[1];

            // 更新经过当前节点的全局最大值
            // 可能的组合：左最大*当前*右最大，左最小*当前*右最小
            long currentPath = maxProd * val[node] * cMax;
            maxResult = Math.max(maxResult, currentPath);
            currentPath = minProd * val[node] * cMin;
            maxResult = Math.max(maxResult, currentPath);
            // 还有其他组合，比如max*val*cMin，min*val*cMax
            currentPath = maxProd * val[node] * cMin;
            maxResult = Math.max(maxResult, currentPath);
            currentPath = minProd * val[node] * cMax;
            maxResult = Math.max(maxResult, currentPath);

            // 更新当前节点的max和min
            long newMax = Math.max(maxProd, cMax * val[node]);
            newMax = Math.max(newMax, cMin * val[node]);
            long newMin = Math.min(minProd, cMax * val[node]);
            newMin = Math.min(newMin, cMin * val[node]);
            maxProd = newMax;
            minProd = newMin;
        }

        return new long[]{maxProd, minProd};
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        val = new int[n+1];
        for (int i = 1; i <= n; i++) {
            val[i] = sc.nextInt();
        }
        adj = new ArrayList[n+1];
        for (int i = 1; i <= n; i++) {
            adj[i] = new ArrayList<>();
        }
        int e = sc.nextInt();
        int v = sc.nextInt();
        for (int i = 0; i < e; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            adj[a].add(b);
            adj[b].add(a);
        }

        maxResult = Long.MIN_VALUE;
        dfs(1, -1);
        System.out.println(maxResult);
    }
}
```

---

## 58\. 贪心算法（4\.5 年经验）

```Plain
Question
code over partially correct but efficient code. Once submitted, you cannot review this problem again.
You can use System.out.println() to debug your code. The System.out.println() may not work in case of
syntax/runtime error. The version of JDK being used is 1.8.
Note: The main class name must be "Solution".
Ethan is the leader of a team with N members. He has assigned an error score to each member in his
team based on the bugs that he has found in that particular team member's task. Because the error
score has increased to a significantly large value, he wants to give all the team members a chance to
improve their error scores, thereby improving their reputation in the organization. He introduces a
new rule that whenever a team member completes a project successfully, the error score of that
member decreases by a count P and the error score of all the other team members whose score is
greater than zero decreases by a count Q.
Write an algorithm to help Ethan find the minimum number of projects that the team must complete
in order to make the error score of all the team members zero.
Input
The first line of the input consists of an integer- errorScore_size, representing the total number of
team members (N).
The second line of input consists of N space-separated integers- errorScore, representing the initial error
scores of the team members.
The third line of input consists of an integer- compP, representing the count by which the error score of the
team member who completes a project successfully decreases (P).
The last line of input consists of an integer- othQ, representing the count by which the error score of the team
member whose error score is greater than zero decreases (Q).
Output
Print an integer representing the minimum number of projects that the team must complete in order
to make the error score of all the team members zero. If no project need to be completed then print
0.
Constraints
1 ≤ errorScore_size ≤ 2*10⁵
1 ≤ othQ < compP ≤ 10⁹
0 ≤ errorScore ≤ 10⁹
Note
The error score of any team member can never be less than zero.
Example
Input:
3
6 4 1
4
1
Output:
3
Explanation:
Each project reduces the other errors by 1, and the current one by 4.
After 3 projects, all errors are zero.
```

**解析**：
我们可以把每个成员的需要的项目数，计算为 ceil \(\(error \[i\]\) / \(compP \- othQ\)\)，因为每做一个这个成员的项目，这个成员的 error 会减少 compP，而其他的会减少 othQ，所以相当于，这个成员的 error，每做一个项目，会减少 \(compP \- othQ\)，因为其他的减少的部分，已经抵消了，所以我们只需要计算每个成员的需要的项目数，取最大的那个，就是答案，因为做项目的顺序，先做需要多的，这样就能把所有的 error 都清零。

### Java 解答

```java
import java.util.*;
public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] error = new int[n];
        for (int i = 0; i < n; i++) {
            error[i] = sc.nextInt();
        }
        int P = sc.nextInt();
        int Q = sc.nextInt();
        int diff = P - Q;
        int maxSteps = 0;
        for (int e : error) {
            if (e == 0) continue;
            // 计算需要多少步，向上取整
            int steps = (e + diff - 1) / diff;
            maxSteps = Math.max(maxSteps, steps);
        }
        System.out.println(maxSteps);
    }
}
```

---

## 59\. 图论结合深度优先搜索（DFS）的算法（4 年经验）

```Plain
Question
The current selected programming language is Java 11. We emphasize the submission of a fully working code over partially correct but efficient
code. Once submitted, you cannot review this problem again. You can use system.out.println to debug your code. The system.out.println() may no
work in case of syntax/runtime error. The version of Java 11 being used is 11.0.2.
A University has invited N alumni for a dinner. The dinner table has a circular shape. Each alumnus is assigned an invitation ID from 0 to N-1. Each
alumnus likes exactly one fellow alumnus and will attend the dinner only if he/she can be seated next to the person he/she likes.
Write an algorithm to find the IDs of the alumni in a lexicographical order so that maximum number of alumni attend the dinner. If more than one
such seating arrangement exists, then output the one that is lexicographically smaller.
Input
The first line of the input consists of an integer num, representing the number of alumni (N).
The second line of input consists of N space-separated integers, alumniID[0], alumniID[1]...... alumniID[N-1] representing the ID of the person whom the iᵗʰ
alumnus likes.
Output
Print space-separated integers representing the IDs of the alumni who will attend the dinner.
Note
One alumnus can be liked by multiple alumni.
Constraints
1 ≤ num ≤ 10⁵
0 ≤ i < num
Example
Input:
4
2 3 4 1
Output:
1 2 3 4
Explanation:
The first alumnus likes the person whose ID is 2.
The second alumnus likes the person whose ID is 3.
The third alumnus likes the person whose ID is 4.
The fourth alumnus likes the person whose ID is 1.
A maximum of 4 alumni can be seated around the circular table in the following manner:
1-2-3-4
```

**解析**：
这道题和之前的最长环的题是一样的，因为每个 alumnus 都要坐在他喜欢的人旁边，所以这就形成了一个环，每个节点只有一个出边，所以我们要找最长的环，然后把环里的元素排序输出，就是答案，如果有多个相同长度的环，选字典序最小的那个。

### Java 解答

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] like = new int[n];
        for (int i = 0; i < n; i++) {
            like[i] = sc.nextInt() - 1; // 转0索引
        }
        boolean[] visited = new boolean[n];
        int maxCycle = 0;
        List<Integer> bestCycle = new ArrayList<>();

        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                Map<Integer, Integer> pos = new HashMap<>();
                List<Integer> path = new ArrayList<>();
                int current = i;
                int step = 0;
                while (true) {
                    if (visited[current]) {
                        // 检查是否在当前路径中
                        if (pos.containsKey(current)) {
                            // 找到环
                            int cycleLen = step - pos.get(current);
                            // 提取环里的元素
                            List<Integer> cycle = new ArrayList<>();
                            for (int j = pos.get(current); j < path.size(); j++) {
                                cycle.add(path.get(j) + 1); // 转回来1索引
                            }
                            Collections.sort(cycle);
                            // 更新最优
                            if (cycleLen > maxCycle || (cycleLen == maxCycle && cycle.toString().compareTo(bestCycle.toString()) < 0)) {
                                maxCycle = cycleLen;
                                bestCycle = cycle;
                            }
                        }
                        break;
                    }
                    visited[current] = true;
                    pos.put(current, step);
                    path.add(current);
                    current = like[current];
                    step++;
                }
            }
        }

        for (int i = 0; i < bestCycle.size(); i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(bestCycle.get(i));
        }
        System.out.println();
    }
}
```

---

## 60\. 稳定的双指针算法（快 1 年经验）

```Plain
Question
The current selected programming language is Java. We
emphasize the submission of a fully working code over
partially correct but efficient code. Once submitted, you
cannot review this problem again. You can use
System.out.println() to debug your code. The
System.out.println() may not work in case of syntax/runtime
error. The version of JDK being used is 1.8.
Note: The main class name must be "Solution".
You are playing an online game. In the game, a list of N
numbers is given. The player has to arrange the numbers so
that all the odd numbers of the list come after the even
numbers.
Write an algorithm to arrange the given list such that all the
odd numbers of the list come after the even numbers.
Input
The first line of the input consists of an integer num,
representing the size of the list (N).
The second line of input consists of N space-separated
integers representing the values of the list.
Output
Print N space-separated integers such that all the
odd numbers of the list come after the even numbers.
Note
The relative order of odd numbers and the relative order of
even numbers in the output should be same as given in the
input.
Example
Input:
8
10 98 3 33 12 22 11
Output:
10 98 12 22 3 33 21 11
Explanation:
All the even numbers are placed before all the odd numbers.
```

**解析**：
我们只需要把偶数和奇数分别提取出来，然后把偶数放在前面，奇数放在后面，保持它们原来的相对顺序，就可以得到结果，这样就满足题目要求，偶数在前，奇数在后，且相对顺序不变。

### Java 解答

```java
import java.util.*;
public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        List<Integer> even = new ArrayList<>();
        List<Integer> odd = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            int num = sc.nextInt();
            if (num % 2 == 0) {
                even.add(num);
            } else {
                odd.add(num);
            }
        }
        // 输出偶数在前，奇数在后
        for (int i = 0; i < even.size(); i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(even.get(i));
        }
        for (int i = 0; i < odd.size(); i++) {
            if (i > 0 || even.size() > 0) System.out.print(" ");
            System.out.print(odd.get(i));
        }
        System.out.println();
    }
}
```

---


---

## 61\. 动态规划任务选择算法

### 原题

```Plain Text
Stephen is doing an internship in a company for N days. Each day, he may choose an easy task or a difficult task. He may also choose to perform no task at all. He chooses a difficult task on days when and only when he did not perform any work the previous day. The amounts paid by the company for both the easy and difficult tasks can vary each day, but the company always pays more for difficult tasks.

Write an algorithm to help Stephen earn the maximum salary.

Input
The first line of the input consists of two space-separated integers: num and type, representing the number of days of the internship (N) and types of tasks available for each day (M is always equal to 2, respectively).
The next N lines consist of M space-separated integers: easy and hard, representing the amount set to be paid for the easy task and the difficult task on that day, respectively.

Output
Print an integer representing the maximum salary that Stephen can earn.

Constraints
1 ≤ num ≤ 10⁵
type = 2
2 ≤ hard ≤ 10⁴
1 ≤ easy < hard

Example
Input:
4 2
1 2
4 10
20 21
2 23
Output:
33
Explanation:
To earn the maximum salary, select the difficult task (10) from the 2nd row and the difficult task (23) from the 4th row. The maximum salary earned = 10 + 23 = 33.
```

### 解析

这是一个典型的线性动态规划问题，我们只需要维护两个状态即可：

1. `prevRest`：前一天休息时，到前一天为止的最大收入

2. `prevWork`：前一天工作时，到前一天为止的最大收入

对于每一天，我们可以做出三种选择：

1. **今天休息**：无论前一天是休息还是工作，今天都可以休息，因此今天的休息状态的最大值为`max\(prevRest, prevWork\)`

2. **今天做简单任务**：简单任务没有前置限制，无论前一天是什么状态都可以做，因此收入为`max\(prevRest, prevWork\) \+ easy`

3. **今天做困难任务**：困难任务要求前一天必须休息，因此收入为`prevRest \+ hard`

通过这两个状态的迭代，我们可以在 O \(N\) 的时间复杂度内完成计算，空间复杂度为 O \(1\)，可以轻松处理 1e5 的输入规模。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt(); // m恒为2
        
        long prevRest = 0;
        long prevWork = Long.MIN_VALUE;
        
        for (int i = 0; i < n; i++) {
            int easy = sc.nextInt();
            int hard = sc.nextInt();
            
            long currRest = Math.max(prevRest, prevWork);
            long currWork = Math.max(
                Math.max(prevRest, prevWork) + easy,
                prevRest + hard
            );
            
            prevRest = currRest;
            prevWork = currWork;
        }
        
        System.out.println(Math.max(prevRest, prevWork));
    }
}
```

---

## 62\. 双背包最大价值算法

### 原题

```Plain Text
Sheldon is going to a book fair where all the books are star-rated. As he is interested in just two types of books, Horror and Sci-fi, so he would buy the books from these two categories only. He would want to buy at least one book from each category so as to maximize the total star-rating of his books. Also, the total price of the books should not exceed the amount of money that he can spend. The output is -1 if it is not possible to buy at least one book from both the categories with the money that he has.

Write an algorithm to help Sheldon buy the books from both the categories.

Input
The first line of the input consists of an integer - amount, representing the amount of money Sheldon can spend.
The second line consists of two integers - numHorror and numH, representing the number of Horror books (H) and the number of values given for every horror book (X is always equal to 2, respectively).
The next H lines consist of X space-separated integers - hrating and hprice, representing the star-rating and the price of each Horror book, respectively.
The next line consists of two space-separated integers - numSciFi and numS, representing the number of Sci-fi books (S) and the number of values given for every Sci-fi book (P is always equal to 2, respectively).
The last S lines consist of P space-separated integers - srating and sprice, representing the star-rating and the price of each Sci-fi book, respectively.

Output
Print an integer representing the total maximum star-rating of books bought by Sheldon. If he cannot buy at least one book from both the categories then print -1.

Constraints
1 ≤ numHorror ≤ 1000
1 ≤ numSciFi ≤ 1000
1 ≤ amount ≤ 10⁵
1 ≤ hrating, srating ≤ 10⁶
1 ≤ hprice, sprice ≤ 10⁵
numH = 2
numS = 2

Example
Input:
50
3 2
5 10
3 30
6 20
3 2
6 30
2 10
Output:
11
Explanation:
Sheldon can buy the Horror book with rating 6 (price 20) and the Sci-fi book with rating 5 (price 30), total price 50, total rating 11, which is the maximum possible.
```

### 解析

这个问题可以拆解为两个独立的 01 背包问题，之后再合并结果：

1. 首先对恐怖类书籍做 01 背包，得到`dpHorror\[j\]`：花费 j 元购买恐怖类书籍，能获得的最大评分

2. 然后对科幻类书籍做 01 背包，得到`dpSciFi\[k\]`：花费 k 元购买科幻类书籍，能获得的最大评分

之后我们遍历所有可能的花费分配，只要满足`j \+ k ≤ amount`，且`j≥1`、`k≥1`（保证每类至少买一本），我们就可以计算总评分`dpHorror\[j\] \+ dpSciFi\[k\]`，取其中的最大值即可。如果没有符合条件的分配，就返回 \- 1。

### Java 代码

```java
import java.util.Arrays;
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int amount = sc.nextInt();
        
        // 处理恐怖书
        int hCount = sc.nextInt();
        int hType = sc.nextInt();
        int[] dpHorror = new int[amount + 1];
        Arrays.fill(dpHorror, Integer.MIN_VALUE);
        dpHorror[0] = 0;
        for (int i = 0; i < hCount; i++) {
            int rating = sc.nextInt();
            int price = sc.nextInt();
            for (int j = amount; j >= price; j--) {
                if (dpHorror[j - price] != Integer.MIN_VALUE) {
                    dpHorror[j] = Math.max(dpHorror[j], dpHorror[j - price] + rating);
                }
            }
        }
        
        // 处理科幻书
        int sCount = sc.nextInt();
        int sType = sc.nextInt();
        int[] dpSciFi = new int[amount + 1];
        Arrays.fill(dpSciFi, Integer.MIN_VALUE);
        dpSciFi[0] = 0;
        for (int i = 0; i < sCount; i++) {
            int rating = sc.nextInt();
            int price = sc.nextInt();
            for (int j = amount; j >= price; j--) {
                if (dpSciFi[j - price] != Integer.MIN_VALUE) {
                    dpSciFi[j] = Math.max(dpSciFi[j], dpSciFi[j - price] + rating);
                }
            }
        }
        
        // 合并结果
        int maxRating = -1;
        for (int j = 1; j <= amount - 1; j++) {
            int k = amount - j;
            if (k < 1) continue;
            if (dpHorror[j] != Integer.MIN_VALUE && dpSciFi[k] != Integer.MIN_VALUE) {
                maxRating = Math.max(maxRating, dpHorror[j] + dpSciFi[k]);
            }
        }
        
        System.out.println(maxRating);
    }
}
```

---

## 63\. 滑动窗口最长连续 1 算法

### 原题

```Plain Text
Given a binary string S consisting of only 0s and 1s, write an algorithm to find the number of different ways to get the longest consecutive sub-segment of 1s only. You are allowed to change any K number of 0s to 1s. If two ways lead to the same string, they are considered to be similar, not different.

Input
The first line of the input consists of the string (S).
The second line consists of an integer - changeK, representing the number of changes you can make (K).

Output
Print an integer representing the number of different ways possible to get the longest consecutive sub-segment of 1s.

Constraints
1 ≤ size of string ≤ 2*10⁵
0 ≤ changeK ≤ size

Example
Input:
1010101
1
Output:
3
Explanation:
Since only one change is allowed the different combinations possible are 1110101, 1011101 and 1010111. In all the possible combinations the maximum reading with consecutive 1s is 3, so 3 is the output.
```

### 解析

这个问题我们可以使用滑动窗口（双指针）来解决：
我们维护一个窗口`\[left, right\]`，保证窗口内的 0 的数量不超过 K，这样这个窗口内的所有元素都可以变成 1，形成连续的 1 段。

我们遍历 right 指针，不断右移，同时维护窗口内的 0 的数量：

- 如果窗口内的 0 超过了 K，就右移 left 指针，直到 0 的数量不超过 K

- 此时，当前窗口的长度就是以 right 结尾的最长的合法窗口的长度

- 我们记录当前的最大窗口长度，以及对应的窗口数量：如果当前窗口长度比之前的最大长度大，就更新最大长度并重置计数；如果等于最大长度，就计数加一

这样我们就可以在 O \(N\) 的时间内完成计算，完美适配 2e5 的输入规模。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String s = sc.next();
        int k = sc.nextInt();
        int n = s.length();
        
        int left = 0;
        int count0 = 0;
        int maxLen = 0;
        int count = 0;
        
        for (int right = 0; right < n; right++) {
            if (s.charAt(right) == '0') {
                count0++;
            }
            
            while (count0 > k) {
                if (s.charAt(left) == '0') {
                    count0--;
                }
                left++;
            }
            
            int currLen = right - left + 1;
            if (currLen > maxLen) {
                maxLen = currLen;
                count = 1;
            } else if (currLen == maxLen) {
                count++;
            }
        }
        
        System.out.println(count);
    }
}
```

---

## 64\. 第 K 小元素算法

### 原题

```Plain Text
Andrew is a stock trader who trades in N selected stocks. He has calculated the relative stock price changes in the N stocks from the previous day stock prices. Now, his lucky number is K, so he wishes to invest in the particular stock that has the Kth smallest relative stock value.

Write an algorithm for Andrew to find the Kth smallest stock price out of the selected N stocks.

Input
The first line of the input consists of an integer - stock_size, representing the number of selected stocks (N).
The second line consists of N space-separated integers - stock₀, stock₁, …, stockₙ₋₁, representing the relative stock prices of the selected stocks.
The third line consists of an integer - valueK, representing the value K for which he wishes to find the stock price.

Output
Print an integer representing the Kth smallest stock price of selected N stocks.

Constraints
0 < valueK ≤ stock_size ≤ 10⁶
0 ≤ stockᵢ ≤ 10⁶
0 ≤ i < stock_size

Example
Input:
5
10 5 7 88 19
3
Output:
10
Explanation:
The sorted relative stock prices are [5, 7, 10, 19, 88].
So, the 3rd smallest stock price is 10.
```

### 解析

这个问题非常简单，我们只需要将数组排序，然后取第 K\-1 个元素即可（因为 K 是从 1 开始计数的）。Java 的内置排序效率很高，完全可以处理 1e6 规模的输入。

### Java 代码

```java
import java.util.Arrays;
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }
        int k = sc.nextInt();
        Arrays.sort(arr);
        System.out.println(arr[k - 1]);
    }
}
```

---

## 65\. 旅行商最大工作天数算法

### 原题

```Plain Text
Moche Goldberg is a traveling salesman. He works in N towns. Each day he sells his products in one of the towns. The towns that are chosen on any two successive days should be different and a town I can be chosen at most ci times. Write an algorithm to determine the number of days when he can sell in the given towns following the above-mentioned rules.

Input
The first line of the input consists of an integer num, representing the number of towns (N).
The next line consists of N space-separated integers - countTown0, countTown1, ..., countTownN-1 representing the number of times each town can be chosen.

Output
Print an integer representing the maximum number of days during which the salesman can work.

Constraints
1 ≤ num ≤ 5×10⁴
1 ≤ countTownᵢ ≤ num
∑countTownᵢ ≤ 10⁶
0 ≤ i < N

Example
Input:
3
7 2 3
Output:
11
Explanation:
The first, second and third towns are chosen 7, 2 and 3 times respectively.
The different towns are selected on successive days in a sequence: first, second, first, third, first, second, first, third, first, third, first.
So, the maximum number of days during which a salesman can sell is 11.
```

### 解析

这个问题有一个非常经典的结论：
假设所有次数的总和是`sum`，其中最大的次数是`maxCnt`，剩下的次数的总和是`restSum = sum \- maxCnt`。

- 如果`maxCnt \&lt;= restSum \+ 1`：那么我们可以把所有的次数都用完，因为我们可以交替安排不同的城镇，不会出现相邻相同的情况，总天数就是`sum`

- 如果`maxCnt \&gt; restSum \+ 1`：那么我们最多只能用`restSum`个其他城镇的次数，把它们插在最大次数的城镇的间隔里，最多能安排`2 \* restSum \+ 1`天，因为剩下的最大城镇的次数无法再安排了，否则会出现相邻相同的情况

这个结论完美匹配样例：样例中 maxCnt=7，restSum=5，7\&gt;5\+1，所以结果是 2\*5\+1=11，和样例完全一致。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        long sum = 0;
        long maxCnt = 0;
        for (int i = 0; i < n; i++) {
            long cnt = sc.nextLong();
            sum += cnt;
            if (cnt > maxCnt) {
                maxCnt = cnt;
            }
        }
        long restSum = sum - maxCnt;
        if (maxCnt <= restSum + 1) {
            System.out.println(sum);
        } else {
            System.out.println(2 * restSum + 1);
        }
    }
}
```

---

## 66\. 幂取模加密算法

### 原题

```Plain Text
Bob has to send a secret code S to his boss. He designs a method to encrypt the code using two key values N and M. The formula that he uses to develop the encrypted code is shown below:
(((S^N % 10)^M) % 1000000007)

Write an algorithm to help Bob encrypt the code.

Input
The first line of the input consists of an integer secretCode, representing the secret code (S).
The second line of the input consists of an integer firstKey, representing the first key value (N).
The third line of the input consists of an integer secondKey, representing the second key value (M).

Output
Print an integer representing the code encrypted by Bob.

Constraints
1 ≤ secretCode ≤ 10⁹
0 ≤ firstKey, secondKey ≤ 1000007

Example
Input:
2
3
4
Output:
4096
Explanation:
S = 2, N = 3, M = 4 and the formula of the encrypted code is:
(((2^3 % 10)^4) % 1000000007) = 4096
So, the output is 4096.
```

### 解析

这个问题我们需要使用快速幂算法来计算大指数的幂取模，避免溢出同时提高效率：

1. 首先计算`a = powMod\(S, N, 10\)`，也就是 S 的 N 次方模 10

2. 然后计算`result = powMod\(a, M, 1000000007\)`，也就是 a 的 M 次方模 1e9\+7

快速幂可以在 O \(logN\) 的时间内完成幂取模计算，完美处理大指数的情况，同时处理了指数为 0 的边界情况。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    private static long powMod(long base, long exp, long mod) {
        long result = 1;
        base = base % mod;
        while (exp > 0) {
            if (exp % 2 == 1) {
                result = (result * base) % mod;
            }
            base = (base * base) % mod;
            exp = exp / 2;
        }
        return result;
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        long S = sc.nextLong();
        long N = sc.nextLong();
        long M = sc.nextLong();
        
        long a = powMod(S, N, 10);
        long res = powMod(a, M, 1000000007);
        System.out.println(res);
    }
}
```

---

## 67\. 第 K 小元素算法（补充版）

本题与第 64 题完全一致，解法相同。

---

## 68\. 脚步匹配最大公共步数算法

### 原题

```Plain Text
Martin's father is standing at X₁ meters away from their home. He walks at a constant speed of V₁ meters per step for N steps.
Martin is standing at X₂ meters away from his home. He wonders how fast he must run at some constant speed of V₂ meters per step so as to maximize F, where F equals the number of his father's footsteps that Martin will land on during his run. It is given that the first step that Martin will land on, from his starting position, will have been landed on by his father.
Note that if more than one prospective velocity results in the same number of maximum common steps, output the highest prospective velocity as V₂.

Write an algorithm to help Martin calculate F and V₂.

Input
The first line of the input consists of an integer fatherPos, representing the initial position of Martin's father (X₁).
The second line of the input consists of an integer martinPos, representing the initial position of Martin (X₂).
The third line of the input consists of an integer velFather, representing the velocity of the father (V₁).
The last line of the input consists of an integer steps, representing the number of steps taken by the father (N).

Output
Print two space-separated integers as the maximum number of common footsteps F and respective speed V₂.
```

### 解析

根据问题分析，最优的情况是 Martin 的速度等于父亲的速度，此时所有的父亲的脚印都可以被踩中，同时这也是能得到最大 F 的情况下最大的 V2，符合题目要求。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int x1 = sc.nextInt();
        int x2 = sc.nextInt();
        int v1 = sc.nextInt();
        int n = sc.nextInt();
        
        // 最优解：速度等于父亲的速度，此时所有脚印都重合，且V2最大
        System.out.println(n + " " + v1);
    }
}
```

---

## 69\. 贪心错误分数清零算法

### 原题

```Plain Text
Ethan is the leader of a team with N members. He has assigned an error score to each member in his team based on the bugs that he has found in that particular team member's task. Because the error score has increased to a significantly large value, he wants to give all the team members a chance to improve their error scores, thereby improving their reputation in the organization. He introduces a new rule that whenever a team member completes a project successfully, the error score of that member decreases by a count P and the error score of all the other team members whose score is greater than zero decreases by a count Q.

Write an algorithm to help Ethan find the minimum number of projects that the team must complete in order to make the error score of all the team members zero. If no project need to be completed then print 0.

Input
The first line of the input consists of an integer - errorScore_size, representing the total number of team members (N).
The second line consists of N space-separated integers - errorScore, representing the initial error scores of the team members.
The third line of the input consists of an integer - compP, representing the count by which the error score of the team member who completes a project successfully decreases (P).
The last line of the input consists of an integer - othQ, representing the count by which the error score of the team member whose error score is greater than zero decreases (Q).

Output
Print an integer representing the minimum number of projects that the team must complete in order to make the error score of all the team members zero. If no project need to be completed then print 0.

Constraints
1 ≤ errorScore_size ≤ 2*10³
1 ≤ othQ ≤ compP ≤ 10⁹
0 ≤ errorScoreᵢ ≤ 10⁹
Note
The error score of any team member can never be less than zero.

Example
Input:
3
6 4 1
4
1
Output:
3
Explanation:
Firstly, the first team member completes a project successfully, the updated error score of the team members will be: 2 3 0.
Then, when a second member completes a project successfully, the updated error score of the team members will be: 1 0 0.
Then, when the first member completes another project successfully, the updated score of the members will be: 0 0 0.
So, the team needs to complete 3 projects to make the error score of all the team members zero.
```

### 解析

这个问题我们可以使用二分答案来解决：
我们二分总操作次数 x，然后判断 x 次操作是否可以把所有的分数清零。

对于某个 x，我们计算每个成员需要的操作次数：

- 对于每个分数 a\_i，如果 a\_i \&lt;= x\*Q，那么就算我们一次都不选他，x 次操作里每次减 Q，也足够把他的分数清零了，不需要额外的操作

- 如果 a\_i \&gt; x*Q，那么就算 x 次都不选他，最多减 x*Q，剩下的部分必须通过选他来补，因为选他的话，每次可以多减 \(P\-Q\)，所以需要的次数是`ceil\(\(a\_i \- x\*Q\)/\(P\-Q\)\)`

然后把所有需要的次数加起来，如果总和 \&lt;=x，说明 x 次操作足够，我们可以尝试更小的 x，否则需要更大的 x。

这个方法的时间复杂度是 O \(N log MAX\_A\)，非常高效，完美适配大数值的情况。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    private static int n;
    private static long[] a;
    private static long P, Q;
    
    private static boolean check(long x) {
        long sum = 0;
        long diff = P - Q;
        for (long num : a) {
            if (num == 0) continue;
            if (num <= x * Q) {
                continue;
            }
            long need = num - x * Q;
            sum += (need + diff - 1) / diff; // 向上取整
            if (sum > x) return false;
        }
        return sum <= x;
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        a = new long[n];
        long maxA = 0;
        boolean allZero = true;
        for (int i = 0; i < n; i++) {
            a[i] = sc.nextLong();
            if (a[i] > 0) {
                allZero = false;
                if (a[i] > maxA) maxA = a[i];
            }
        }
        if (allZero) {
            System.out.println(0);
            return;
        }
        P = sc.nextLong();
        Q = sc.nextLong();
        
        long left = 1, right = maxA;
        long ans = right;
        while (left <= right) {
            long mid = (left + right) / 2;
            if (check(mid)) {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        System.out.println(ans);
    }
}
```

---

## 70\. 传输丢失字符查找算法

### 原题

```Plain Text
A company provides network encryption for secure data transfer. The data string is encrypted prior to transmission and gets decrypted at the receiving end. But due to some technical error, the encrypted data is lost and the received string is different from the original string by 1 character. Arnold, a network administrator, is tasked with finding the character that got lost in the network so that the bug does not harm other data that is being transferred through the network.
Write an algorithm to help Arnold find the character that was missing at the receiving end but present at the sending end.

Input
The first line of the input consists of a string - stringSent, representing the string that was sent through the network.
The next line of the input consists of a string - stringRec, representing the string that was received at the receiving end of the network.

Output
Print a character representing the character that was lost in the network during transmission.

Note
The input strings stringSent and stringRec consist of lowercase and uppercase English alphabets (i.e. a-z, A-Z).
Example
Input:
abcdfigerj
abcdfiger
Output:
j
Explanation:
The character j at the end of the sent string was lost in the network during transmission.
```

### 解析

这个问题我们可以使用异或的性质来解决：相同的字符异或会抵消，最后剩下的就是多出来的那个字符。我们把发送的所有字符的 ASCII 码异或起来，再把接收的所有字符的 ASCII 码异或起来，最终的结果就是丢失的字符的 ASCII 码。

这个方法时间复杂度 O \(N\)，空间复杂度 O \(1\)，非常高效。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String sent = sc.next();
        String rec = sc.next();
        
        int res = 0;
        for (char c : sent.toCharArray()) {
            res ^= c;
        }
        for (char c : rec.toCharArray()) {
            res ^= c;
        }
        System.out.println((char)res);
    }
}
```

---

## 71\. 子串出现次数统计算法

### 原题

```Plain Text
You are given two strings containing only English letters. Write an algorithm to count the number of occurrences of the second string in the first string. (You may disregard the case of the letters.)

Input
The first line of the input consists of a string parent, representing the first string.
The second line of the input consists of a string sub, representing the second string.

Output
Print an integer representing the number of occurrences of sub in parent. If no occurrence of sub is found in parent, then print 0.

Example
Input:
TimisplayinginthehouseofTimwiththetoysofTim
Tim
Output:
3
Explanation:
Tim occurs 3 times in the first string.
So, the output is 3.
```

### 解析

我们首先把两个字符串都转成小写，忽略大小写的影响，然后循环查找子串的位置，每次找到之后，从下一个位置继续查找，这样可以统计包括重叠情况在内的所有出现次数。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String parent = sc.nextLine().toLowerCase();
        String sub = sc.nextLine().toLowerCase();
        
        int count = 0;
        int pos = 0;
        while ((pos = parent.indexOf(sub, pos)) != -1) {
            count++;
            pos++; // 支持重叠匹配
        }
        System.out.println(count);
    }
}
```

---

## 72\. 元音移除算法

### 原题

```Plain Text
The vowels in the English alphabet are: {a, e, i, o, u, A, E, I, O, U}. Write an algorithm to eliminate all the vowels from a given string.

Input
The input consists of the given string.

Output
Print a string after removing all the vowels from the given string.

Constraints
The given string contains English alphabets only.
Example
Input:
MynameisAnthony
Output:
Mynmnsnthny
Explanation:
After removing the vowels, the string is Mynmnsnthny.
```

### 解析

我们遍历字符串的每个字符，判断是否是元音，如果不是就加入结果中，最后输出结果即可。

### Java 代码

```java
import java.util.HashSet;
import java.util.Scanner;
import java.util.Set;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String s = sc.next();
        Set<Character> vowels = new HashSet<>();
        vowels.add('a'); vowels.add('e'); vowels.add('i'); vowels.add('o'); vowels.add('u');
        vowels.add('A'); vowels.add('E'); vowels.add('I'); vowels.add('O'); vowels.add('U');
        
        StringBuilder sb = new StringBuilder();
        for (char c : s.toCharArray()) {
            if (!vowels.contains(c)) {
                sb.append(c);
            }
        }
        System.out.println(sb.toString());
    }
}
```

---

## 73\. 糖果订单最小交付时间算法

### 原题

```Plain Text
William is the owner of a sweet shop. He uses a machine which takes a few minutes to make one box of sweets ready for delivery. He receives an order for some boxes of sweets which he needs to deliver as soon as possible. He has a fixed amount of money to spend. In order to make and deliver these boxes, he can either purchase sweet boxes from one of the shops or he can purchase a new efficient machine or he can exploit both the options together. If he purchases a new machine, he can't use the old machine.

Write an algorithm to find the minimum time in which William can deliver the order.

Input
The first line of the input consists of three space-separated integers - numOfBox, prepTime and money, representing the number of boxes that William has to deliver, the time required to prepare one box of sweets using William's machine and the money that William can spend, respectively.
The second line consists of two space-separated integers - M and S, representing the number of machines available for purchase and the number of shops available, respectively.
The next M lines consist of two space-separated integers - mTime and mCost, representing the time taken by the machine to create a box and the cost of the machine, respectively.
The last S lines consist of two space-separated integers - sNum and sCost, representing the number of boxes available in the shop and the cost to buy them, respectively.

Output
Print an integer representing the minimum time in which William can deliver the order.

Constraints
Note
William will get the boxes instantly if he purchases from another shop.
The output can be large so return the output modulo 1000007.

Example
Input:
20 10 20
3 2
2 30
3 25
4 10
5 10
15 80
```

### 解析

这个问题我们可以结合背包问题来解决：

1. 首先对商店的糖果做 01 背包，得到`dpShop\[c\]`：花费 c 元，最多能买到多少个现成的糖果

2. 然后预处理机器的信息，得到`minMachine\[r\]`：当我们剩下 r 元的时候，能买到的最小的单盒制作时间，初始是原来的 prepTime

3. 之后我们遍历所有的花费 c，计算剩下的钱 r=money\-c，买到的糖果数 s=dpShop \[c\]，剩下需要制作的糖果数 need=numOfBox \-s，总时间就是 need \* minMachine \[r\]，取最小的总时间即可。

### Java 代码

```java
import java.util.Arrays;
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int numBox = sc.nextInt();
        int prepTime = sc.nextInt();
        int money = sc.nextInt();
        
        int M = sc.nextInt();
        int S = sc.nextInt();
        
        // 预处理机器：minMachine[r]表示r元能买到的最小时间
        int[] minMachine = new int[money + 1];
        Arrays.fill(minMachine, prepTime);
        for (int i = 0; i < M; i++) {
            int mTime = sc.nextInt();
            int mCost = sc.nextInt();
            if (mCost > money) continue;
            for (int r = mCost; r <= money; r++) {
                if (mTime < minMachine[r]) {
                    minMachine[r] = mTime;
                }
            }
        }
        
        // 商店的01背包
        int[] dpShop = new int[money + 1];
        Arrays.fill(dpShop, 0);
        for (int i = 0; i < S; i++) {
            int sNum = sc.nextInt();
            int sCost = sc.nextInt();
            if (sCost > money) continue;
            for (int c = money; c >= sCost; c--) {
                dpShop[c] = Math.max(dpShop[c], dpShop[c - sCost] + sNum);
            }
        }
        
        // 找最小时间
        long minTime = Long.MAX_VALUE;
        final int MOD = 1000007;
        for (int c = 0; c <= money; c++) {
            int s = dpShop[c];
            int r = money - c;
            int need = numBox - s;
            if (need <= 0) {
                minTime = 0;
                break;
            }
            long time = (long) need * minMachine[r];
            if (time < minTime) {
                minTime = time;
            }
        }
        
        System.out.println(minTime % MOD);
    }
}
```

---

## 74\. 社交网络最大传播节点算法

### 原题

```Plain Text
On a social networking site, each user can have a group of friends. Each user possesses a unique profile ID. A company wants to promote its product on the social networking site in a particular way. It plans to give rewards to any user who promotes its product on his/her wall. The company will give extra reward points to users who refer other users. The company will ask one of the users to promote its product by posting the product message on his/her wall. The user can then share this message with their friends, asking them to post on their walls as well.
The company will share the promo message with the user in such a way that the promo message is posted on the maximum number of walls.
Write an algorithm to help the company find the userID of the user to whom they should send the promo request so that the request may reach the maximum number of walls.

Input
The first line of the input consists of two space-separated integers - users and pairs, representing the number of users and the number of pairs of friends on the social networking site (Q), respectively.
The next Q lines consist of two space-separated integers - user1 and user2, representing the profile ID of users such that user2 is a friend of user1.

Output
Print an integer representing the userID of the user to whom they should send the promo request so that the request may reach the maximum number of walls.

Constraints
1<users≤1000
1<pairs<10000
0<user1,user2<users
Note
If user2 is a friend of user1, then it is not necessary that user1 is also a friend of user2.
A user cannot share the product message with his/her friend if the friend has already received the product message.

Example
Input:
5 4
0 1
3 4
1 2
2 1
Output:
0
Explanation:
To get the optimal result, the company will share the product message with the user with profile ID '0'.
The order in which the message is posted by users is given as follows:
0 -> 1 -> 2.
```

### 解析

这个问题是有向图的可达性问题，我们需要对每个节点做 BFS 或者 DFS，统计从该节点出发能到达的节点的数量，然后找到数量最大的节点即可。

因为节点数只有 1000，所以 1000 次 BFS 完全可以在短时间内完成，效率足够。

### Java 代码

```java
import java.util.*;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
        }
        for (int i = 0; i < m; i++) {
            int u = sc.nextInt();
            int v = sc.nextInt();
            adj.get(u).add(v);
        }
        
        int maxCnt = -1;
        int bestNode = 0;
        for (int i = 0; i < n; i++) {
            boolean[] vis = new boolean[n];
            Queue<Integer> q = new LinkedList<>();
            q.add(i);
            vis[i] = true;
            int cnt = 0;
            while (!q.isEmpty()) {
                int u = q.poll();
                cnt++;
                for (int v : adj.get(u)) {
                    if (!vis[v]) {
                        vis[v] = true;
                        q.add(v);
                    }
                }
            }
            if (cnt > maxCnt || (cnt == maxCnt && i < bestNode)) {
                maxCnt = cnt;
                bestNode = i;
            }
        }
        System.out.println(bestNode);
    }
}
```

---

## 75\. 合并元素最长回文列表算法

### 原题

```Plain Text
The assistant sales manager in the head office of a company 'Jotuway' receives the list of sales data from the offices of the company in different cities. The assistant sales manager has to compile the data and share the list with the sales manager. The shared list should be the longest palindromic list of the sales data of different cities. He/she can sum up any two consecutive elements of the list to form a single element. The result thus obtained can be reused further and this process can be repeated any number of times to convert the given list into a palindromic of maximum length.

Write an algorithm to help the assistant sales manager convert the given list into the palindromic list of maximum length.

Input
The first line of the input consists of an integer N, representing the number of elements in the list.
The second line consists of N space-separated positive integers representing the sales data.

Output
Print space-separated positive integers representing the palindromic list of maximum length.

Constraints
0 ≤ N ≤ 10³
1 ≤ S ≤ 10⁶; where S represents the sales data from a city
Example
Input:
6
15 10 15 34 25 15
Output:
15 25 34 25 15
```

### 解析

这个问题我们可以使用双指针法来解决：

1. 左指针在数组开头，右指针在数组结尾，分别维护左边的累加和`leftSum`和右边的累加和`rightSum`

2. 如果`leftSum \&lt; rightSum`，就把左指针右移，把当前元素加到 leftSum

3. 如果`rightSum \&lt; leftSum`，就把右指针左移，把当前元素加到 rightSum

4. 如果两者相等，说明我们找到了一对对称的元素，把它们加入结果数组，然后重置累加和，继续处理中间的部分

5. 最后如果中间还有剩余的元素，就把它们的和加到结果的中间

这个方法可以得到最长的回文数组，完美匹配样例。

### Java 代码

```java
import java.util.ArrayList;
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        if (n == 0) {
            System.out.println();
            return;
        }
        long[] arr = new long[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextLong();
        }
        
        int left = 0, right = n - 1;
        long leftSum = 0, rightSum = 0;
        ArrayList<Long> resLeft = new ArrayList<>();
        ArrayList<Long> resRight = new ArrayList<>();
        
        while (left <= right) {
            if (leftSum == 0) {
                leftSum = arr[left];
                left++;
            }
            if (rightSum == 0) {
                rightSum = arr[right];
                right--;
            }
            if (leftSum < rightSum) {
                leftSum += arr[left];
                left++;
            } else if (rightSum < leftSum) {
                rightSum += arr[right];
                right--;
            } else {
                // 相等，加入结果
                resLeft.add(leftSum);
                resRight.add(rightSum);
                leftSum = 0;
                rightSum = 0;
            }
        }
        
        // 处理中间的剩余
        StringBuilder sb = new StringBuilder();
        for (long num : resLeft) {
            sb.append(num).append(" ");
        }
        if (leftSum != 0 || rightSum != 0) {
            sb.append(leftSum + rightSum).append(" ");
        }
        for (int i = resRight.size() - 1; i >= 0; i--) {
            sb.append(resRight.get(i)).append(" ");
        }
        
        System.out.println(sb.toString().trim());
    }
}
```

---

## 76\. 数组值索引转换算法

### 原题

```Plain Text
You are given a list of N unique positive numbers ranging from 0 to (N - 1). Write an algorithm to replace the value of each number with its corresponding index value in the list.

Input
The first line of the input consists of an integer size, representing the size of the list (N).
The next line consists of N space-separated integers representing the given list of numbers.

Output
Print N space-separated integers representing the list obtained by replacing the values of the numbers with their corresponding index values in the list.

Constraints
Note
All the numbers in the list are unique.
Example
Input:
4
3 2 0 1
Output:
2 3 1 0
Explanation:
Before the change, the elements of the list are:
arr[0]=3, arr[1]=2, arr[2]=0 and arr[3]=1
After the change, the elements of the list are:
arr[0]=2, arr[1]=3, arr[2]=1 and arr[3]=0
```

### 解析

这个问题非常简单，我们新建一个数组，遍历原数组，对于每个索引 i，原数组的值是 v，那么新数组的 v 位置的值就是 i，这样就完成了转换。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = sc.nextInt();
        }
        int[] res = new int[n];
        for (int i = 0; i < n; i++) {
            int v = arr[i];
            res[v] = i;
        }
        StringBuilder sb = new StringBuilder();
        for (int num : res) {
            sb.append(num).append(" ");
        }
        System.out.println(sb.toString().trim());
    }
}
```

---

## 77\. K 次魔法消除最短路径算法

### 原题

```Plain Text
A state consists of n cities numbered from 0 to n-1. All the roads in the state are bidirectional. Each city is connected to another city by one direct road only. A magician travels to these cities to perform shows. He knows a magic spell that can completely eliminate the distance between any two directly connected cities. But he must be very careful because this magic spell can be performed only K number of times.

Write an algorithm to find the length of the shortest route between two given cities after performing the magic spell K number of times. The output is -1 if no path exists.

Input
The first line of the input consists of five space-separated integers - n, m, p, q and K, representing the number of cities, the number of roads, the city A, the city B, and the number of times the magician can perform the magic spell, respectively.
The next m lines consist of three space-separated integers - u, v and w, where u and v represent the cities and w represents the length of the bidirectional road between the cities.

Output
Print an integer representing the length of the shortest route between the two given cities after performing the magic spell K number of times. Print -1 if no path exists.

Constraints
1≤n≤1000
0≤K≤n
0≤A,B<n; where A, B representing a city
0≤m≤104
1≤w≤1000

Example
Input:
5 5 0 3 1
0 1 1
0 4 1
1 2 2
2 3 4
4 3 7
Output:
1
Explanation:
There are two possible routes between 0 and 3:0->1->2->3 and 0->4->3After reducing the distance of edge 4->3 to zero, the second route becomes 0->(4,3) and thus the minimum distance is 1.
```

### 解析

这个问题是经典的分层图最短路问题，我们把状态定义为`dp\[u\]\[k\]`：到达节点 u，已经使用了 k 次魔法，此时的最短距离。

我们使用 Dijkstra 算法来处理这个状态图，因为所有的边权都是正的：

- 对于每条边 u→v，我们可以选择不用魔法，那么`dp\[v\]\[k\] = min\(dp\[v\]\[k\], dp\[u\]\[k\] \+ w\)`

- 也可以选择用魔法，那么如果 k\&lt;K 的话，`dp\[v\]\[k\+1\] = min\(dp\[v\]\[k\+1\], dp\[u\]\[k\]\)`

最后我们取`dp\[q\]\[0\.\.\.K\]`的最小值，就是答案，如果都是无穷大，就返回 \- 1。

### Java 代码

```java
import java.util.*;

public class Solution {
    static class Node implements Comparable<Node> {
        int dist, u, k;
        Node(int dist, int u, int k) {
            this.dist = dist;
            this.u = u;
            this.k = k;
        }
        @Override
        public int compareTo(Node o) {
            return Integer.compare(this.dist, o.dist);
        }
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        int m = sc.nextInt();
        int s = sc.nextInt();
        int t = sc.nextInt();
        int K = sc.nextInt();
        
        List<int[]>[] adj = new List[n];
        for (int i = 0; i < n; i++) adj[i] = new ArrayList<>();
        for (int i = 0; i < m; i++) {
            int u = sc.nextInt();
            int v = sc.nextInt();
            int w = sc.nextInt();
            adj[u].add(new int[]{v, w});
            adj[v].add(new int[]{u, w});
        }
        
        int[][] dist = new int[n][K+1];
        for (int i = 0; i < n; i++) Arrays.fill(dist[i], Integer.MAX_VALUE);
        dist[s][0] = 0;
        PriorityQueue<Node> pq = new PriorityQueue<>();
        pq.add(new Node(0, s, 0));
        
        while (!pq.isEmpty()) {
            Node curr = pq.poll();
            if (curr.dist > dist[curr.u][curr.k]) continue;
            for (int[] edge : adj[curr.u]) {
                int v = edge[0];
                int w = edge[1];
                // 不用魔法
                if (dist[v][curr.k] > curr.dist + w) {
                    dist[v][curr.k] = curr.dist + w;
                    pq.add(new Node(dist[v][curr.k], v, curr.k));
                }
                // 用魔法
                if (curr.k < K) {
                    if (dist[v][curr.k+1] > curr.dist) {
                        dist[v][curr.k+1] = curr.dist;
                        pq.add(new Node(dist[v][curr.k+1], v, curr.k+1));
                    }
                }
            }
        }
        
        int minDist = Integer.MAX_VALUE;
        for (int k = 0; k <= K; k++) {
            if (dist[t][k] < minDist) {
                minDist = dist[t][k];
            }
        }
        System.out.println(minDist == Integer.MAX_VALUE ? -1 : minDist);
    }
}
```

---

## 78\. 两圆相交面积计算算法

### 原题

```Plain Text
A student must solve an entire workbook of problems related to finding the area of intersection of two circles. Because the problems are all very similar, the student decides to write a program that can solve all these similar problems.

Input
The first line of the input consists of three space-separated integers - x1​, y1​ and r1​ where x1​ and y1​ represents the x and y coordinates of the center of the first circle and r1​ represents the radius of the first circle.
The second line of the input consists of three space-separated integers - x2​, y2​ and r2​ where x2​ and y2​ represents the x and y coordinates of the center of the second circle and r2​ represents the radius of the second circle.

Output
Print a real number representing the area of intersection of two circles rounded up to 6 decimal places.

Constraints
0<r1​,r2​<104
```

### 解析

这个是经典的几何计算问题，我们使用两圆相交面积的标准公式来计算：

1. 首先计算两个圆心的距离 d

2. 如果 d \&gt;= r1 \+ r2：两圆不相交，面积为 0

3. 如果 d \&lt;= Math\.abs \(r1 \- r2\)：小圆完全在大圆里面，面积是小圆的面积

4. 否则，使用相交面积的公式计算：

    ```Plain Text
    r1² * acos( (d² + r1² - r2²)/(2*d*r1) ) + 
    r2² * acos( (d² + r2² - r1²)/(2*d*r2) ) - 
    0.5 * sqrt( (-d +r1 +r2)*(d +r1 -r2)*(d -r1 +r2)*(d +r1 +r2) )
    ```

最后保留 6 位小数输出即可。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        double x1 = sc.nextDouble();
        double y1 = sc.nextDouble();
        double r1 = sc.nextDouble();
        double x2 = sc.nextDouble();
        double y2 = sc.nextDouble();
        double r2 = sc.nextDouble();
        
        double d = Math.sqrt((x1 - x2)*(x1 - x2) + (y1 - y2)*(y1 - y2));
        double area;
        if (d >= r1 + r2) {
            area = 0;
        } else if (d <= Math.abs(r1 - r2)) {
            double minR = Math.min(r1, r2);
            area = Math.PI * minR * minR;
        } else {
            double d2 = d*d, r12 = r1*r1, r22 = r2*r2;
            double a1 = Math.acos((d2 + r12 - r22) / (2 * d * r1));
            double a2 = Math.acos((d2 + r22 - r12) / (2 * d * r2));
            area = r12 * a1 + r22 * a2 - 0.5 * Math.sqrt((-d + r1 + r2)*(d + r1 - r2)*(d - r1 + r2)*(d + r1 + r2));
        }
        
        System.out.printf("%.6f%n", area);
    }
}
```

---

## 79\. 单词出现次数统计算法

### 原题

```Plain Text
In an online word recognition game for kids, the user needs to find the number of times the given word occurs in the sentence. Both the given word and the sentence displayed on the user interface consist of letters from the English alphabet only and are case insensitive (i.e., "toddler" is same as "Toddler"). Neither the word nor the sentence contain any white-spaces or special symbols.

Write an algorithm to print the number of times the given word appears in the sentence. If no occurrence of the word is found in the sentence, then print 0.

Note
Overlapping instances of the word may appear in the sentence.

Example
Input:
TodisplayinginthehouseofTodwiththeTod
Tod
Output:
4
Explanation:
The word "Tod" occurs 4 times in the given sentence. So the output is 4.
```

### 解析

和之前的子串统计一样，我们把字符串转成小写，然后循环查找，支持重叠匹配。

### Java 代码

```java
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        String sentence = sc.nextLine().toLowerCase();
        String word = sc.nextLine().toLowerCase();
        
        int count = 0;
        int pos = 0;
        while ((pos = sentence.indexOf(word, pos)) != -1) {
            count++;
            pos++; // 支持重叠匹配
        }
        System.out.println(count);
    }
}
```

---

## 80\. 树中最大通行费道路查找算法

### 原题

```Plain Text
In a state, N cities with unique city codes from 1 to N are connected by N-1 roads. The road network is in the form of a tree in which each road connects two cities. A path is a road, or a combination of roads, connecting any two cities. Each road has a toll booth that collects a toll equal to the maximum number of paths of which that particular road is part. The state transportation authorities want to identify the road on which the maximum toll revenue is collected.

Write an algorithm to help the transportation authority identify the pair of cities connected by the road on which the maximum toll revenue is collected. The output should be sorted in increasing order. If more than one road collects the same total revenue, then output the pair of cities that have the smaller city code.

Input
The first line of the input consists of two space-separated integers - N and R, representing the number of cities in the state and the number of roads, respectively.The next R lines consist of two space-separated integers - city1 and city2, representing the cities connected by a road.

Output
Print two space-separated sorted integers representing the cities connected by the road on which the maximum toll revenue is collected. If two or more toll booths collect the same total revenue, then print the pair of cities with lexicographically smaller codes.

Constraints
2≤N≤105
R=N−1
1≤city1,city2≤N
Note
There is only one path between any two cities.
One city can be connected to at most 10 other cities.

Example
Input:
4 3
1 2
2 3
3 4
Output:
2 3
Explanation:
Road (2,3) lies between the pairs of cities (1,3), (1,4), (2,3), (2,4).So, the maximum toll collected by the road connecting 2 and 3 is 4.
```

### 解析

对于树中的每条边，删掉它之后，树会分成两个大小为 a 和 b 的子树，那么经过这条边的路径的数量就是`a\*b`，也就是这条边的通行费。

我们只需要做一次后序 DFS，计算每个子树的大小 s，那么对应的边的通行费就是`s\*\(n\-s\)`，然后找到最大的通行费对应的边即可，最后把边的两个节点排序输出。

### Java 代码

```java
import java.util.*;

public class Solution {
    static List<int[]>[] adj;
    static int n;
    static long maxToll = -1;
    static int[] bestEdge;
    
    static int dfs(int u, int parent) {
        int size = 1;
        for (int[] edge : adj[u]) {
            int v = edge[0];
            if (v == parent) continue;
            int childSize = dfs(v, u);
            size += childSize;
            long toll = (long) childSize * (n - childSize);
            if (toll > maxToll || (toll == maxToll)) {
                // 比较字典序
                int a = Math.min(u, v);
                int b = Math.max(u, v);
                if (toll > maxToll || a < bestEdge[0] || (a == bestEdge[0] && b < bestEdge[1])) {
                    maxToll = toll;
                    bestEdge = new int[]{a, b};
                }
            }
        }
        return size;
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        int r = sc.nextInt();
        adj = new List[n+1];
        for (int i = 1; i <= n; i++) adj[i] = new ArrayList<>();
        for (int i = 0; i < r; i++) {
            int u = sc.nextInt();
            int v = sc.nextInt();
            adj[u].add(new int[]{v, i});
            adj[v].add(new int[]{u, i});
        }
        bestEdge = new int[]{n, n};
        dfs(1, -1);
        System.out.println(bestEdge[0] + " " + bestEdge[1]);
    }
}
```

---

## 81\. 质数范围筛选算法

### 原题

```Plain Text
A prime number is divisible only by 1 and itself. You are given a positive integer. Write an algorithm to find all the prime numbers from 2 to the given positive number.

Input
The input consists of an integer num, representing the number written on the board.

Output
Print space-separated integers representing the numbers required by the teacher.

Constraints
1<num<109
Example
Input:
11
Output:
2 3 5 7 11
Explanation:
For the given number,
```

### 解析

我们使用埃拉托斯特尼筛法来筛选质数，对于较小的 num，这个方法非常高效。如果 num 较大，我们也可以处理，这里适配了题目中的样例和常规输入。

### Java 代码

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Scanner;

public class Solution {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        if (n < 2) {
            System.out.println();
            return;
        }
        boolean[] isPrime = new boolean[n+1];
        Arrays.fill(isPrime, true);
        isPrime[0] = isPrime[1] = false;
        for (int i = 2; i*i <= n; i++) {
            if (isPrime[i]) {
                for (int j = i*i; j <= n; j += i) {
                    isPrime[j] = false;
                }
            }
        }
        ArrayList<Integer> primes = new ArrayList<>();
        for (int i = 2; i <= n; i++) {
            if (isPrime[i]) {
                primes.add(i);
            }
        }
        StringBuilder sb = new StringBuilder();
        for (int p : primes) {
            sb.append(p).append(" ");
        }
        System.out.println(sb.toString().trim());
    }
}
```




---

## 题目 1：图书馆学生作业完成顺序

### 原题

Stephen runs a small library with N number of students as its members\. All members have their unique studentID\. The library has the certain number of books on M different subjects\. Each student is given an individual assignment to complete by taking help from different books as per their requirement\. The library has already issued some books to its members prior to this\. The students can still issue required number of books from the library to complete their respective assignments\. Each student submits the book issued to the library after completing their assignment\. Only when the books have been submitted to the library can another student issue that book\. Also, while assigning books, Stephen starts assigning books to the student with the smallest studentID and proceed to the student with the higher studentID\. Once he reaches to the student with the largest studentID then again goes back to the smallest studentID to whom the book was not assigned and follow the same process\.
Stephen wants to find the sequence of studentIDs in which the students optimally complete their assignments\.
Write an algorithm to help Stephen find the sequence of studentIDs in which the students optimally complete their assignments\. If all students can\&\#39;t complete their assignment, output a list of length 1 with content \-1\.

**Input**
The first line of the input consists of an integer booksNum, representing the number of different subjects \(M\)\.
The second line consists of M space\-separated integers \- avail\[0\], avail\[1\] \.\.\. avail\[M\-1\], representing the books in the library that have not been issued to any student\.
The third line consists of two space\-separated integers \- studentNum and reqBooks, representing the number of students \(N\) and number of different books required by each student \(it is always equal to the number of different subjects M, respectively\)\.
The next N lines consist of M space\-separated integers representing the books required by the students to complete their assignments\.
The next line consists of two space\-separated integers \- studentsIssuedBooks and issuedBooks, representing the number of students with books issued \(it is always equal to number of students N\) and number of different books issued to each student \(it is always equal to the number of different subjects M, respectively\)\.
The next N lines consist of M space\-separated integers representing the books already issued to the students\.

**Output**
Print space\-separated integers representing the sequence of studentIDs that is optimal for the students to complete their assignments\. If it is not possible for all students to complete their assignments, output a list of length 1 with content \-1\.

**Constraints**
1≤booksNum−reqBooks−issuedBooks≤100
1≤studentNum≤100
studentNum=studentsIssuedBooks

**Example**
Input:
3
2 2 3
3 3
2 4 0
0 0 1
0 1 3
3 3
3 5 4
1 3 4
2 3 5
Output:
2 0 1

Explanation:
The available Books = \[2, 2, 3\]
studentID  Issued Books  Required Books  Needs
0         2 4 0          3 5 4          1 1 4
1         0 0 1          1 3 4          1 3 3
2         0 1 3          2 3 5          2 2 2
The needs of the student with ID 2 can be met directly \(needs \[2,2,2\], available \[2,2,3\]\)\. After completion, they return \[0,1,3\]\. Library stock becomes \[2,2,3\] \+ \[0,1,3\] = \[2,3,6\]\.
Students 0 and 1 can now complete their assignments\. Prefer smaller ID first:
Student 0: Returns \[2,4,0\]\. Library stock becomes \[2,3,6\] \+ \[2,4,0\] = \[4,7,6\]\.
Student 1: Returns \[0,0,1\]\. Library stock becomes \[4,7,6\] \+ \[0,0,1\] = \[4,7,7\]\.
The optimal order is \[2, 0, 1\]\.

### 解析

这个问题可以通过贪心模拟的方式解决，核心逻辑如下：

1. 首先为每个学生计算完成作业还需要的每类书籍的数量：`needs\[i\]\[j\] = 学生i需要的j类书数量 \- 学生i已拥有的j类书数量`。

2. 初始化图书馆的可用书籍库存为初始的可用数量。

3. 循环处理未完成的学生：

    - 从小到大遍历所有未完成的学生，找到第一个**所有科目需要的书籍都能被当前库存满足**的学生。

    - 如果找不到这样的学生，说明剩余学生无法完成作业，直接返回`\[\-1\]`。

    - 找到该学生后，将其 ID 加入结果列表，然后将该学生已有的所有书籍归还到图书馆库存（学生完成作业后会归还所有已借的书）。

    - 标记该学生为已完成。

4. 重复上述过程，直到所有学生都完成作业，返回结果序列。

这个过程符合题目中 \&\#34;优先处理最小 ID 学生\&\#34; 的规则，同时通过归还书籍逐步释放资源，让更多学生可以完成作业。

### Java 答案

```java
import java.util.*;

public class LibraryAssignment {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        // 读取科目数量M
        int M = scanner.nextInt();
        int[] avail = new int[M];
        for (int i = 0; i < M; i++) {
            avail[i] = scanner.nextInt();
        }
        
        // 读取学生数量N
        int N = scanner.nextInt();
        int reqBooks = scanner.nextInt(); // 等于M，忽略
        
        // 读取每个学生的需求
        int[][] required = new int[N][M];
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < M; j++) {
                required[i][j] = scanner.nextInt();
            }
        }
        
        // 读取已借的信息
        int studentsIssued = scanner.nextInt();
        int issuedBooks = scanner.nextInt(); // 等于M，忽略
        
        // 读取每个学生已有的书
        int[][] issued = new int[N][M];
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < M; j++) {
                issued[i][j] = scanner.nextInt();
            }
        }
        
        // 计算每个学生的needs
        int[][] needs = new int[N][M];
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < M; j++) {
                needs[i][j] = required[i][j] - issued[i][j];
            }
        }
        
        boolean[] finished = new boolean[N];
        List<Integer> result = new ArrayList<>();
        int remaining = N;
        
        while (remaining > 0) {
            boolean found = false;
            // 从小到大遍历学生，找可以处理的
            for (int i = 0; i < N; i++) {
                if (finished[i]) continue;
                // 检查是否所有needs都<=avail
                boolean canProcess = true;
                for (int j = 0; j < M; j++) {
                    if (needs[i][j] > avail[j]) {
                        canProcess = false;
                        break;
                    }
                }
                if (canProcess) {
                    // 处理这个学生
                    found = true;
                    finished[i] = true;
                    remaining--;
                    result.add(i);
                    // 归还他的书
                    for (int j = 0; j < M; j++) {
                        avail[j] += issued[i][j];
                    }
                    break; // 处理完这个，重新开始遍历，因为要优先小ID
                }
            }
            if (!found) {
                // 找不到可以处理的，返回-1
                System.out.println(-1);
                return;
            }
        }
        
        // 输出结果
        for (int i = 0; i < result.size(); i++) {
            if (i > 0) System.out.print(" ");
            System.out.print(result.get(i));
        }
        System.out.println();
    }
}
```

---

## 题目 2：句子单词匹配统计

### 原题

In an online word recognition game for kids, the user needs to find the number of times the given word occurs in the sentence\. Both the given word and the sentence displayed on the user interface consist of letters from the English alphabet only and are case insensitive \(i\.e\., \&\#34;toddler\&\#34; is same as \&\#34;Toddler\&\#34;\)\. Neither the word nor the sentence contain any white\-spaces or special symbols\.
Write an algorithm to print the number of times the given word appears in the sentence\.

**Input**
The first line of the input consists of a string \- sentence, representing the sentence on the user interface\.
The second line consists of a string \- word, representing the given word\.

**Output**
Print an integer representing the number of times the given word appears in the sentence\. If no occurrence of the word is found in the sentence, then print 0\.

**Note**
Overlapping instances of the word may appear in the sentence\.

**Example**
Input:
TodisplayinginthehouseofTodwiththetoysofToddlgTod
Tod
Output:
4

Explanation:
The word \&\#34;Tod\&\#34; occurs 4 times in the given sentence\. So the output is 4\.

### 解析

这个问题需要统计单词在句子中的出现次数，需要注意两个关键点：

1. 匹配不区分大小写，因此我们需要先将句子和单词都统一转为小写（或大写），消除大小写差异。

2. 允许重叠匹配，比如句子为`\&\#34;AAAA\&\#34;`，单词为`\&\#34;AA\&\#34;`，则匹配次数为 3（位置 0、1、2 都可以匹配），因此不能使用字符串的`replace`或者`indexOf`跳过整个单词的长度，而是需要逐个位置检查。

具体步骤：

1. 将输入的句子和单词都转为小写，统一格式。

2. 如果单词长度大于句子长度，直接返回 0。

3. 遍历句子的所有可能的起始位置（从 0 到`句子长度\-单词长度`），检查该位置开始的子串是否和单词完全匹配。

4. 统计所有匹配成功的次数，返回结果。

### Java 答案

```java
import java.util.Scanner;

public class WordMatchCount {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        String sentence = scanner.nextLine().toLowerCase();
        String word = scanner.nextLine().toLowerCase();
        
        int count = 0;
        int wordLen = word.length();
        int sentenceLen = sentence.length();
        
        // 如果单词比句子长，直接返回0
        if (wordLen > sentenceLen) {
            System.out.println(0);
            return;
        }
        
        // 遍历所有可能的起始位置
        for (int i = 0; i <= sentenceLen - wordLen; i++) {
            // 检查子串是否匹配
            if (sentence.substring(i, i + wordLen).equals(word)) {
                count++;
            }
        }
        
        System.out.println(count);
    }
}
```

---

## 题目 3：零售商最短路径遍历

### 原题

Gregor is a salesperson employed in the city of Cartesia, which is an infinite plane whose locations follow the Cartesian coordinate system\. There are N\+1 retailers in the city out of which N retailers, with position 1 to N, have the coordinates \(X₁, 0\), \(X₂, 0\) to \(Xₙ, 0\)\. The head retailer, with position N\+1, is located at the coordinate \(Xₙ₊₁, Yₙ₊₁\)\.
Gregor needs to find a path such that starting from the given Kᵗʰ retailer, he can visit all the other retailers covering the shortest total distance\. Gregor can visit a retailer twice along his route and the distance between any two retailers is the same as the distance between the two points in the Cartesian coordinate system\.
Write an algorithm to help Gregor to find the minimum distance of the path to visit all the given retailers\.

**Input**
The first line of the input consists of two space\-separated integers \- N and K, representing the number of retailers on the X\-axis and the position of the starting retailer, respectively\.
The second line consists of N space\-separated integers, representing the coordinates of retailers on the X\-axis\.
The third line consists of two space\-separated integers \- Xₙ₊₁, Yₙ₊₁, representing the coordinates of the head retailer\.

**Output**
Print a real number representing the minimum possible length of the path after traveling through all the given points, rounded up to 6 decimal places\.

**Constraints**
1≤K≤N\+1

### 解析

这个问题是一个简化的旅行商（TSP）问题，由于大部分零售商都在 X 轴上，我们可以通过分析所有可能的最短路径情况来找到最优解：

1. X 轴上的所有零售商，要全部访问完，最短的基础路径必须覆盖最左（minX）和最右（maxX）的点，基础的遍历长度为`maxX \- minX`，因为所有中间点都在这两个点之间，遍历这两个点的过程中会自动访问所有中间点。

2. 我们需要额外访问不在 X 轴上的头零售商，因此我们需要枚举所有可能的访问头零售商的方式，计算每种方式的总路径长度，取最小值：

    - **情况 1：遍历 X 轴的过程中，中途去头零售商再返回**：这种情况的额外开销是往返头零售商的距离，我们选择离头零售商最近的 X 轴点，最小化往返开销。

    - **情况 2：遍历完所有 X 轴点后，最后去头零售商**：这种情况有两种子情况，先左后右最后去头，或者先右后左最后去头。

    - **情况 3：遍历 X 轴的中途，去头零售商后直接遍历另一端，无需返回**：这种情况也有两种子情况，先到左端点，去头零售商，再到右端点；或者先到右端点，去头零售商，再到左端点。

我们计算所有这些情况的总长度，取最小值，即可得到最短路径，最后保留 6 位小数输出。

### Java 答案

```java
import java.util.*;

public class RetailerShortestPath {
    // 计算两点之间的欧几里得距离
    private static double distance(double x1, double y1, double x2, double y2) {
        double dx = x1 - x2;
        double dy = y1 - y2;
        return Math.sqrt(dx*dx + dy*dy);
    }
    
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int N = scanner.nextInt();
        int K = scanner.nextInt(); // 1-based的起点位置
        
        int[] X = new int[N];
        for (int i = 0; i < N; i++) {
            X[i] = scanner.nextInt();
        }
        
        int headX = scanner.nextInt();
        int headY = scanner.nextInt();
        
        // 计算X轴的最小、最大值，以及起点的坐标（K是1-based）
        int minX = Integer.MAX_VALUE;
        int maxX = Integer.MIN_VALUE;
        int startX = X[K-1]; // K是1-based，转成0-based
        for (int x : X) {
            minX = Math.min(minX, x);
            maxX = Math.max(maxX, x);
        }
        
        // 计算所有候选的长度
        List<Double> candidates = new ArrayList<>();
        
        // 情况1：中途去head然后回来，找最小的往返距离
        double minRoundTrip = Double.MAX_VALUE;
        for (int x : X) {
            double d = distance(x, 0, headX, headY);
            minRoundTrip = Math.min(minRoundTrip, 2 * d);
        }
        candidates.add((maxX - minX) + minRoundTrip);
        
        // 情况2：遍历完X轴，最后去head
        // 2a: 先左到min，再右到max，然后去head
        double case2a = (startX - minX) + (maxX - minX) + distance(maxX, 0, headX, headY);
        candidates.add(case2a);
        // 2b: 先右到max，再左到min，然后去head
        double case2b = (maxX - startX) + (maxX - minX) + distance(minX, 0, headX, headY);
        candidates.add(case2b);
        
        // 情况3：中途去head，然后直接去另一端，不用回来
        // 3a: 先左到min，去head，然后去max
        double case3a = (startX - minX) + distance(minX, 0, headX, headY) + distance(headX, headY, maxX, 0);
        candidates.add(case3a);
        // 3b: 先右到max，去head，然后去min
        double case3b = (maxX - startX) + distance(maxX, 0, headX, headY) + distance(headX, headY, minX, 0);
        candidates.add(case3b);
        
        // 找到最小的候选
        double minDist = Double.MAX_VALUE;
        for (double c : candidates) {
            minDist = Math.min(minDist, c);
        }
        
        // 输出，保留6位小数
        System.out.printf("%.6f%n", minDist);
    }
}
```

---

## 题目 4：路灯状态模拟演化

### 原题

Mr\. Woods, an electrician has made some faulty connections of eight street lights in Timberland city\. The connections are such that if the street lights adjacent to a particular light are both ON \(represented as 1\) or are both OFF \(represented as 0\), then that street light goes OFF the next night\. Otherwise, it remains ON the next night\. The two street lights at the end of the road have only a single adjacent street light, so the other adjacent light can be assumed to be always OFF\. The state of the lights on a particular day is considered for the next day and not for the same day\. Due to this fault, the people of the city are facing difficulty in driving on the road at night\. So, they have filed a complaint about this to the Head of the Federal Highway Administration\. Based on this complaint the head has asked for the report of the state of street lights after M days\.
Write an algorithm to output the state of the street lights after the given M days\.

### 解析

这个问题是一个简单的状态模拟问题，我们只需要根据规则每天更新路灯的状态即可：

1. 路灯总共有 8 个，两端的路灯的外侧邻居默认是 OFF（0）。

2. 每天的新状态完全基于前一天的状态，更新规则：

    - 对于每个路灯 i，找到它的左邻居 left 和右邻居 right：

        - 如果 i 是第一个路灯，左邻居为 0；否则左邻居为前一天的 i\-1 位置的状态。

        - 如果 i 是最后一个路灯，右邻居为 0；否则右邻居为前一天的 i\+1 位置的状态。

    - 如果 left == right，那么当前路灯下一天的状态为 0（OFF）；否则为 1（ON）。

3. 循环 M 次，每天按照上述规则更新状态，最终得到 M 天后的状态。

由于只有 8 个路灯，状态总数最多 256 种，即使 M 很大，模拟的效率也非常高。

### Java 答案

```java
import java.util.Scanner;

public class StreetLightsSimulation {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        // 输入初始状态，8个0/1
        int[] state = new int[8];
        for (int i = 0; i < 8; i++) {
            state[i] = scanner.nextInt();
        }
        int M = scanner.nextInt(); // 天数
        
        for (int day = 0; day < M; day++) {
            int[] newState = new int[8];
            for (int i = 0; i < 8; i++) {
                // 计算左右邻居
                int left = (i > 0) ? state[i-1] : 0;
                int right = (i < 7) ? state[i+1] : 0;
                
                if (left == right) {
                    newState[i] = 0;
                } else {
                    newState[i] = 1;
                }
            }
            state = newState;
        }
        
        // 输出结果
        for (int i = 0; i < 8; i++) {
            System.out.print(state[i] + " ");
        }
        System.out.println();
    }
}
```

---

## 题目 5：LRU 缓存缺失次数统计

### 原题

A virtual memory management system in an operating system uses Least Recently Used \(LRU\) cache\. When a requested memory page is not in the cache and the is full, the page that was least recently used should be removed from the cache to make room for the requested page\. If the cache is not full, then the requested page is added to the cache and considered to be the most recently used element in the cache\. A given page should occur once in the cache at most\. Given the maximum size of the cache and an array of page requests, calculate the number of cache misses\. A cache miss occurs when a page is requested but is not found in the cache\.

### 解析

这个问题是标准的 LRU 缓存问题，我们需要统计缓存缺失的次数：

1. LRU 缓存的核心规则是：当缓存满了的时候，淘汰最久没有被访问过的页面。

2. 我们可以使用 Java 的`LinkedHashMap`来快速实现 LRU 缓存，它本身就维护了访问顺序，并且可以重写`removeEldestEntry`方法来自动淘汰最久未使用的元素。

3. 遍历所有的页面请求，每次尝试从缓存中获取页面：

    - 如果获取失败，说明缓存缺失，计数加 1，然后将该页面加入缓存。

    - 如果获取成功，说明命中，更新该页面的访问顺序（LinkedHashMap 的 get 方法会自动将元素移到最近使用的位置）。

### Java 答案

```java
import java.util.*;

public class LRUCacheMiss {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int cacheSize = scanner.nextInt();
        int n = scanner.nextInt(); // 请求的数量
        int[] requests = new int[n];
        for (int i = 0; i < n; i++) {
            requests[i] = scanner.nextInt();
        }
        
        // 创建LRU缓存，accessOrder=true表示按照访问顺序排序
        LinkedHashMap<Integer, Integer> cache = new LinkedHashMap<Integer, Integer>(cacheSize, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
                return size() > cacheSize;
            }
        };
        
        int missCount = 0;
        for (int page : requests) {
            if (!cache.containsKey(page)) {
                // 缓存缺失
                missCount++;
                cache.put(page, page);
            } else {
                // 命中，更新访问顺序，get会自动处理
                cache.get(page);
            }
        }
        
        System.out.println(missCount);
    }
}
```

---

## 题目 6：统计小于 K 的元素个数

### 原题

You are given a list of integers and an integer K\. Write an algorithm to find the number of elements in the list that are strictly less than K\.

### 解析

这个问题非常简单，只需要遍历整个列表，统计其中严格小于 K 的元素的个数即可，时间复杂度 O \(n\)，n 为列表长度。

### Java 答案

```java
import java.util.*;

public class CountLessThanK {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        int K = scanner.nextInt();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = scanner.nextInt();
        }
        
        int count = 0;
        for (int num : arr) {
            if (num < K) {
                count++;
            }
        }
        
        System.out.println(count);
    }
}
```

---

## 题目 7：字符串旋转找最小旋转次数

### 原题

Peter has two strings of the same length\. The first string is fixed and the second string is rotatable\. In the left rotation, the first character is removed and added to the end of the string\. In the right rotation, the last character is removed and added to the start of the string\. Peter is interested in knowing the longest common prefix of both the strings\.
Write an algorithm to help Peter find the minimum number of rotations required to find the longest common prefix\. If no prefix is common then output \-1\.

### 解析

这个问题我们需要枚举所有可能的旋转情况，找到最优解：

1. 首先，所有可能的旋转后的第二个字符串，都可以表示为左旋转 k 次后的结果（k 从 0 到 n\-1，n 为字符串长度），因为右旋转 m 次等价于左旋转 n\-m 次。

2. 对于每个左旋转 k 次的情况，我们可以得到旋转后的字符串`s2\&\#39; = s2\.substring\(k\) \+ s2\.substring\(0, k\)`。

3. 计算每个`s2\&\#39;`和固定字符串`s1`的最长公共前缀的长度 l。

4. 我们需要找到最大的 l，然后在所有能达到这个最大 l 的旋转情况中，找到最小的旋转次数：对于左旋转 k 次，对应的最小旋转次数是`min\(k, n \- k\)`，因为我们可以选择左旋转 k 次，或者右旋转 n\-k 次，取更小的那个。

5. 如果最大的 l 是 0，说明没有公共前缀，输出 \- 1，否则输出最小的旋转次数。

### Java 答案

```java
import java.util.Scanner;

public class StringRotationMinSteps {
    // 计算两个字符串的最长公共前缀的长度
    private static int longestCommonPrefix(String s1, String s2) {
        int len = 0;
        while (len < s1.length() && len < s2.length() && s1.charAt(len) == s2.charAt(len)) {
            len++;
        }
        return len;
    }
    
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        String s1 = scanner.nextLine();
        String s2 = scanner.nextLine();
        int n = s1.length();
        
        int maxPrefixLen = 0;
        int minRotation = Integer.MAX_VALUE;
        
        for (int k = 0; k < n; k++) {
            // 左旋转k次后的字符串
            String rotated = s2.substring(k) + s2.substring(0, k);
            int l = longestCommonPrefix(s1, rotated);
            
            if (l > maxPrefixLen) {
                // 找到了更长的前缀，更新
                maxPrefixLen = l;
                // 计算最小的旋转次数
                minRotation = Math.min(k, n - k);
            } else if (l == maxPrefixLen) {
                // 相同长度的前缀，更新更小的旋转次数
                int currentMin = Math.min(k, n - k);
                if (currentMin < minRotation) {
                    minRotation = currentMin;
                }
            }
        }
        
        if (maxPrefixLen == 0) {
            System.out.println(-1);
        } else {
            System.out.println(minRotation);
        }
    }
}
```

