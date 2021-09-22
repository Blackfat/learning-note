####redis对象结构

```c
typeof struct redisObject{
// 数据类型
unsigned type;
// 编码 
unsigned encoding;
// 底层实现数据结构的指针  
void *ptr;  
}
```



#### 编码和底层实现

| 编码常量                  | 底层数据结构               |
| ------------------------- | -------------------------- |
| REDIS_ENCODING_INT        | long类型的整数             |
| REDIS_ENCODING_EMBSTR     | embstr编码的简单动态字符串 |
| REDIS_ENCODING_RAW        | 简单动态字符串             |
| REDIS_ENCODING_HT         | 字典                       |
| REDIS_ENCODING_LINKEDLIST | 双端链表                   |
| REDIS_ENCODING_ZIPLIST    | 压缩链表                   |
| REDIS_ENCODING_INTSET     | 整数集合                   |
| REDIS_ENCODING_SKIPLIST   | 跳跃表和字典               |



| 类型         | 编码                      |
| ------------ | ------------------------- |
| REDIS_STRING | REDIS_ENCODING_INT        |
| REDIS_STRING | REDIS_ENCODING_EMBSTR     |
| REDIS_STRING | REDIS_ENCODING_RAW        |
| REDIS_LIST   | REDIS_ENCODING_ZIPLIST    |
| REDIS_LIST   | REDIS_ENCODING_LINKEDLIST |
| REDIS_HASH   | REDIS_ENCODING_ZIPLIST    |
| REDIS_HASH   | REDIS_ENCODING_HT         |
| REDIS_SET    | REDIS_ENCODING_HT         |
| REDIS_SET    | REDIS_ENCODING_INTSET     |
| REDIS_ZSET   | REDIS_ENCODING_ZIPLIST    |
| REDIS_ZSET   | REDIS_ENCODING_SKIPLIST   |

编码转换

REDIS_ENCODING_INT->REDIS_ENCODING_RAW

- 向对象执行了一些命令，使得这个对象保存的不再是整数值，而是一个字符串值，那么字符串对象的编码将从int变为raw。


REDIS_ENCODING_EMBSTR->REDIS_ENCODING_RAW

- 大于44字节的字符串会是用REDIS_ENCODING_RAW，否则使用REDIS_ENCODING_EMBSTR。但当我们对embstr编码的字符串对象执行任何修改命令时，程序会先将对象的编码从embstr转换成raw，然后再执行修改命令。REDIS_ENCODING_EMBSTR编码的字符串修改后总会变成一个REDIS_ENCODING_RAW编码的字符串。

embstr与raw都使用redisObject和sds保存数据，区别在于，embstr的使用只分配一次内存空间（因此redisObject和sds是连续的），而raw需要分配两次内存空间（分别为redisObject和sds分配空间）。因此与raw相比，embstr的好处在于创建时少分配一次空间，删除时少释放一次空间，以及对象的所有数据连在一起，寻找方便。而embstr的坏处也很明显，如果字符串的长度增加需要重新分配内存时，整个redisObject和sds都需要重新分配空间，因此redis中的embstr实现为只读。

ZIPLIST->LINKEDLIST

- 列表对象保存的所有字符串元素的长度都小于64字节

- 列表对象保存元素的个数小于512个

不能满足这两个条件的列表对象需要使用linkedlist编码

ZIPLIST->HT

- 哈希对象保存的所有键值对的键和值的字符串长度都小于64字节

- 哈希对象保存的键值对数量小于512个

不能满足这两个条件的列表对象需要使用hashtable编码

INTSET->HT

- 集合对象保存的所有元素都是整数值

- 集合对象保存的元素数量不超过512个

不能满足这两个条件的集合对象需要使用hashtable编码

ZIPLIST-> SKIPLIST

- 有序集合元素小于128个

- 有序集合保存的所有元素成员的长度都小于64字节

不能满足这两个条件的集合对象需要使用skiplist编码

  