## 232. Implement Queue using Stacks

用两个栈实现队列

1.input栈是栈顺序，output栈是队列顺序。  
2.所以放元素时就放到input中，取元素就查找top时就在output中操作。  
{: .notice} 

```java

```

***

## 225. Implement Stack using Queues

Implement the following operations of a stack using queues.

用队列实现栈

思路：  
在入队时，将队列的顺序倒转，这样就可以实现后进先出。  
{: .notice} 

```java
class MyStack {
    Queue<Integer> queue;

    public MyStack() {
        this.queue = new LinkedList<Integer>();
    }

    // Push element x onto stack.
    public void push(int x) {
        queue.add(x);
        for (int i = 0; i < queue.size() - 1; i++) {
            queue.add(queue.poll());
        }
    }

    public void pop() {
        queue.poll();
    }

    public int top() {
        return queue.peek();
    }

    public boolean empty() {
        return queue.isEmpty();
    }
}

```


***

## 155. Min Stack  

Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.





***








设计有getMin功能的栈

***

用递归函数和栈操作逆序一个栈

***

一个栈实现另一个栈的排序