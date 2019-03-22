# 查询条件on、where、having区别
## ON 和WHERE

所有的查询都回产生一个中间临时报表，查询结果就是从返回临时报表中得到。ON和WHERE后面所跟限制条件的区别，主要与限制条件起作用的时机有关，ON根据限制条件对数据库记录进行过滤，然后生产临时表；而WHERE是在临时表生产之后，根据限制条件从临时表中筛选结果。

因为以上原因，ON和WHERE的区别主要有下：

1）返回结果：在左外（右外）连接中，ON会返回左表（右表）中的所有记录；而WHERE中，此时相当于inner join，只会返回满足条件的记录（因为是从临时表中筛选，会过滤掉不满足条件的）。

2）速度：因为ON限制条件发生时间较早，临时表的数据集要小，因此ON的性能要优于WHERE。

## HAVING和WHERE

HAVING和WHERE的区别也是与限制条件起作用时机有关，HAVING是在聚集函数计算结果出来之后筛选结果，查询结果只返回符合条件的分组，HAVING不能单独出现，只能出现在GROUP BY子句中。；而WHERE是在计算之前筛选结果，如果聚集函数使用WHERE，那么聚集函数只计算满足WHERE子句限制条件的数据,例如：

在使用和功能上，HAVING和WHERE有以下区别：

1）HAVING不能单独出现，只能出现在GROUP BY子句之中；WHERE即可以和SELECT等其他子句搭配使用，也可以和GROUP BY子句搭配使用，WHERE的优先级要高于聚合函数高于HAVING。

2）因为WHERE在聚集函数之前筛选数据，HAVING在计算之后筛选分组，因此WHERE的查询速度要比HAVING的查询速度快。

3.总结

ON、WHERE、HAVING的主要差别是其子句中限制条件起作用时机引起的，ON是在生产临时表之前根据条件筛选记录，WHERE是从生产的临时表中筛选数据，而HAVING是对临时表中满足条件的数据，进行计算分组之后，通过HAVING限制语句筛选分组，返回结果是满足HAVING子句限制的分组。

## 参考
- [1] [查询条件on、where、having区别](https://blog.csdn.net/weixin_39805338/article/details/80798763)