# Spring
..
# SpringMVC
..
# Mybatis

## 1. 关于 mybatis 返回单一对象或对象列表的问题

- 返回数据类型由 dao 中的接口和 map.xml 文件共同决定。另外，不论是返回单一对象还是对象列表，***map.xml 中的配置都是一样的，都是 resultMap=”***Map” 或 resultType=“* .* .*” 类型.

- 每一次 mybatis 从数据库中 select 数据之后，都会检查数据条数和 dao 中定义的返回值是否匹配。

- 若返回一条数据，dao 中定义的返回值是一个对象或对象的 List 列表，则可以正常匹配，将查询的数据按照 dao 中定义的返回值存放。

- 若返回多条数据，dao 中定义的返回值是一个对象，则无法将多条数据映射为一个对象，此时 mybatis 报错。
