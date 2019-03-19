####字符串

| 命令   | 用例                   | 描述                         |
| ------ | ---------------------- | ---------------------------- |
| incr   | incr key-name          | 将键值存储的值加1            |
| decr   | decr key-name          | 将键值存储的值减1            |
| incrby | incrby key-name amount | 将键值存储的值加上整数amount |
| decrby | decrby key-name amount | 将键值存储的值减去整数amount |

如果对一个不存在的键或者一个保存了空串执行自增或者自减操作，redis执行时会将这个键当成0来处理。

#### 列表

| 命令       | 用例                                 | 描述                                                         |
| ---------- | ------------------------------------ | ------------------------------------------------------------ |
| rpush      | rpush key value [value ……]           | 将一个或多个值推入列表的右端                                 |
| lpush      | lpush key value [value ……]           | 将一个或多个值推入列表的左端                                 |
| rpop       | rpop key-name                        | 移除并返回列表最右端的元素                                   |
| lpop       | lpop key-name                        | 移除并返回列表最左端的元素                                   |
| lindex     | lindex key-name offset               | 返回列表中偏移量为offset的元素                               |
| lrange     | lrange key-name start end            | 返回列表中从start偏移量到end偏移量的所有元素，包括start和end元素 |
| ltrim      | ltrim key-name start end             | 只保留从start偏移量到end偏移量范围内的元素，包括start和end元素 |
| blpop      | blpop key-name [key-name……] timeout  | 从第一个非空列表弹出位于最左端的元素，或者在timeout内阻塞并等待可弹出的元素出现 |
| brpop      | brpop key-name [[key-name……] timeout | 从第一个非空列表弹出位于最右端的元素，或者在timeout内阻塞并等待可弹出的元素出现 |
| rpoplpush  | rpoplpush source-key dest-key        | 从source-key中弹出列表的最右端元素，并将其推入dest-key的最左端，并返回这个元素 |
| brpoplpush | brpoplpush source-key dest-key       | 从source-key中弹出列表的最右端元素，并将其推入dest-key的最左端，并返回这个元素，如果source-key为空，那么在timeout内阻塞并等待可弹出元素的出现 |



#### 集合

| 命令        | 用例                            | 描述                                                         |
| ----------- | ------------------------------- | ------------------------------------------------------------ |
| sadd        | sadd key-name item [item……]     | 将一个或多个元素加入集合中，并返回被添加元素中原本并不存在集合的元素数量 |
| srem        | srem key-name item [item……]     | 从集合中移除一个或多个元素，并返回移除元素的数量             |
| sismember   | sismember key-name item         | 判断item是否存在于集合中                                     |
| scard       | scard key-name                  | 返回元素中的集合个数                                         |
| srandmember | srandmember key-name [count]    | 从集合中随机返回一个或多个元素，当count为正数，返回的元素不会重复，当count为负数，返回的元素可能会重复 |
| smembers    | smembers key-name               | 返回集合中的所有元素                                         |
| spop        | spop key-name                   | 从集合中随机移除一个元素，并返回                             |
| smove       | smove source-key  dest-key item | 如果集合source-key中包含元素item，那么从source-key中移除item，并将元素item添加到des-key中，如果item移除成功返回1，否则返回0 |
| sdiff       | sdiff key-name [key-name……]     | 计算集合元素的差集                                           |
| sinter      | sinterkey-name [key-name……]     | 计算集合元素的交集                                           |
| sunion      | sunion key-name [key-name……]    | 计算集合元素的并集                                           |

#### 散列

| 命令    | 用例                           | 描述                           |
| ------- | ------------------------------ | ------------------------------ |
| hmget   | hmget key-name key [key……]     | 从散列里面获取一个或多个值     |
| hmset   | hmset key-name key  value      | 为散列表里面的键设置值         |
| hdel    | hdel key-name key              | 删除散列表中的键值对           |
| hlen    | hlen key-name                  | 返回散列表键值对数量           |
| hincrby | hincrby key-name key increment | 将key存储的值加上整数increment |
| hexists | hexists key-name key           | 检查指定的key是否存在散列表中  |

#### 有序列表

| 命令    | 用例                              | 描述                                               |
| ------- | --------------------------------- | -------------------------------------------------- |
| zadd    | zadd key-name score member        | 将带有给定分之的成员加入有序集成中                 |
| zrem    | zrem key-name  key                | 从有序集合中移除指定的成员，并返回被移除成员的数量 |
| zcard   | zcard key-name                    | 返回有序列表的成员数量                             |
| zincrby | zincrby key-name increment member | 将member成员的分值加上increment                    |
| zcount  | zcount key-name min max           | 返回分值介于min max之间的成员数量                  |
| zrank   | zrank key-name member             | 返回member在有序集成中的排名，从0开始              |
| zrange  | zrange key-name start stop        | 返回有序集合排名介于start stop之间的成员           |
| zscore  | zscore key-name member            | 返回成元member的分值                               |
|         |                                   |                                                    |

