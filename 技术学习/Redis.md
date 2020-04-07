# 常用命令

模糊查key  keys rbox:search:brand:*

	get rbox:search:category:1000

​			
zset			
zrange rbox:search:matrix:category:1694 0 -1

输出带分数的结果为
zrange rbox:search:matrix:category:1694 0 -1 withscores

