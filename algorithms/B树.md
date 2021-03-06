

  * B树是多路平衡查找树，类似于二叉查找树，具有较快的索引速度，但是由于mysql数据库一般的数据比较多，不可能将所有数据都引入到内存，因此需要分批的加载
  磁盘上面的数据，就导致大量的IO操作，耗时很严重。
  * 为了减少这种IO的次数，引入胖矮的B树来代替瘦高的二叉查找树作为mysql的索引。
  
  m阶B-树的特征：
    
  1. 根节点至少有2个子女
  2. 每个中间节点（包括根）都包含k-1个元素和k个孩子，其中m/2<=k<=m(k-1个元素正好用来分割k个孩子)
  3. 每个叶子节点都包含k-1个元素，其中m/2<=k<=m
  4. 所有叶子节点位于同一层
  5 每个节点中的元素从小到大排列，节点中k-1个元素元素正好是k个孩子元素值域的划分
     
  * 非关系型数据库MongoDB就是利用B-树作为索引
  * 关系型数据库以B+树作为索引