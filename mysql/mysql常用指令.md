#### 查看隔离级别

```mysql
SELECT @@tx_isolation               ---默认REPEATABLE-READ
```

#### 查看binlog日志模式

```mysql
show variables like '%binlog_format%'; -- 默认STATEMENT
```

