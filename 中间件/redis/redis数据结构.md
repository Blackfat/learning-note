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

  