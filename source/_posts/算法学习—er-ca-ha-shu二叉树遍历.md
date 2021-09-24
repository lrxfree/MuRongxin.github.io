---
title: 算法学习—二叉树遍历
date: 2021-1-04 06:24:07
tags: 算法
category: 算法
cover: ../image/算法.png
---

## 二叉树遍历

- 前序遍历：根-左-右

- 中序遍历：左-根-右

- 后序遍历：左-右-根

- 层序遍历：从上往下、从左至右

  ---遍历方式：

- 递归遍历：

- 迭代遍历：

- Morris遍历

  

![](https://z3.ax1x.com/2021/09/21/4YhPln.png)

> #### 前序遍历
>
> 要使用到栈的思想，栈用来记录走过的节点，前序遍历就是打印栈顶元素，即使不用栈的思维，也很好理解，根-左-右【1 2 4 5 6 7 3】

> > 递归

```c#
public static void TraversalTree(TreeNode root){
    if(root==null)
        return;
    Console.WriteLine(root.value);//首先打印根，在继续往下找；
    TraversalTree(root,left);
    TraversalTree(root.right);
}
```

>> 迭代

```c#
public static void TraversalTree(TreeNode root){
    if(root!=null){
        Stack<TreeNode> stack =new Stack<TreeNode>();
    	stack.Push(root);
        while(stack.Count!=0){
            root=stack.Pop();
            if(root!=null){
            	Console.WriteLine(root.value);
            	stack.push(root.right);
            	stack.push(root.left);
            }
        }
    }
}
```



> #### 中序遍历
>
> 当元素第二次成为栈顶的时候打印，【4 2 6 5 7 1 3】

>> 递归

```c#
public static void TraversalTree(TreeNode root){
    if(root==null){
        return;
    }
    TraversalTree(root,left);//从左边开始找，一直找到最左边；
    Console.WriteLine(root.value);
    TraversalTree(root.right);
}
```

>>迭代

```c#
public static void IterateTraversalTree(TreeNode root){
    if(root!=null){
        Stack<TreeNode> stack =new Stack<TreeNode>();    	
        while(stack.Count!=0||root!=null){        
            if(root!=null){            	
            	stack.push(root);
                root=root.left;            	
            }
            else{
                root=stack.Pop();
                Console.WriteLine(root.value);
                root=root.right; 
            }
        }
    }
}
```

> #### 后序遍历
>
> 当元素第三次成为栈顶元素时打印；【4 6 7 5 2 3 1】

>> 递归

```c#
public static void TraversalTree(TreeNode root){
    if(root==null){
        return;
    }
    TraversalTree(root,left);//从左边开始找，一直找到最左边；    
    TraversalTree(root.right);
    Console.WriteLine(root.value);
}
```

>> 迭代

```c#
public static void IterateTraversalTree(TreeNode root){
    if(root!=null){
        Stack<TreeNode> stack =new Stack<TreeNode>();  
        TreeNode prev = null;
        while(stack.Count!=0||root!=null){        
            while(root!=null){            	
            	stack.push(root);
                root=root.left;            	
            }
            root=stack.Pop();
            if(root.right==null||root.right==prev){              
                Console.WriteLine(root.value);
                prev=root;
                root=null;
            }
            else{
                stack.push(root);
                root=root.right;                 
            }
        }
    }
}
```

> 层序遍历
>
> 在二叉树中，每层最多2的层数次方数节点；要使用List，如果按照层序把节点放入列表，假设父节点的位置是 i，则子节点的位置是 2i 与 2i + 1；

>>递归

```c#
public static void TraversalTree(TreeNode root,int nodeIndex,List<TreeNode> treeNodeList){
    if(root==null){
        return;
    }
    int length=treeNodeList.Count;
    if(nodeIndex>=treeNodeList.Count)
        for(int i=0;i<=nodeIndex;i++)
            treeNodeList.Add(-1);
    
    treeNodeList[nodeIndex]=root.value;
    
    TraversalTree(root.left,nodeIndex*2,treeNodeList);
    TraversalTree(root.right,nodeIndex*2+1,treeNodeList);
    
}	
```

> > 迭代

```c#
public static void IterateTraversalTree(TreeNode root){
    Queue<NodeTree> queue = new Queue<TreeNode>();
    queue.Dequeue(root);
    while(queue.Count!=0){       
    	TreeNode node=queue.Enqueue();
    	if(root!=null){
            Console.WriteLine(node.value);
            queue.Dequeue(node.left);
            queue.Dequeue(node.right);
        }
    }
}
```



>  无论使用递归还是迭代，时间/空间复杂度都是O(N)，

> Morris遍历 线索二叉树
>
> 利用空闲的指针，数量：2N-(N-1)；之前的遍历空间复杂度高，是因为要不断保存路径上的节点；
>
> ```
> 中序线索二叉树很方便，因为它能投影在数轴上，能很方便找到前驱；节点的前驱节点就是它左边的最右子节点；Morris遍历的前序、中序、后序 唯一的区别就是对于打印时机的处理；最麻烦的就是后序遍历；
> ```

> > 前序遍历

```c#
public static void MorrisScanTree(NodeTree currentNode){
    if(currentNode==null)
        return;
    TreeNode mostRight = null;    while(currentNode!=null){
        mostRight=currentNode.right;
        if(mostRight!=null){
            while(mostRight.right!=null&&mostRight.right！=currentNode){
                mostRight=mostRight.right;
            }
            if(mostRight.right==null){//建立索引   	
                mostRight.right=currentNode; 
                Console.WriteLine(current.value);
                currentNode=currentNode.left;
                continue;
            }
            else{//释放索引
                mostRight.right=null;
            }
        }
        else{
            Console.WriteLine(current.value);
        }
        currentNode=currentNode.right;
    }
}
```

> > 中序遍历

```c#
public static void MorrisScanTree(NodeTree currentNode){
    if(currentNode==null)
        return;
    TreeNode mostRight = null;
    while(currentNode!=null){
        mostRight=currentNode.right;
        if(mostRight!=null){
            while(mostRight.right!=null&&mostRight.right！=currentNode){
                mostRight=mostRight.right;
            } 
            if(mostRight.right==null){//建立索引
                mostRight.right=currentNode;
                currentNode=currentNode.left;
                continue;
            }
            else{//释放索引 
                mostRight.right=null;
            } 
        }
        else{
        }
        Console.WriteLine(current.value);
        currentNode=currentNode.right;
    } 
}
```

> > 后序遍历

```c#
public static void MorrisScanTree(NodeTree currentNode){
    if(currentNode==null)
        return;
    TreeNode root=currntNode;
    TreeNode mostRight = null; 
    while(currentNode!=null){
        mostRight=currentNode.right;
        if(mostRight!=null){
            while(mostRight.right!=null&&mostRight.right！=currentNode){
                mostRight=mostRight.right;
            } 
            if(mostRight.right==null){//建立索引
                mostRight.right=currentNode;
                currentNode=currentNode.left;
                continue;
            }       
            else{//释放索引  
                mostRight.right=null; 
                PrintNode(currentNode.left);
            }   
        }     
        else{ 
        }  
        currentNode=currentNode.right; 
    } 
    PrintNode(root); 
}
private static void PrintNode(TreeNode head){
    TreeNode tail=Reverse(head);
    while(tail!=null){ 
        Console.WriteLine(current.value); 
        tail=tail.right; 
    }   
    Reverse(tail); }
private static void Reverse(TreeNode head){
    TreeNode next=null;
    TreeNode curr=null;
    TreeNode prev=null;
    while(curr!=null){ 
        next=curr.right; 
        curr.right=prev; 
        prev=curr;   
        curr=next;  
    }   
    return prev;
}
```

