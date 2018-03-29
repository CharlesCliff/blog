title: mybatis misc
author: Charles Cliff
date: 2018-03-21 15:45:09
tags:
---
### mybatis 返回值
mybatis封装的insert delete update方法返回值默认为影响的行数，比如判断是否insert, delete or update成功，判断返回值是否大于0

### mysql java连接注意事项

1. mysql tinyint

	由于mysql中并没有bool类型，java中的bool类型都会被转化为tinyint, 默认情况下mysql表里的tinyint字段都会被转化为java的boolean类型，如果不想自动转为boolean， 需要在连接的url里添加配置`tinyInt1isBit=false`