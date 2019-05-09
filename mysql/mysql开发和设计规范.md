
##  表设计规范

###  表

1)   表名不能包含tmp、temp、bak和数字

2)   每张表必须包含created_time, updated_time, creator, updator, delete_flag, remark  六个列 
      1. 使用 creator 和 updator 记录“更新人”和“创建人”的信息，使用delete_flag实现逻辑删除，使用remark记录备注信息
      2. 但是，不要创建2个日期列去表示同一个业务含义（例如，字段类型为DATE和DATETIME的2个列，都代表修改日期）

3)   created_time（创建日期），默认使用timestamp类型，默认值为current_timestamp
      updated_time（修改日期），默认使用timestamp类型，默认值为current_timestamp，使用on update current_timestamp属性

CREATE TABLE crm_member (
id BIGINT UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '主键',
mid BIGINT UNSIGNED NOT NULL COMMENT '会员ID',
mb_name VARCHAR ( 50 ) NOT NULL COMMENT '会员名字',
**delete_flag INT NOT NULL DEFAULT 1 COMMENT '记录状态，1-启用；0-失效',**
**remark VARCHAR ( 100 ) NOT NULL DEFAULT '' COMMENT '记录描述，对记录进行描述和备注',**
**creator VARCHAR ( 50 ) NOT NULL DEFAULT '' COMMENT '记录创建人姓名',**
**updator VARCHAR ( 50 ) NOT NULL DEFAULT '' COMMENT '记录更新人姓名',**
**created_time TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',**
**updated_time TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',**
PRIMARY KEY ( id ),
UNIQUE KEY uk_mid ( mid ) 
) ENGINE = INNODB COMMENT = 'crm会员表';

###  主键

1)   每个表必须有主键 
      1. 主键应定义为bigint unsigned not null auto_increment
      2. 当有特殊需求，使用huaxia.common中的类时，详细情况可以跟时蕾、张永冬确认。   参考文档：[华夏信财MySQL开发规范](http://192.168.15.88:8090/pages/viewpage.action?pageId=2982531)

2)   核心业务表必须设计业务主键列，使用唯一索引约束。

CREATE TABLE `crm_member` (
  **`id`  bigint unsigned NOT NULL AUTO_INCREMENT COMMENT '****主键****',**  **`mid` bigint unsigned NOT NULL COMMENT '****会员****ID',**  `mb_name`  varchar(50) NOT NULL COMMENT '会员名字',
  `created_time` timestamp NOT NULL DEFAULT current_timestamp COMMENT '创建时间',
  `updated_time` timestamp NOT NULL DEFAULT current_timestamp ON UPDATE current_timestamp COMMENT '更新时间',
  **PRIMARY KEY (`id`)**,
  **UNIQUE KEY `uk_mid` (`mid`)**) ENGINE=InnoDB COMMENT='crm会员表';

### 字段

1)   字段命名全部小写，单词之间使用"_"分隔，必须要有注释说明；

2)   单表字段数最多不大于50个
      涉及到核心交易的表强烈建议最多持有30个字段，例如资产、投标、预付、支付流水、代收代付、分账、冻结、赎回、对账、掉单、清分、结算；

3)   为每个字段定义NOT NULL 和 DEFAULT属性；

4)   使用通用数据类型，不建议使用小众数据类型（如enum,set, bit,real等）；

5)   存储相同数据时，在多张表中的列名和列类型必须一致（如 在多个表中的用户ID字段，名称都叫user_id，它们类型必须一致）；

6)   禁用TEXT, BLOB字段， 禁止存储json类型的数据，如有需求请出门右转找MongoDB；

7)   金额统一使用 decimal(20,8)类型；

8)   枚举字段 统一使用int类型，其枚举值须维护在相关配置表中；

###  数据类型

**1)**   **Datetime和Timestamp**

​      1. 需要精确到时间（时分秒）的字段使用DATETIME类型。
      2. 日期、时间类型的字段不能使用VARCHAR类型，统一使用DATE、DATETIME或者TIME字段类型存放。 

**2)**   **VARCHAR**

​      1. InnoDB存储引擎下，全部使用VARCHAR类型，不允许使用CHAR类型；

​      2. 原则上单个VARCHAR字段不允许超过200个字符；
              确实需要超过200个字符时，须和DBA确认后才可以使用

​      3. varchar类型的长度，挑选下面的几种使用，不要出现别的长度
          代码类：
                 代码（短）使用varchar(50)
                 代码（中）使用varchar(100)
                 代码（长）使用varchar(200)
          描述类：
                 描述（短）使用varchar(100)
                 描述（中）使用varchar(300)
                 描述（长）使用varchar(500)

**3)**   **DECIMAL**

​      对于精确浮点型数据存储，使用DECIMAL，禁用FLOAT和DOUBLE。

4)   禁用ENUM、SET类型，使用SMALLINT/TINYINT来代替。

## 索引设计规范

1)   索引以"idx_字段名称"命名，唯一索引以"uk_字段名称"命名；

2)   单表的索引数量应控制在5个以内，每个组合索引不要超过3个字段；

3)   禁止重复创建索引，例如先后为 id 列创建主键 pk_id 和普通索引 idx_id ；

4)   禁止对过长的VARCHAR类型字段建立索引；

5)   组合索引命名应注意字段顺序，需将选择性较高的字段和经常用到的字段放在前面；

6)   禁止使用外键，应由程序保证参照完整性；

7)   ORDER BY, GROUP BY, DISTINCT的字段，建议使用索引字段，禁用order by rand() 。

##  视图设计规范

1. 原则上涉及到核心交易的禁用视图，其它功能不建议使用视图；   如确实需要使用视图，请与DBA协商并充分评估性能。
2. 视图统一使用"v_模块简写_视图名简写"格式命名

##  敏感信息加密

涉及用户身份证号、手机号、社保账号、银行卡号、详细住址、邮箱等敏感信息，需要加密后存储。针对用户密码，需要保证加密后不可解密。


## 开发规范

## 对表不能做delete操作（而且也没有delete权限），关键业务表要保留修改日志

##  Alter使用规范

1)   在使用alter修改列的属性时，所做的修改是覆盖性的，因此必须将列的原有属性罗列出来，否则会造成属性丢失。  但是，不要指定 first 或者 after column_name属性

2)   在同一张表中，添加多列，或者修改多列时，使用合并书写的形式
      例如，不要使用如下写法： 
                                               alter table table_name1 modify column_name1 ... ;
                                               alter table table_name1 modify column_name2 ... ;
                                               alter table table_name1 modify column_name3 ... ;
                       使用如下写法：
                                               alter table table_name1 modify column_name1 ... , modify column_name2 ... , modify column_name3 ... ;

##  Select使用规范

1)   禁用select *，必须按需要枚举字段名；

2)   select后面必须要有where条件或limit进行限制；

3)   若业务明知只有1行，须用limit 1进行限制（如能精确知道N行就必须用limit N限制）；

4)   select进行分页查询，当limit起点较高时，可先用过滤条件进行过滤。

​      select a,b,c from tab_name limit **10000**,**20**;
      优化为：
      select a,b,c from tab_name where id>**10000** limit **20**;  

##  Insert使用规范

1)   Insert时需要把字段名列出来，禁止使用下面的书写形式

insert into tab values(...);

 

2)   推荐将insert合并书写（如下所示），但values的个数不宜超过1000个。

insert into tab_name(name,sex) values(...),(...);

 

3)   不推荐使用如下的书写形式

insert into tab_name(name,sex) value(...);
insert into tab_name(name,sex) value(...); 

##  Update使用规范

1)   update表的数据量超过5万条的时候要用批量提交

##  Where使用规范

1)   在where条件中出现的列上，不建议对列本身使用任何计算、转换；

2)   union 替换为 or 或者 union all；

3)   使用 in 时，in 子句中元素建议小于500。

##  join使用规范

多表join时注意比较字段类型一致，避免隐式类型转换； 

##  事务规范

事务涉及的表必须全部是InnoDB表；

事务里批量更新数据需要控制数量，做到少量多次。 

##  其他规范

进行大批量操作时必须分批提交，每次数据量操作不能超过10万条

减少使用order by, goup by, distinct等较为消耗CPU的语句，可以将其放入程序端去做

禁用update/delete...limit N结构的SQL