# 剑指Offer解题记录

[数组](# 数组)

[双指针/三指针](# 双指针/三指针)

[动态规划](# 动态规划)

[树](# 树)

[二进制](# 二进制)



## 数组

#### 1. 二维数组中的查找
```txt
在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
```

```java
public boolean Find(int target, int [][] array) {
    
    //方法1：遍历，n^2
    for(int i = 0;i<array.length;i++){
        for(int j = 0;j<array[0].length;j++){
            if(target==array[i][j]) return true;
        }
    }

    ////方法2：从右上角一次去掉一整行或一整列
    //int rangei = 0;
    //int rangej = array[0].length-1;
    //for(int i = rangei;i<array.length;i++){
    //    for(int j = rangej;j>=0;j--){
    //        if(array[i][j]==target){
    //            return true;
    //        }
    //        else if(array[i][j]>target){
    //            rangej--;
    //        }
    //        else{
    //            rangei++;
    //        }
    //    }
    //}
    return false;
}
```

#### 调整数组顺序使奇数位于偶数前面

```txt
输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。
```

```java
//使用插入排序的思想
//i表示此轮将做好序的数组的大小
for(int i = 1;i<array.length;i++){
    int  cur = array[i];
    if((cur&1) == 1){
        int j = i-1;
        while(j>=0&&(array[j]&1)==0){
            array[j+1] = array[j];
            j--;
        }
        array[j+1]=cur;
    }
}
```



#### 用两个栈实现队列

```txt
用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。
```

```java
public class Solution {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();
    
    public void push(int node) {
        stack1.push(node);
    }
    
    public int pop() {
        if(stack2.isEmpty()){
            while(!stack1.isEmpty()){
                stack2.push(stack1.pop());
            }
        }
        return stack2.pop();
    }
}
```

#### 旋转数组的最小数字

```txt
把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。
例如数组[3,4,5,1,2]为[1,2,3,4,5]的一个旋转，该数组的最小值为1。
NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。
```

```java
public int minNumberInRotateArray(int [] array) {
    int len = array.length;
    if(array.length==0) return 0;
    int left = 0;
    int right = len-1;
    int cur;
    while(left<right){
        cur = (left+right)/2;
        if(array[right]<array[cur]){
            //最小数在(cur,cur]区间
            left=cur+1;
        }
        else if(array[right]>array[cur]){
            //最小数在[left,cur]区间
            right=cur;
        }
        else{
            //缩小范围
            right--;
        }
    }
    return array[left];
}
```

## 双指针/三指针

#### 链表中倒数第k各结点

```txt
输入一个链表，输出该链表中倒数第k个结点。
```

```java
/*
public class ListNode {
    int val;
    ListNode next = null;

    ListNode(int val) {
        this.val = val;
    }
}*/
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        if(head==null||k==0) return null;
        
        int fastIndex = 1;
        ListNode fast = head;
        ListNode slow = head;
        while(fast.next!=null){
            fast = fast.next;
            if(fastIndex>=k){
                slow = slow.next;
            }
            fastIndex++;
        }
        return fastIndex<k?null:slow;
    }
}
```

#### 丑数

```txt
把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。
```

```java
 public int GetUglyNumber_Solution(int index) {
        if(index <= 0)return 0;
        int p2=0,p3=0,p5=0;//初始化三个指向三个潜在成为最小丑数的位置
        int[] result = new int[index];
        result[0] = 1;//
        for(int i=1; i < index; i++){
            result[i] = Math.min(result[p2]*2, Math.min(result[p3]*3, result[p5]*5));
            if(result[i] == result[p2]*2)p2++;//为了防止重复需要三个if都能够走到
            if(result[i] == result[p3]*3)p3++;//为了防止重复需要三个if都能够走到
            if(result[i] == result[p5]*5)p5++;//为了防止重复需要三个if都能够走到
        }
        return result[index-1];
    }
```



## 动态规划

#### 斐波那楔数列

```txt
大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0，第1项是1）。n<=39
```

```java
public class Solution {
    public int Fibonacci(int n) {
        int[] res = new int[40];
        res[0] =0;
        res[1] =1;
        for(int i = 2;i<=n;i++){
            res[i] =  res[i-1]+res[i-2];
        }
        return res[n];
    }
}
```

#### 跳台阶

```txt
一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。
```

```java
public class Solution {
    public int JumpFloor(int target) {
        int[] dp = new int[target+1];
        dp[0] = 1;
        dp[1] = 1;
        for(int i = 2;i<=target;i++){
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[target];
    }
}
```

#### 变态跳台阶

```txt
一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。
```

```java
public class Solution {
    public int JumpFloorII(int target) {
        int[] dp = new int[target+1];
        dp[0] = 1;
        dp[1] = 1;
        for(int i = 2;i<=target;i++){
            for(int j = 0;j<i;j++){
                dp[i]+=dp[j];
            }
        }
        return dp[target];
    }
}
```

#### 矩形覆盖

```txt
我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？
```

<img src="imgs\动态规划.png" alt="img" style="zoom:50%;" />

```java
public class Solution {
    public int RectCover(int target) {
        if(target<2) return target;
        int[] dp = new int[target+1];
        dp[0] = 0;
        dp[1] = 1;
        dp[2] = 2;
        for(int i = 3;i<=target;i++){
            dp[i]=dp[i-1]+dp[i-2];
        }
        return dp[target];
    }
}
```

## 树

#### 根据前序遍历和中序遍历重建二叉树

```txt
输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。
```

![1599271133115](imgs\1599271133115.png)

```java
/*
* 思路：
* 一个树的前序遍历的第一个值，肯定是根节点，后边跟着的一段区间是左子树的前序遍历，最后的一段区间是右子树的前序遍历
* 在中序遍历中找到根节点的位置，根节点左边就是左子树的中序遍历，根节点右边就是右子树的中序遍历
* 将左子树和右子树递归
*/
Map<Integer,Integer> mapIn;
public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
    int prelen = pre.length;
    int inlen = pre.length;
    //遍历一次中序遍历数组的值，存于hashMap
    mapIn = new HashMap(prelen);
    for(int i = 0;i<prelen;i++){
        mapIn.put(in[i],i);
    }
    return reConstructBinaryTree(pre,in,0,prelen-1,0,inlen-1);
}

public TreeNode reConstructBinaryTree(int[] pre, int[] in ,int leftp, int rightp, int lefti, int righti)
{
    if(leftp>rightp) return null;

    //提取根节点
    TreeNode childRoot = new TreeNode(pre[leftp]);
    //根节点在中序遍历中的位置
    int rootIndex = mapIn.get(pre[leftp]);
    //将左子树前序遍历和中序遍历的区间作为递归参数，构造左子树
    childRoot.left=reConstructBinaryTree(pre,in,leftp+1,leftp+rootIndex-lefti,lefti,rootIndex-1);
    //将右子树前序遍历和中序遍历的区间作为递归参数，构造右子树
    childRoot.right=reConstructBinaryTree(pre,in,leftp+rootIndex-lefti+1,rightp,rootIndex+1,righti);
    return childRoot;
}
```

#### 

## 二进制

#### 二进制中1的个数

```txt
输入一个整数，输出该数32位二进制表示中1的个数。其中负数用补码表示。
```

```java
public class Solution {
    public int NumberOf1(int n) {
        int res = 0;
        String str = Integer.toBinaryString(n);
        char[] chars = str.toCharArray();
        for(char c : chars){
            if(c=='1') res++;
        }
        return res;
    }
}
```

