```maven
# 为maven依赖包添加源码
mvn dependency:sources

# 打出指定groupId和artifactId的依赖关系
mvn dependency:tree -Dverbose -Dincludes=groupId:artifactId
```

