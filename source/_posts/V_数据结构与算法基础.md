---
title: 数据结构与算法基础
date: 2019-11-16 10:17:59
tags: 
categories: 
---
#一、*思维导图*
#*总知识点*
##V_数据结构与算法基础
###一、概述
####1、数据结构概述
	1、什么是数据结构
	2、数据存储结构
		1、顺序存储结构
		2、链式存储结构
	3、数据的逻辑结构
		1、集合结构
		2、线性结构
		3、树形结构
		4、图形结构
####2、算法概述
	1、算法的定义
	2、算法的特性
		1、输入
		2、输出
		3、有穷性
		4、确定性
		5、可行性
	3、算法的基本要求
		1、正确性
		2、可读性
		3、健壮性
		4、时间复杂度
		5、空间复杂度
###二、线性结构
####顺序存储结构
####1、数组
#####1.1、数组的基本使用
	1、使用
	public class TestOpArray {
	    public static void main(String[] args) {
	        int[] arr = new int[]{9,8,7,6,5,4,3};
	        System.out.println(Arrays.toString(arr));
	

​		// 定义一个需要删除的下标
​        int dst = 3;

​		//定义一个新数组
​        int[] newArr = new int[arr.length-1];

​        for (int i = 0; i < newArr.length; i++) {
​		//保留dst之前的数组元素
​            if (i < dst){
​                newArr[i] = arr[i];
​            }else {
​                newArr[i] = arr[i+1];
​            }
​        }
​		// 重新赋给arr
​        arr = newArr;
​        System.out.println(Arrays.toString(arr));
​    }
}

2、添加
3、删除

#####1.2、面向对象的数组
#####1.3、数组的有序性
#####1.4、查找法
	1、线性查找
	2、二分法查找
		// 二分法
		public class TestBinarySearch {
		    public static void main(String[] args) {
			//定义有序的数组
		        int[] arr = new int[]{1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
		

​		//定义需要查找的数
​	        int target = 9;
​	
​		// 开始的下标为0
​	        int start = 0;
​		//结束的下标
​	        int end = arr.length - 1;
​		//中间的下标
​	        int mid = (start + end) / 2;
​	
​	        int index = -1;
​	
​	        for (int i = 0; i < arr.length; i++) {
​	            if (arr[mid] == target) {
​	                index = mid;
​	                break;
​	            } else {
​	                // 大于
​	                if (arr[mid] > target) {
​	                    end = mid - 1;
​	
​	                } else {
​	                    start = mid + 1;
​	                }
​	                mid = (start + end) / 2;
​	            }
​	        }
​	        System.out.println("index-->" + index);
​	
​	    }
​	}

####2、栈
	1、栈的内部实现的结构是数组
	2、栈的方法包括
		1、入栈
		2、出栈
		3、查看栈顶元素
		4、判断是否为空

public class MyStack {

​    private int[] elements;

​    public MyStack() {
​        elements = new int[0];

​    }

//入栈
    public void push(int element) {
        int[] newArr = new int[elements.length + 1];

​        for (int i = 0; i < elements.length; i++) {
​            newArr[i] = elements[i];
​        }
​        newArr[newArr.length-1] = element;

​        elements = newArr;
​    }


​	

​    //取出栈顶元素
​    public int pop() throws Exception {
​        if (elements.length==0){
​            throw new Exception();
​        }
​        // 取出最大下标值
​        int pop = elements[elements.length - 1];
​        // 创建一个新的数组
​        int[] newArr = new int[elements.length - 1];
//重新赋值
​        for (int i = 0; i < elements.length - 1; i++) {
​            newArr[i] = elements[i];
​        }
​        elements = newArr;
​        return pop;
​    }

​    public int peek(){
​        return elements[elements.length-1];
​    }
}

####3、队列
	1、队列的内部实现的结构是数组
	2、队列的方法包括
		1、入队
		2、出队
		3、判断是否为空
	public class MyQueue {

​    private int[] elements;

public MyQueue(){
    elements = new int[0];
}

public void add(int element){
    int[] newArr = new int[elements.length + 1];
    for (int i = 0; i < elements.length; i++) {
        newArr[i] = elements[i];
    }
    newArr[newArr.length-1] = element;
    elements = newArr;
}

public int pool(){
    int pool = elements[0];
    int[] newArr = new int[elements.length-1];
    for (int i = 0; i < elements.length-1; i++) {
        newArr[i] = elements[i+1];
    }
    elements = newArr;
    return  pool;
    }
 }

####链式存储结构
####4、单链表
	1、链表初始化
	2、单链表的新增
	3、单链表中删除
	

public class Node {

​    private int data;

​    // 下一个节点
​    Node next;

​    public  Node(int data){
​        this.data = data;
​    }

​    // 为节点追回节点
​    public void append(Node node){
​        Node currentNode = this;
​        // 循环向后找
​        while (true){
​            // 取出下一个节点
​            Node nextNode = currentNode.next;

​            // 如果下一个节点为null，当前节点已经是最后一个节点
​            if (nextNode == null){
​                break;
​            }

​            // 赋值当前节点
​            currentNode = nextNode;
​        }
​        // 把需要追回的节点追加为找到的当前节点的下一个节点
​        currentNode.next = node;
​    }

​    // 获取下一个节点
​    public Node next(){
​        return this.next;
​    }


​	

​    public int getData(){
​        return this.data;
​    }

​    // 删除结点
​    public void removeNode(){
​        // 取出下下个节点
​        Node newNode = next.next;
​        // 当前节点的下一个节点
​        this.next = newNode;
​    }

​    // 新增结点
​    public void after(Node node){
​        // 获取下一结点
​        Node nextNextNode = this.next;
​        // 当前节点的下一个节点为新增结点
​        this.next = node;
​        node.next = nextNextNode;
​    }
}

####5、循环链表
	类似于单链表，只不过是尾指针指向首结点
####6、双链表
	循环双向链表
####7、递归
	1、概念：在一个方法内部调用该方法本身的编程方法
	2、应用：
		裴波那契数列   1 1 2 3 5 8
		汉诺塔问题 

public class TestRecusive {

   public static void main(String[] args) {
   //print(10);

   //System.out.println(febonacci(3));
   //System.out.println(febonacci(5));

​       hanoi(3,'A','B','C');
   }

   // 递归
   public static void print(int i) {
       if (i > 0) {

​           System.out.println(i);
​           print(i - 1);
​       }
   }


​	

   // 裴波那契数列
   public static int febonacci(int i) {
       if (i == 1 || i == 2) {
           return 1;
       } else {
           return febonacci(i - 1) + febonacci(i - 2);
       }
   }


​	

  /**
   *

	    * @param n  共有N个盒子
	    * @param from  开始柱子
	    * @param in   中间柱子
	        * @param to   目的柱子
	        */
	      public static void  hanoi(int n , char from , char in , char to){
	       // 只有一个盘子
	       if (n == 1){
	           System.out.println("第一个盘子"+from + "移动"+to);
	       }else {
	           // 除了最下面的一个盘子外，全部的看成一个盘子
	           hanoi(n-1,from , to , in);
	           System.out.println("第"+n+"个盘子从"+from + "移动"+to);
	           // 把最上面的盘子从中间移动到目标位置
	           hanoi(n-1,in,from,to);
	       }
	      }
	}


####8、排序算法 
####8.1、时间复杂度和空间复杂度概述
####8.2、8种常用排序算法
####1、交换排序
#####1.1、冒泡排序
	

/**
	 * 冒泡排序
	 */
	public class BubbleSort {
	
	​    public static void main(String[] args) {
	​        int[] arr = {2, 6, 1, 4, 0, 8, 19, 7};
	
	​        System.out.println(Arrays.toString(arr));
	​        bubbleSort(arr);
	​        System.out.println(Arrays.toString(arr));
	​    }


​		

​	    /**
	     * 冒泡排序
	     * 2,6,1,4,0,8,19,7  共比较arr.length-1轮
	     * 第一轮：2,6,1,4,0,8,19,7   2,1,6,4,0,8,19,7 。。。。
	          *
	          * @param arr
	               */
	            private static void bubbleSort(int[] arr) {
	        // 控制轮数
	        for (int i = 0; i < arr.length - 1; i++) {
	                // 控制每一轮的里面比较的次数
	                for (int j = 0; j < arr.length - 1 - i; j++) {
	                if (arr[j] > arr[j + 1]) {
	                    // 交换
	                    int temp = arr[j];
	                    arr[j] = arr[j+1];
	                    arr[j+1] = temp;
	                }
	                }
	        }
	            }
	}


​			
#####1.2、快速排序
​		

public class QuickSort {
    public static void main(String[] args) {

​        int[] arr = {4, 2, 45, 12, 3, 1, 2, 10, 14, 15};

​        quickSort(arr, 0, arr.length - 1);

​        System.out.println(Arrays.toString(arr));
​    }

​    /**
     * 快速排序
          *
          * @param arr
               * @param i
               * @param i1
                    */
                private static void quickSort(int[] arr, int start, int end) {
        if (start < end) {
                    // 寻找一个标准数
                    int stard = arr[start];

        ​    // 低位指针
        ​    int low = start;

        ​    // 高位指针
        ​    int high = end;

        ​    while (low < high) {
        ​        // 右边的数比标准数大
        ​        while (low < high && stard <= arr[high]) {
        ​            high--;
        ​        }
        ​        // 用右边的数字替换左边的数
        ​        arr[low] = arr[high];

        ​        while (low < high && arr[low] <= stard) {
        ​            low++;
        ​        }
        ​        // 不然替换到高位
        ​        arr[high] = arr[low];
        ​    }
        ​    // 重复赋值给两个指针重合的地方
        ​    arr[low] = stard;
        ​    // 递归处理小的数组
        ​    quickSort(arr, start, low);
        ​    // 递归处理大的数组
        ​    quickSort(arr, low + 1, end);
        }

​    }
}

####2、插入排序
#####2.1、直接插入排序
			

public class InsertSort {
    public static void main(String[] args) {

​        int[] arr = {5, 7, 9, 2, 3, 1, 10, 8};

​        // 插入排序
​        insertSort(arr);

​        System.out.println(Arrays.toString(arr));
​    }

​    /**
     * 插入排序
          *
          * @param arr
               */
            private static void insertSort(int[] arr) {
        // 从第二位开始遍历
        for (int i = 1; i < arr.length; i++) {
                // 当前元素比前一元素小
                if (arr[i] < arr[i - 1]) {
                // 把当前的元素的存入临时变量
                int temp = arr[i];
                int j;
                // 遍历当前数字前面所有的数字(没有理解)
                for (j = i-1; j >= 0 && temp < arr[j]; j--) {
                    // 把前一个数字赋给后一个数字
                    arr[j + 1] = arr[j];
                }
                arr[j+1] = temp;
                }
        }
            }

}

#####2.2、希尔排序(未理解)
		public class ShellSort {
	

​    public static void main(String[] args) {
​        int[] arr = new int[]{3, 5, 2, 7, 8, 1, 2, 0, 4, 7, 4, 3, 8};

​        System.out.println("排序之前：" + Arrays.toString(arr));

​        shellSort(arr);

​        System.out.println("排序之后：" + Arrays.toString(arr));


​	

​    }

​    /**
     * 希尔排序
          *
          * @param arr
               */
            private static void shellSort(int[] arr) {
        int k = 1;
        // 遍历所有的步长
        for (int d = arr.length / 2; d > 0; d /= 2) {
                // 遍历所有的元素
                for (int i = d; i < arr.length; i++) {
                for (int j = i - d; j >= 0; j -= d) {
                    // 如果当前元素大于加上步长后的那个元素
                    if (arr[j] > arr[j + d]) {
                        int temp = arr[j];
                        arr[j] = arr[j + d];
                        arr[j + d] = temp;
                    }
                }
                }
                System.out.println("第" + k + "次排序" + Arrays.toString(arr));
                k++;
        }

​    }
}


​		
####3、选择排序(未理解)
#####3.1、简单选择排序

public class SelectSort {

public static void main(String[] args) {
    int[] arr = new int[]{4, 2, 7, 1, 8, 3, 6, 9};

​    selectSort(arr);

​    System.out.println(Arrays.toString(arr));
}

/**
 * 选择排序
 * @param arr
 */
private static void selectSort(int[] arr) {
    // 遍历所有的数
    for (int i = 0; i < arr.length; i++) {
        int minIndex = i;
        // 把当前遍历的数和后面的所有数依次进行比较，并记录下最小的数的下标
        for (int j = i+1;j<arr.length;j++){
            // 如果后面比较的数比记录的最小数小
            if (arr[minIndex]>arr[j]){
                // 记录下最小的那个数的下标
                minIndex = j;
            }
        }
        // 如果最小的数和当前遍历数的下标不一致，说明下标为minIndex的数比当前遍历的数更小
        if (i !=minIndex){
            int temp = arr[i];
            arr[i] = arr[minIndex];
            arr[minIndex] = temp;
        }
    }
}

}
#####3.2、堆排序	
	
####4、归并排序(未理解)
	
####5、基数排序(未理解)
#####8.3、8种常用算法的对比

###三、树结构
####1、树结构概述
	1、概念：
		1、根节点
		2、双亲节点
		3、字节点
		4、路径
		5、节点的度
		6、节点的权
		7、叶子节点
		8、子树
		9、层
		10、数的高度
		11、森林
####2、二叉树
	1、概念
		任何一个结点的子节点数量不超过2
		二叉树的子节点分为左节点和右节点

​	满二叉树：所有的叶子节点都在最后一层，而且节点的总数为2^n-1,n为树的高度
​	完全二叉树：所有的叶子节点都在最后一层或倒数第二层，且最后一层的叶子节点在左边连续，倒数第二节的叶子节点在右边连续 
2、存储结构
​	1、链式存储二叉树
​		1、创建二叉树
​		2、添加节点
​		3、树的遍历
​			1、前序遍历
​				根节点-左树-右树
​			2、中序遍历
​				左树-根-右树
​			3、后序遍历
​				左树-右树-根
​		
​		4、查找节点
​		5、删除节点
​	2、顺序存储二叉树

####3、线索二叉树
####4、赫夫曼树
####5、二叉查找树
####6、AVL树
####7、多路查找树