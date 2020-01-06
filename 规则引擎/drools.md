## Drools

Drools 是一个基于RETE算法的，易于访问企业策略、易于调整以及易于管理的开源业务规则引擎，符合业内标准，速度快、效率高。Drools 是用Java语言编写的开放源码规则引擎，使用Rete算法对所编写的规则求值。Drools允许使用声明方式表达业务逻辑。可以使用非XML的本地语言编写规则，从而便于学习和理解。并且，还可以将Java代码直接嵌入到规则文件中。

### 基本概念

事实(Fact):对象之间及对象属性之间的关系

规则(Rule):由条件和结论构成的推理语句，一般表示为if……then。一个规则的if部分称为LHS，then部分称为RHS。

模式(module):就是指IF语句的条件。这里IF条件可能是有几个更小的条件组成的大条件。模式就是指的不能在继续分割下去的最小的原子条件。

### 规则语法drl

```java
package rules

import com.neo.drools.entity.Order

rule "zero"
    no-loop true
    lock-on-active true
    salience 1
    when
        $s : Order(amout <= 100)
    then
        $s.setScore(0);
        update($s);
end

rule "add100"
    no-loop true
    lock-on-active true
    salience 1
    when
        $s : Order(amout > 100 && amout <= 500)
    then
        $s.setScore(100);
        update($s);
end

rule "add500"
    no-loop true
    lock-on-active true
    salience 1
    when
        $s : Order(amout > 500 && amout <= 1000)
    then
        $s.setScore(500);
        update($s);
end

rule "add1000"
    no-loop true
    lock-on-active true
    salience 1
    when
        $s : Order(amout > 1000)
    then
        $s.setScore(1000);
        update($s);
end    
```



- package 与Java语言类似，drl的头部需要有package和import的声明,package不必和物理路径一致
- import 导出java Bean的完整路径，也可以将Java静态方法导入调用
- rule 规则名称，需要保持唯一 ,可以无限次执行。
- no-loop 定义当前的规则是否不允许多次循环执行,默认是 false,也就是当前的规则只要满足条件,可以无限次执行。
- lock-on-active 将lock-on-active属性的值设置为true,可避免因某些Fact对象被修改而使已经执行过的规则再次被激活执行。
- salience 用来设置规则执行的优先级,salience 属性的值是一个数字,数字越大执行优先级越高, 同时它的值可以是一个负数。默认情况下,规则的 salience 默认值为 0。如果不设置规则的 salience 属性,那么执行顺序是随机的。
- when 条件语句，就是当到达什么条件的时候
- then 根据条件的结果，来执行什么动作
- end 规则结束



####比较操作符

```
`>` `>=` `<` `<=` `==` `!=` `contains` `not contains` `memberOf` `not memberOf` `matches` `not matches
```



#### LHS语法

LHS是规则的条件部分的统称，由零到多条条件元素组成。如果LHS为空，默认为是条件部分一直为true。当一个新的WorkingMemory session创建的时候，会被激活和触发。

```java
rule "no CEs"
when
    // empty
then
    ... // actions (executed once)
end

// The above rule is internally rewritten as:

rule "eval(true)"
when
    eval( true )
then
    ... // actions (executed once)
end
```

LHS中的条件元素基于一个或多个模式，最常用的条件元素是`and`。当然如果LHS中有多个不互相连接的模式时，默认使用隐式的`and`。

```java
rule "2 unconnected patterns"
when
    Pattern1()
    Pattern2()
then
    ... // actions
end

// The above rule is internally rewritten as:

rule "2 and connected patterns"
when
    Pattern1()
    and Pattern2()
then
    ... // actions
end
```

##### **模式**

模式是最终要的条件元素，它可以隐式地匹配所有插入到WorkingMemory中的所有`fact`。一个模式具有0个或多个约束条件和一个可选的模式组合。

```java
Person()
```

这里的类型为Person，该模式意味着将匹配WorkingMemory中的所有Person对象。该类型不需要是一个真实 `fact`对象的类。模式可以指向超类甚至是接口，这样可以匹配多个不同类的`facts`。

```java
Object() // matches all objects in the working memory
```

为了引用匹配的对象，可以使用一个模式绑定参数如：`$p`。

```java
rule ...
when
    $p : Person()
then
    System.out.println( "Person " + $p );
end
```

##### **约束**

约束是一个返回`true`或`false`的表达式

```java
Person( 5 < 6 ) 
```

- Java Beans属性获取

  任何一个bean的属性都可以被直接使用

  ```java
  //use directly
  Person( age == 50 )
  
  // this is the same as:
  Person( getAge() == 50 )
  ```

- 嵌套的属性获取方式

  ```java
  //use directly
  Person( address.houseNumber == 50 )
  
  // this is the same as:
  Person( getAddress().getHouseNumber() == 50 )
  ```

- 逗号分隔符 AND

  ```java
  // Person is at least 50 and weighs at least 80 kg
  Person( age > 50, weight > 80 )
  
  // Person is at least 50, weighs at least 80 kg and is taller than 2 meter.
  Person( age > 50, weight > 80, height > 2 )
  ```

  逗号运算符不能出现在复合的约束表达式中

  ```java
  // Do NOT do this: compile error
  Person( ( age > 50, weight > 80 ) || height > 2 ) 
  
  // Use this instead
  Person( ( age > 50 && weight > 80 ) || height > 2 )
  ```

  

- 绑定变量

  属性值可以绑定到一个变量中

  ```java
  // 2 persons of the same age
  Person( $firstAge : age ) // binding
  Person( age == $firstAge ) // constraint expression
  ```

- 内联强制类型转换

  当处理嵌套对象时，往往需要将其转换成子类，可以通过使用`#`符号来完成

  ```java
  Person( name == "mark", address#LongAddress.country == "uk" )
  ```

  在该例子中将 `Address`转换成`LongAddress`。如果类型转换失败，该值会被认为是false。

  在同一个表达式中使用多级内联转换也是可行的

  ```java
  Person( name == "mark", address#LongAddress.country#DetailedCountry.population > 10000000 )
  
  ```

  Drools同样支持instanceof操作

  ```java
  Person( name == "mark", address instanceof LongAddress, address.country == "uk" )
  ```

  

#### RHS语法

