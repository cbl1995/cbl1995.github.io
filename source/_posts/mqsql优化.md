1. B-tree索引 is null不会走,is not null会走,位图索引 is null,is not null 都会走。

2. mysql索引查询会遵循最左匹配的原则，当我们创建了一个联合索引例如index(a,b,c,d),其实相当与创建了index1(a),index2(a,b),index3(a,b,c),index4(a,b,c,d)。

   1. 如果查找不从索引列的最左边开始，索引无法使用。
   2. 不能跳过索引中的列。
   3. 联合索引中的排序，必须报纸一致，否则索引也会失效。

   |             sql语句              | 索引是否有效 |
   | :------------------------------: | :----------: |
   |            where a=1             |     true     |
   |            where  b=1            |    false     |
   |        where a=1 and b>2         |     true     |
   |        where a=1 and c>3         |    false     |
   | where a=1 and b in (2,3) and c<3 |     true     |
   |   where a=1 order by b,c desc    |     true     |
   | where a=1 order by b asc,c desc  |    false     |

3. 不要使用not in，不走索引。

4. union和uniona all,尽量使用union all，两者的区别是union会对相同的数据去重，union all不会。