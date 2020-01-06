> Rete算法是目前效率最高的一个Forward-Chaining推理算法，Drools项目是Rete算法的一个面向对象的Java实现。
> 规则引擎的推理步骤如下：
> 1. 将初始数据（fact）输入Working Memory。
> 2. 使用Pattern Matcher比较规则（rule）和数据（fact）。
> 3. 如果执行规则存在冲突（conflict），即同时激活了多个规则，将冲突的规则放入冲突集合。
> 4. 解决冲突，将激活的规则按顺序放入Agenda。
> 5. 使用规则引擎执行Agenda中的规则。重复步骤2至5，直到执行完毕所有Agenda中的规则。

#### KieServices

kie整体的入口,可以用来创建Container,resource,fileSystem

####KieModule

是一个包含了多个`KieBase`定义的容器。一般用kmodule.xml来表示。

#### KieBase

提供的用来收集应用当中知识（knowledge）定义的知识库对象，在一个 `KieBase` 当中可以包含普通的规则（rule） 、规则流(rule flow)、函数定义(function)、用户自定义对象（type model）等。在Drools中主要就是规则和方法，KieBase本身并不包含运行时的数据之类的，如果需要执行规则`KieBase`中的规则的话，就需要根据`KieBase`创建`KieSession`

#### KieFileSystem

一个完整的文件系统,包括资源和组织结构

#### KieBuilder

用来在业务代码当中收集已经编写好的规则，然后对这些规则文件进行编译，最终产生一批编译好的规则包
（`KnowledgePackage`）给其它的应用程序使用。`KieBuilder` 在编译规则的时候可以通过其提供hasErrors()方法得到编译规则过程中发现规则是否有错误，如果有的话通过其提供的 getErrors()方法将错误打印出来，以帮助我们找到规则当中的错误信息。

####KieContainer

 `KieContainer`就是一个`KieBase`的容器，可以根据kmodule.xml 里描述的KieBase信息来获取具体的`KieSession`

#### KieSession

`KieSession`就是一个跟Drools引擎打交道的会话，其基于`KieBase`创建，它会包含运行时数据，包含“事实 Fact”，并对运行时数据事实进行规则运算。

##### StatefulRuleSession

`StatefulRuleSession`的`Working Memory`能够在多个方法调用期间保存状态。你可以在多个方法调用期间在`Working Memory`中加入多个对象，然后执行引擎，接下来还可以加入更多的对象并再次执行引擎。

#####StatelessRuleSession

`StatelessRuleSession`类是不保存状态的，为了执行它的executeRules方法，你必须为`Working Memory`提供所有的初始数据，执行规则引擎，得到一个内容列表作为返回值。

