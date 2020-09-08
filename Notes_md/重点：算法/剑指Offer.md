# 剑指Offer解题记录
### 1. 二维数组中的查找
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

### 2. 根据前序遍历和中序遍历重建二叉树

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

### 用两个栈实现队列

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

### 旋转数组的最小数字

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

