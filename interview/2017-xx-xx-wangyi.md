# 网易一二面+HR面

* 介绍一下项目，说说为啥要用mongodb。
* mongodb是如何进行表连接。
    * 直接使用mongodb的区别，则使用`lookup()`, 这是关系型数据库的左外连接方法。
    * 使用mongoose的话，使用`population()`, [populate的使用方法](http://blog.csdn.net/hellochenlu/article/details/50467563)
* 关系型数据库的表连接方式: [看这里](http://www.cnblogs.com/rush/archive/2012/03/27/2420246.html)
    * 内连接: 满足交换律, `A inner join B`等价于`B inner join A`, 并且只显示查询存在的数据。
        ```
        SELECT DISTINCT College.cName, College.enrollment
        FROM  College INNER JOIN
                Apply ON College.cName = Apply.cName
        ```
    * 外连接:
        * 左外连接: 产生左边表的完全集，右边的表，若匹配则有值，若不匹配，则显示为NULL。
            ```
            SELECT College.cName, College.state, College.enrollment,
            Apply.cName, Apply.major, Apply.decision
            FROM  College LEFT OUTER JOIN
                    Apply ON College.cName = Apply.cName
            ```
        * 完全外部连接: 即产生左边表的完全集以及右边表的完全集。完整外部联接（full outer join）满足交换律：“A full outer join B” 和 “B full outer join A” 是相等的。
            ```
            SELECT College.cName, College.state, College.enrollment,
            Apply.cName, Apply.major, Apply.decision
            FROM  College FULL OUTER JOIN
                    Apply ON College.cName = Apply.cName
            ```
        * 右外连接: 与左外连接同理。
    * 交叉连接: 会产生笛卡尔积(M*N), 在需要所有数据的情况下会进行交叉连接。
        ```
        SELECT College.cName, College.state, College.enrollment,
        Apply.cName, Apply.major, Apply.decision
        FROM College
        CROSS JOIN Apply
        ```
* 三大范式: [看这里](http://aijuans.iteye.com/blog/1629645)
    * 函数依赖: 如果一个表中某一个字段Y的值是由另外一个字段或一组字段X的值来确定的，就称为Y函数依赖于X。
    * 1NF: 原子性，字段不可再分割。
    * 2NF: 在1NF的基础上, 每个非主属性是由整个主键函数决定的，而不能由主键的一部分来决定。即主键(student+major) => 教师／教材，但是major => 教材，因此不满足第二范式。
    * 3NF: 在2NF的基础上, 如果非主属性之间存在了函数依赖，就会存在传递依赖，这样就不满足第三范式。
* 数据库设计优化。
* 两列布局。
* es6实现类继承。
* es5，es6面向对象的写法。
* 写一个函数，每5s自己调用自己, 100次结束。
* node如何利用多核CPU。
    * 每个进程使用一个cpu，那么多个进程就可以使用多个cpu了。
    * 因此，只需要开多个进程就可以了。
    * 创建子进程的模块: child_process
    * 复制子进程: fork()方法
* cookie和session的优点缺点。
* https。
* md5实现原理。
    * 是不可逆的。
    * 很多网站数据库里存放的是密码的md5值。
    * 但是md5还是很容易被破解的，因此有了加盐一说法。
    * [关于加盐可以看这里](http://blog.csdn.net/blade2001/article/details/6341078)
* 用过哪些node的原生模块。
* 说说关于打包工具。
* 轮播组件。
* Select组件。
* 1000个数找到2个数和为100的所有数。
* 什么是对称加密。
* 什么是替换元素和非替换元素，它们之间有什么区别。
* 写一个工具函数用于判断类型。
* hr面聊人生

由于实习的时候写后端写的比较多，所以一面面试官问了一些数据库的和node的知识，数据库的都忘的差不多了。。。node也没有怎么用过原生模块，都是用的框架，所以一面问了很多这方面的知识，自己答的不好。so gg..



