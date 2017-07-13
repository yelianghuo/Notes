heapq : 堆队列算法
==========

**Source code:** Lib/heapq.py

介绍
-----

* 这个模块提供了堆队列算法的实现，也称为优先队列算法。
    
* 堆是一个特殊的二叉树（本文档中指的小顶堆，对应还有大顶堆），它的每个父结点的值小于等于它的两个孩子结点的值。
该文中的堆是基于数组来实现的静态二叉树，对于数组中的每个元素
 
*`heapq.merge(*iterables, key=None, reverse=False)`*
  
  * 合并多个已排序(升序)的序列，返回一个可迭代对象
  * 功能和`sorted(itertools.chain(*iterables))`类似，但是它返回的是可迭代对象，它不会将所有的数据一次性放到内存里面，并且以输入的序列必须是
  排序、升序为前提
  * 有两个可选参数：    
    * key参数制定了一个key函数，该函数用来指定输入序列中用来排序的元素；
    * reverse是一个bool值，若为true，则合并之后的元素倒排
**eg：**
  ``` 
  a = [3, 1, 5, 7]
  b = [2, 4, 6, 8]
  c = merge(a, b)    
  for item in c:
      print(item, sep=" ", end=" "
  output:
    1 2 3 4 5 6 7 8 
  
  
  ```
 
基础的例子
-------

* 堆排序可以通过将所有的元素压入堆中，然后每次从小顶堆中取出最小的元素（即二叉树中的根节点）：
   
   ```
    def heapsort(iterable):
    h = []
    for value in iterable:
        heappush(h, value)
    return [heappop(h) for i in range(len(h))]

    heapsort([1, 3, 5, 7, 9, 2, 4, 6, 8, 0])
    ```
* 上述的堆排序和内置`sorted(iterable)`函数很像，但是该堆排序不是稳定的，而`sorted(iterable)`是稳定的排序；

* 堆元素可以是元组。这可以用于像任务优先级中，选取某个属性来作为当前的比较值：
    
    ```
    h = []
    heappush(h, (5, 'write code'))
    heappush(h, (7, 'release product'))
    heappush(h, (1, 'write spec'))
    heappush(h, (3, 'create tests'))
    heappop(h)
    ```
    