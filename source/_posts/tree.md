---
title: Java实现二叉树
date: 2017-10-19 12:38:19
tags: [树,Java]
---
<center>**参考链接：** [java实现二叉树](http://blog.csdn.net/skylinesky/article/details/6611442)、[Java实现基本数据结构2（树）](https://segmentfault.com/a/1190000002606302)</center>
&emsp;&emsp;在各个公司的比试中很多都涉及了二叉树的前序、中序、后序遍历，本文包含树的创建，树的前序、中序、后序遍历（递归和非递归），层次遍历，求树高，判断是否为平衡二叉树的Java实现。
<!--more-->
### 一、建立二叉树
1. 定义二叉树结点Node：
    ```java
    class Node<T>{
        private T value;
        private Node<T> left;
        private Node<T> right;
        public Node() {

        }
        public Node(T value, Node<T> left, Node<T> right) {
            this.value = value;
            this.left = left;
            this.right = right;
        }
        public Node(T value) {
            this(value,null,null);
        }
        public T getValue() {
            return value;
        }
        public void setValue(T value) {
            this.value = value;
        }
        public Node<T> getLeft() {
            return left;
        }
        public void setLeft(Node<T> left) {
            this.left = left;
        }
        public Node<T> getRight() {
            return right;
        }
        public void setRight(Node<T> right) {
            this.right = right;
        }
    }
    ```
2. 定义二叉树Tree：
```java
class Tree<T>{
    private Node<T> root;//根节点
    public Tree() {
        
    }   
    public Tree(Node<T> root) {
        this.root = root;
    }
    
    //创建二叉树
    public void buildTree(){
        System.out.println("输入一棵树（按层），叶子节点用#表示");
        Scanner scx = new Scanner(System.in);
        String input = scx.nextLine();
        scx.close();
        Scanner sc=new Scanner(input);
        root=createTreeAsLevel(sc);
    }
    //按层建立树(用“#”填充，使其为完全二叉树）
    private Node<T> createTreeAsLevel(Scanner sc){
        ArrayList<Node<T>> list = new ArrayList<Node<T>>(); 
        while(sc.hasNext()){
            String temp =sc.next();
            if (temp.trim().equals("#"))
                list.add(null);
            else list.add(new Node((T)temp));
        }
        for(int i=0;i<list.size();i++){
            Node<T> t = list.get(i);
            if(t==null)
                continue;
            if (2*i+1<list.size())
                t.setLeft(list.get(2*i+1));         
            if(2*i+2<list.size())
                t.setRight(list.get(2*i+2));
            list.set(i, t);         
        }
        return list.get(0);        
    }
}
```

### 二、树的先序遍历
1. 递归：
```java
    public void preOrderTraverse(Node<T> node) {
        if (node != null) {
            System.out.print(node.getValue()+" ");  
            preOrderTraverse(node.getLeft());  
            preOrderTraverse(node.getRight());  
        }  
    } 
```
2. 非递归：
&emsp;&emsp; 使用栈实现，先让根进栈。只要栈不为空，就可以弹栈。每次弹出一个节点，要把它的左右子节点进栈（**右子节点**先进栈）。
```java
    public void nrPreOrderTraverse(Node<T> rootnode) {  
        if(rootnode==null){
            return;
        }  
        Stack<Node<T>> stack = new Stack<Node<T>>();  
        Node<T> node = rootnode;  
        stack.push(node);
        while(stack.isEmpty()!=true){
            node = stack.pop();
            System.out.print(node.getValue()+" ");
            if(node.getRight() != null ){//重点：右子节点先进栈
                stack.push(node.getRight());
            }
            if(node.getLeft() != null){
                stack.push(node.getLeft());
            }
        }  
                 
    } 
```

### 三、树的中序遍历
1. 递归：
```java
    public void inOrderTraverse(Node<T> node) { 
        if (node != null) {  
            inOrderTraverse(node.getLeft());
            System.out.print(node.getValue()+" ");  
            inOrderTraverse(node.getRight());  
        } 
    }
```
2. 非递归：
```java
    /*1、current = root;
     *2、把current, current的左孩子，current的左孩子的左孩子都入栈,直至current = null -> 跳到step 3
     *  （current = current.left, push(current)）
     *3、若current = null 且栈没空，则弹栈，并访问。current = 弹出的节点的右孩子 <- 十分重要，之后重复2。
    */
    public void nrInOrderTraverse(Node<T> rootnode) {
        if(rootnode==null){
            return;
        }
        Node<T> current = rootnode;
        Stack<Node<T>> stack = new Stack<Node<T>>();
        while(current != null||stack.isEmpty()!=true){
            while(current!=null){
                stack.push(current);
                current = current.getLeft();
            }
            if(current==null){
                Node<T> node = stack.pop();
                System.out.print(node.getValue()+" ");
                current = node.getRight();
            }
        } 
    }  
```

### 四、树的后序遍历
1. 递归：
```java
    public void postOrderTraverse(Node<T> node) {  
        if (node != null) {  
            postOrderTraverse(node.getLeft());  
            postOrderTraverse(node.getRight());  
            System.out.print(node.getValue()+" ");  
        }  
    } 
```
2. 非递归：
```java
    /* 1.1 创建一个空栈
       2.1 当current is not null
        a) 先右孩子进栈，然后current进栈
        b) 设置current为左孩子     \这样从根节点，down to 最左孩子节点。最后current == null
       2.2 出栈，设置出栈的节点为current  \既然出栈了，该节点肯定没有左孩子。
        a) 如果出栈节点存在右孩子并且 右孩子是栈顶并且 栈不为空 ,则 再弹栈（弹出右孩子），把current指向的刚刚出栈的节点（右孩子的爹）入栈。
                设置 current = current.right;
        b) 如果出栈节点不存在右孩子，那么就可以访问之。记得设置current = null
       2.3 重复 2.1 and 2.2 直到栈空.
    */
    public void nrPostOrderTraverse(Node<T> rootnode) {
        if(rootnode==null){
            return;
        }   
        Stack<Node<T>> stack = new Stack<Node<T>>();
        Node<T> current = rootnode;
        while(current !=null || stack.isEmpty()!=true){     
            //step 1 
            while(current!=null){   
                if(current.getRight()!=null){
                    stack.push(current.getRight());
                }
                stack.push(current);
                current = current.getLeft();
            }

            // step2 既然出栈了，该节点肯定没有左孩子。
            current = stack.pop();
            if(current.getRight() !=null && !stack.isEmpty() && current.getRight() == stack.peek())  {
                    stack.pop(); //出栈右孩子
                    stack.push(current);
                    current = current.getRight();
            }
            else{
                System.out.print(current.getValue()+" ");
                current = null;
            }
        }
    }   
```

### 五、树的层次遍历
```java
    public void levelTraverse(Node<T> node) {      
        Queue<Node<T>> queue = new LinkedList<Node<T>>();  
        queue.add(node);  
        while (!queue.isEmpty()) {               
            Node<T> temp = queue.poll();  
            if (temp != null) {  
                System.out.print(temp.getValue()+" ");  
                queue.add(temp.getLeft());  
                queue.add(temp.getRight());  
            }                           
        }                   
    }
```
