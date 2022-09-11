## 索引

官方定义：**索引（index）是帮助MySQL高效获取数据的数据结构**

索引是数据结构。

索引存储在文件系统种

索引的文件存储形式与存储引擎有关

索引文件的结构：hash、二叉树、B树、B+树

索引是数据的目录



 什么时候适用索引？

- 字段有唯一性限制的，比如商品编码；
- 经常用于 `WHERE` 查询条件的字段，这样能够提高整个表的查询速度，如果查询条件不是一个字段，可以建立联合索引。
- 经常用于 `GROUP BY` 和 `ORDER BY` 的字段，这样在查询的时候就不需要再去做一次排序了，因为我们都已经知道了建立索引之后在 B+Tree 中的记录都是排序好的。





### 字段类型分类索引

* 主键索引
  * 主键索引就是建立在主键字段上的索引，唯一的标识，主键不可重复，只能有一个列作为主键
* 唯一索引
  * 唯一索引建立在 UNIQUE 字段上的索引，避免重复的列出现，一张表可以有多个唯一索引，索引列的值必须唯一。
* 常规索引
  * 普通索引就是建立在普通字段上的索引，既不要求字段为主键，也不要求字段为 UNIQUE。
* 前缀索引
  * 前缀索引是指对字符类型字段的前几个字符建立的索引，而不是在整个字段上建立的索引，前缀索引可以建立在字段类型为 char、 varchar、binary、varbinary 的列上。







### 数据结构分类索引



#### B+ tree

MySQL 默认B+ tree

* B+树是B树的扩展，允许有效的插入，删除和搜索操作。

* 在B树中，键和记录都可以存储在内部节点和叶子节点中。 然而，在B+树中，记录(数据)只能存储在叶节点上，而内部节点只能存储键值。

* B+树的叶节点以单链表的形式链接在一起，以使搜索查询更有效。

* B+树用于存储无法存储在主存储器中的大量数据。 由于主存储器的大小总是有限的事实，B+树的内部节点(访问记录的键)存储在主存储器中，而叶节点存储在辅助存储器中。

优点：

1. 可以在相同数量的磁盘访问中获取记录。

2. 树高度保持平衡，与B树相比较少。

3. 可以顺序访问存储在B+树中的数据，也可以直接访问。

4. 键用于索引。

5. 更快的搜索查询，因为数据仅存储在叶节点上。



InnoDB 是在 MySQL 5.5 之后成为默认的 MySQL 存储引擎，B+Tree 索引类型也是 MySQL 存储引擎采用最多的索引类型。

创建表以后，InnoDB会根据情况选择不同的列作为主键

* 有主键，使用主键作为聚簇索引的索引键（key）

- 如果没有主键，就选择第一个不包含 NULL 值的唯一列作为聚簇索引的索引键（key）；
- 在上面两个都没有的情况下，InnoDB 将自动生成一个隐式自增 id 列作为聚簇索引的索引键（key）；

其它索引都属于辅助索引（Secondary Index），也被称为二级索引或非聚簇索引。

**创建的主键索引和二级索引默认使用的是 B+Tree 索引**。

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220810140729422.png" alt="image-20220810140729422" style="zoom:80%;" />

主键索引的 B+Tree 和二级索引的 B+Tree 区别如下：

- 主键索引的 B+Tree 的叶子节点存放的是实际数据，所有完整的用户记录都存放在主键索引的 B+Tree 的叶子节点里；
- 二级索引的 B+Tree 的叶子节点存放的是主键值，而不是实际数据。



二级索引查询会先检索二级索引中的 B+Tree 的索引值（商品编码，product_no），找到对应的叶子节点，然后获取主键值，然后再通过主键索引中的 B+Tree 树查询到对应的叶子节点，然后获取整行数据。**这个过程叫「回表」，也就是说要查两个 B+Tree 才能查到数据**。

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220810140851959.png" alt="image-20220810140851959" style="zoom:80%;" />



#### 数据结构之间对比

***1、B+Tree vs B Tree***

B+Tree 只在叶子节点存储数据，而 B 树 的非叶子节点也要存储数据，所以 B+Tree 的单个节点的数据量更小，在相同的磁盘 I/O 次数下，就能查询更多的节点。

另外，B+Tree 叶子节点采用的是双链表连接，适合 MySQL 中常见的基于范围的顺序查找，而 B 树无法做到这一点。

***2、B+Tree vs 二叉树***

对于有 N 个叶子节点的 B+Tree，其搜索复杂度为`O(logdN)`，其中 d 表示节点允许的最大子节点个数为 d 个。

在实际的应用当中， d 值是大于100的，这样就保证了，即使数据达到千万级别时，B+Tree 的高度依然维持在 3~4 层左右，也就是说一次数据查询操作只需要做 3~4 次的磁盘 I/O 操作就能查询到目标数据。

而二叉树的每个父节点的儿子节点个数只能是 2 个，意味着其搜索复杂度为 `O(logN)`，这已经比 B+Tree 高出不少，因此二叉树检索到目标数据所经历的磁盘 I/O 次数要更多。

***3、B+Tree vs Hash***

Hash 在做等值查询的时候效率贼快，搜索复杂度为 O(1)。

但是 Hash 表不适合做范围查询，它更适合做等值的查询，这也是 B+Tree 索引要比 Hash 表索引有着更广泛的适用场景的原因





### 什么时候需要/不需要索引

索引最大的好处是提高查询速度，但是索引也是有缺点的，比如：

- 需要占用物理空间，数量越大，占用空间越大；
- 创建索引和维护索引要耗费时间，这种时间随着数据量的增加而增大；
- 会降低表的增删改的效率，因为每次增删改索引，B+ 树为了维护索引有序性，都需要进行动态维护。



**什么时候适用索引？**

- 字段有唯一性限制的，比如商品编码；
- 经常用于 `WHERE` 查询条件的字段，这样能够提高整个表的查询速度，如果查询条件不是一个字段，可以建立联合索引。
- 经常用于 `GROUP BY` 和 `ORDER BY` 的字段，这样在查询的时候就不需要再去做一次排序了，因为我们都已经知道了建立索引之后在 B+Tree 中的记录都是排序好的



**什么时候不需要索引:**

1. `WHERE` 条件，`GROUP BY`，`ORDER BY` 里用不到的字段,起不到快速定位的字段,索引浪费物理空间

2. 字段中存在大量重复数据

3. 表数据太少的时候

4. 经常更新的字段,如果索引字段频繁修改,由于B+ tree的可序性,会导致频繁重建索引

   

**优化索引的方法:**

- 前缀索引优化

  使用某个字段中字符串的前几个字符建立索引

  

- 覆盖索引优化；

  覆盖索引是指 SQL 中 query 的所有字段，在索引 B+Tree 的叶子节点上都能找得到的那些索引，从二级索引中查询得到记录，而不需要通过聚簇索引查询获得，可以避免回表的操作。

  假设我们只需要查询商品的名称、价格，有什么方式可以避免回表呢？

  我们可以建立一个联合索引，即「商品ID、名称、价格」作为一个联合索引。如果索引中存在这些数据，查询将不会再次检索主键索引，从而避免回表。

  所以，使用覆盖索引的好处就是，不需要查询出包含整行记录的所有信息，也就减少了大量的 I/O 操作

  

- 主键索引最好是自增的；

  * **如果我们使用自增主键**，那么每次插入的新数据就会按顺序添加到当前索引节点的位置，不需要移动已有的数据，当页面写满，就会自动开辟一个新页面。因为每次**插入一条新记录，都是追加操作，不需要重新移动数据**
  * **如果我们使用非自增主键**，由于每次插入主键的索引值都是随机的，因此每次插入新的数据时，就可能会插入到现有数据页中间的某个位置，这将不得不移动其它数据来满足新数据的插入，甚至需要从一个页面复制数据到另外一个页面，我们通常将这种情况称为**页分裂**。**页分裂还有可能会造成大量的内存碎片，导致索引结构不紧凑，从而影响查询效率**。
  * 

- 防止索引失效；
  * 当我们使用左或者左右模糊匹配的时候，也就是 `like %xx` 或者 `like %xx%`这两种方式都会造成索引失效；
  * 当我们在查询条件中对索引列做了计算、函数、类型转换操作，这些情况下都会造成索引失效；
  * 联合索引要能正确使用需要遵循最左匹配原则，也就是按照最左优先的方式进行索引的匹配，否则就会导致索引失效。
  * 在 WHERE 子句中，如果在 OR 前的条件列是索引列，而在 OR 后的条件列不是索引列，那么索引会失效。



![image-20220828194804009](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220828194804009.png)



### InnoDB 是如何存储数据的？