#### 实现开方函数

#### 给定一个List<String> names, 遍历打印所有元素.
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

#### 给定一个数组int[] orders，再给定一个int position和一个元素 int dist，要求把dist插入到orders里的position位置，如果orders是空的，则return一个新的数组，只包含一个元素dist；如果postion位置超出orders的长度，则把dist放到数组的最后一个元素
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

#### 给定一个数组int[] numbers 返回一个排序后的int数组
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

#### 给定一个数组和一个变量k，打印所有的可能性，满足数组中任意个数数字之和，等于k
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

#### 用两个栈实现队列的add和remove

#### 非递归实现中序遍历


#### 实现一个函数，将给定的链表进行反转，并打印出来。示例：6 -> 4 -> 3 -> 9 -> 12 -> 1，排序后输出  1 -> 12 -> 9-> 3 -> 4 -> 6
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


#### 字符全排列，如果有重复字符如何优化
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

#### 1. 堆排序
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

#### BFS和DFS

#### 图的遍历有几种方式

#### 序列化/反序列化二叉树（序列化成前序遍历数组）

#### 对折链表

#### 二分法找数字

#### 括号匹配

#### 统计岛屿面积

#### 链表翻转 [1,2,3,4,5,6,7,8] 初始化为单链表 一个函数 [8,7,6,5,4,3,2,1]
