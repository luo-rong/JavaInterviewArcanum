[TOC]
# 面试秘籍（算法）

## 排序
1. **常见排序算法**
![image](https://upload-images.jianshu.io/upload_images/1156494-62f859c2ac6f95ff.png)

2. **给定一个数组int[] numbers 返回一个排序后的int数组**
    ```java
    public class QSort{
        private void swap(int i, int j, int[] nums) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
    
        public void qSort(int begin, int end, int[] nums) {
            if (end > begin) {
                int l = begin, r = end;
                while (l < r) {
                    while (l < r && nums[r] >= nums[begin]) --r;
                    while (l < r && nums[l] <= nums[begin]) ++l;
                    if (l < r) {
                        swap(l, r, nums);
                    }
                }
                swap(begin, l, nums);
                qSort(begin, l-1, nums);
                qSort(l+1, end, nums);
            }
        }
    
        public void sort(int[] numbers) {
            qSort(0, numbers.length-1, numbers);
            for (int num: numbers) {
                System.out.print(num + " ");
            }
        }
    }
    ```

3. **1亿手机号排序**

### 堆排序
1. **堆排序**
    ```java
    public class HeapSort {
        private int[] heap;
        private int size;

        private void swap(int i, int j) {
            int temp = heap[i];
            heap[i] = heap[j];
            heap[j] = temp;
        }

        public int getFirstVal() {
            return size > 0 ? heap[0] : 0;
        }

        public void setFirstVal(int val) {
            heap[0] = val;
        }

        public int delLastVal() {
            return size > 0 ? heap[--size] : 0;
        }

        // i节点更新后，调整最大堆
        public void minHeapify(int i) {
            int leftVal = (i << 1) + 1 < size ? heap[(i << 1) + 1] : Integer.MAX_VALUE;
            int rightVal = (i << 1) + 2 < size ? heap[(i << 1) + 2] : Integer.MAX_VALUE;
            if (leftVal <= rightVal && leftVal < heap[i]) {
                swap(i, (i << 1) + 1);
                minHeapify((i << 1) + 1);
            } else if (rightVal < leftVal && rightVal < heap[i]) {
                swap(i, (i << 1) + 2);
                minHeapify((i << 1) + 2);
            }
        }

        public void buildMinHeap(int[] array) {
            this.size = array.length;
            this.heap = new int[size];
            System.arraycopy(array, 0, heap, 0, size);
            for (int i = (size >> 1) - 1; i >= 0; --i) {
                minHeapify(i);
            }
        }

        private int[] ascendingSort(int[] array) {
            buildMinHeap(array);
            int[] ascArray = new int[array.length];
            for (int i = 0; i < array.length; ++i) {
                ascArray[i] = getFirstVal();
                setFirstVal(delLastVal());
                minHeapify(0);
            }
            return ascArray;
        }
    }
    ```

---
## 动态规划
1. **一句话描述动态规划，举一个生活中的例子**
    
    ```
    1. 描述动态规划：
    求出f(n)，只需要知道几个更小的f(c)。我们将求解f(c)称作求解f(n)的“子问题”。这就是DP（动态规划，dynamic programming）
    
    2. 生活中的例子：
        假设一个土豪，身上带了足够的1、5、10、20、50、100元面值的钞票。现在的目标是凑出某个金额w，需要用到尽量少的钞票。长期的生活经验表明，贪心策略是正确的。
        
        但是，如果我们换一组钞票的面值，贪心策略就也许不成立了。如果钞票面额分别是1、5、11，那么我们在凑出15的时候，贪心策略会出错：15=1×11+4×1（贪心策略使用了5张钞票）；15=3×5（正确的策略，只用3张钞票）
        
        贪心策略错在了“鼠目寸光”。贪心是一种只考虑眼前情况的策略。
        
        如果直接暴力枚举凑出w的方案，明显复杂度过高。太多种方法可以凑出w了，枚举它们的时间是不可承受的。我们现在来尝试找一下性质：
        
        w=15时，
        1. 如果取11，接下来就面对w=4的情况
        2. 如果取5，则接下来面对w=10的情况
        3. 如果取1，则接下来面对w=14的情况
        
        我们发现这些问题都有相同的形式：“给定w，凑出w所用的最少钞票是多少张？”
        
        用f(n)来表示“凑出n所需的最少钞票数量”。那么，
        1. 取11：cost = f(4) + 1 = 4 + 1 = 5
        2. 取5：cost = f(10) + 1 = 2 + 1 = 3
        3. 取1：cost = f(14) + 1 = 4 + 1 = 5  
        
        `f(n)`只与`f(n - 1)` `f(n - 5)` `f(n - 11)`相关，确切的说：`f(n) = min{f(n-1), f(n-5), f(n-11)} + 1`
        
        要求出f(n)，只需要求出几个更小的f值；既然如此，我们从小到大把所有的f(i)求出来就好了
        
        它与暴力的区别在于暴力枚举了“使用的硬币”，然而这属于冗余信息。要求出f(15)，只需要知道f(14),f(10),f(4)的值。其他信息并不需要。舍弃了冗余信息。我们只记录了对解决问题有帮助的信息——f(n).　　
        
        我们能这样干，取决于问题的性质：求出f(n)，只需要知道几个更小的f(c)。我们将求解f(c)称作求解f(n)的“子问题”。这就是DP（动态规划，dynamic programming）
    ```
    
2. **最长升序子列**

---
## 数据结构
### 栈
1. **括号匹配**
2. **用两个栈实现队列的add和remove**
### 链表
1. **实现一个函数，将给定的链表进行反转，并打印出来。示例：6 -> 4 -> 3 -> 9 -> 12 -> 1，排序后输出  1 -> 12 -> 9-> 3 -> 4 -> 6**
    
    ```java
    public class ReverseList {
        public class ListNode {
            int val;
        ListNode next;
    
            ListNode(int x) {
                val = x;
            }
    }
    
        // 递归
        public ListNode reverseList1(ListNode head) {
            if (head == null || head.next == null) {
                return head;
            }
            ListNode boot = reverseList(head.next);
            head.next.next = head;
            head.next = null;
            return boot;
    }
    
        // 非递归
        public ListNode reverseList(ListNode head) {
            if (head == null || head.next == null) {
                return head;
            }
            ListNode current = head;
            ListNode prev = null;
            while (current != null) {
                ListNode next = current.next;
                current.next = prev;
                prev = current;
                current = next;
            }
            return prev;
        }
    }
    ```

2. **对折链表**


3. **给定一个List<String> names，遍历打印所有元素**
    ```java
    public class ListPrinter {
        public void printList(List<String> names){
            if (names==null||names.size()==0){
                return;
            }
            for (String name: names) {
                System.out.println(name);
            }
        }
    }
    ```

### 树
1. **非递归实现中序遍历**

2. **序列化/反序列化二叉树（序列化成前序遍历数组）**

### 图
1. **图的遍历有几种方式**

---
## 搜索
1. **BFS和DFS的区别**

2. **统计岛屿面积**

---
## 模拟
1. **给定一个数组int[] orders，再给定一个int position和一个元素 int dist，要求把dist插入到orders里的position位置，如果orders是空的，则return一个新的数组，只包含一个元素dist；如果postion位置超出orders的长度，则把dist放到数组的最后一个元素**
    ```java
    public class InsertDist {
        public int[] insert(int[] orders, int position, int dist) {
            int[] newOrders = new int[orders.length+1];
            if (position >= orders.length){
                System.arraycopy(orders, 0, newOrders, 0, orders.length);
                newOrders[orders.length] = dist;
                return newOrders;
            }
            System.arraycopy(orders, 0, newOrders, 0, position);
            newOrders[position] = dist;
            System.arraycopy(orders, position, newOrders, position + 1, orders.length - position - 1);
            return newOrders;
        }
    }
    ```

---
## 二分法
1. **二分法找数字**

---
## 数学
1. **实现开方函数**

2. **字符全排列，如果有重复字符如何优化**
    ```java
    import java.util.List;

    public class test {
        private char[] chars;
        private List<String> result;

        private void permute(int index, StringBuilder permutation) {
            if (index > chars.length) return;
            if (index == chars.length) {
                result.add(permutation.toString());
            }
            for (char ch : chars) {
                if (permutation.toString().indexOf(ch) == -1) {
                    permutation.append(ch);
                    permute(index + 1, permutation);
                    permutation.delete(index, index + 1);
                }
            }
        }

        public List<String> getPermutation(char[] chars) {
            this.chars = chars;
            permute(0, new StringBuilder());
            return result;
        }
    }
    ```

3. **给定一个数组和一个变量k，打印所有的可能性，满足数组中任意个数数字之和，等于k**
    ```java
    public class Test{
    private Set<List<Integer>> result;
    private int[] nums;
    private int k;
    
    private void makeSum(int index, int sum, List<Integer> arr) {
        if (index == nums.length) {
            if (sum == k) {
                result.add(arr);
            }
            return;
        }
        arr.add(nums[index]);
        makeSum(index+1, sum+nums[index], arr);
        arr.remove((Integer)nums[index]);
        makeSum(index+1, sum, arr);
    }
    
    public Set<List<Integer>> getResult(int k, int[] nums){
        this.nums = nums;
        this.k = k;
        result = new HashMap<>();
        makeSum(0, 0, new ArrayList<>());
        return result;
    }
    }
    ```

4. **找数组中三个数的最小方差**