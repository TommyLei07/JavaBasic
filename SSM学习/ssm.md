# Spring
..
# SpringMVC
..
# Mybatis

## 1. 关于 Mybatis 返回单一对象或对象列表的问题

- 返回数据类型由 dao 中的接口和 map.xml 文件共同决定。另外，不论是返回单一对象还是对象列表，***map.xml 中的配置都是一样的，都是 resultMap=”***Map” 或 resultType=“* .* .*” 类型.

- 每一次 mybatis 从数据库中 select 数据之后，都会检查数据条数和 dao 中定义的返回值是否匹配。

- 若返回一条数据，dao 中定义的返回值是一个对象或对象的 List 列表，则可以正常匹配，将查询的数据按照 dao 中定义的返回值存放。

- 若返回多条数据，dao 中定义的返回值是一个对象，则无法将多条数据映射为一个对象，此时 mybatis 报错。

## 2.Mybatis获取自增主键

今天做网上的一个实战项目的时候，突然发现一个问题。当Mybatis进行`insert`或`update`操作时，可以自动获取自增主键id。
```
 orderItemService.add(oi);
 oiid=oi.getId();
```
就比如上面这个代码，当向数据库中插入一条新的记录后，可以直接通过`oi.getId()`获取新记录的id。之前都是照着用，今天突然一下就很疑惑Mybatis是怎么做到的。

```
<mapper namespace="userMapper">
    <insert id="addUser" parameterType="userScope" useGeneratedKeys="true" keyProperty="id">
        insert user(user_name,pass_word,address) values (#{userName},#{passWord},#{address})
    </insert>
    <insert id="insertSelectKey" parameterType="userScope">
        <selectKey keyProperty="id" resultType="int">
            select LAST_INSERT_ID()
        </selectKey>
        insert user(user_name,pass_word,address) values (#{userName},#{passWord},#{address})
    </insert>
</mapper>
```
在`Mapper.xml`中可以通过两种方式来实现获取自增长主键。一种是使用 `useGemeratedKeys`，一种是使用 `<selectKey/>` 的方式。这两种方式获取自增长主键的 ID 的方式是一样的。都是从参数对象中获取自增长主键值。下面我们来根据源码来看一下 MyBatis 是怎么做的。  

Mybatis都是将`mappper`类扫描后，通过组装`(BeanDefinition)`成 `MapperFactoryBean.class`，然后最后通过`MapperProxy.class`(代理) 将`mapper`实例化，给人的体验就是可以直接调用mapper操作数据库。一个insert执行顺序:
1. UserMapper.insert();
2. MapperProxy.invoke();
3. mapperMethod.execute();
4. DefaultSqlSession.insert();
5. BaseExecutor.update()
6. SimpleExecutor.doUpdate();
7. StatementHandler.update();
8. PreparedStatementHandler.update();

在8中就可以发现执行完sql 后，调用的KeyGenerator

**`PreparedStatementHandler`**

```
public class PreparedStatementHandler extends BaseStatementHandler {
    public int update(Statement statement) throws SQLException {
        PreparedStatement ps = (PreparedStatement) statement;
        ps.execute();//这里是调用的JDBC4PreparedStatement执行sql
        int rows = ps.getUpdateCount();//获取受影响的行数，也就是调用插入操作的时候返回的真正值。
        Object parameterObject = boundSql.getParameterObject();//获取参数对象
        KeyGenerator keyGenerator = mappedStatement.getKeyGenerator();//判断是那种KeyGenerator
        keyGenerator.processAfter(executor, mappedStatement, ps, parameterObject);//根据KeyGenerator去set主键值
        return rows;
    }
}

```
![](https://img-blog.csdn.net/20161210170643023?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemtueHg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**`KeyGenerator`**

![](https://img-blog.csdn.net/20161210170424475?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemtueHg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

- 我们可以看到 KeyGenerator 有三个实现类：`NoKeyGenerator`、`Jdbc3KeyGenerator`、`SelectKeyGenerator`。  
- `NoKeyGenerator` 表示是没有设置返回自增长主键值的插入操作,`Jdbc3KeyGenerator` 表示处理 `useGeneratedKeys` 的方式组装 sql，`SelectKeyGenerator` 是使用 `<SelectKey/>` 的方式组装 sql 结果。
- 下面我们来分析 `Jdbc3KeyGenerator` 这个类。

```
public class Jdbc3KeyGenerator implements KeyGenerator {

    public void processBatch(MappedStatement ms, Statement stmt, Collection<Object> parameters) {
        ResultSet rs = null;
        try {
            //这里获取到执行的sql结果 ， 获取主键的字段值(实体类的映射)，然后set进去很简单
            rs = stmt.getGeneratedKeys();//获取执行结果值
            final Configuration configuration = ms.getConfiguration();
            final TypeHandlerRegistry typeHandlerRegistry = configuration.getTypeHandlerRegistry();
            final String[] keyProperties = ms.getKeyProperties();//获取设置的keyProperty
            final ResultSetMetaData rsmd = rs.getMetaData();
            TypeHandler<?>[] typeHandlers = null;
            if (keyProperties != null && rsmd.getColumnCount() >= keyProperties.length) {//如果设置了keyProperty，则在参数对象中放入处理之后的结果值
                for (Object parameter : parameters) {
                // there should be one row for each statement (also one for each parameter)
                    if (!rs.next()) {
                        break;
                    }
                    final MetaObject metaParam = configuration.newMetaObject(parameter);
                    if (typeHandlers == null) {
                        typeHandlers = getTypeHandlers(typeHandlerRegistry, metaParam, keyProperties, rsmd);//keyProperty设置的字段类型的处理器
                    }
                    populateKeys(rs, metaParam, keyProperties, typeHandlers);//在参数对象中设置keyProperty的字段值
                }
            }
        }
    }
    private void populateKeys(ResultSet rs, MetaObject metaParam, String[] keyProperties, TypeHandler<?>[] typeHandlers) throws SQLException {
        for (int i = 0; i < keyProperties.length; i++) {
            TypeHandler<?> th = typeHandlers[i];
            if (th != null) {
                Object value = th.getResult(rs, i + 1);
                metaParam.setValue(keyProperties[i], value);//在参数对象中设置keyProperty的字段值
            }
        }
    }

    public void setValue(String name, Object value) {
        PropertyTokenizer prop = new PropertyTokenizer(name);
        if (prop.hasNext()) {
            MetaObject metaValue = metaObjectForProperty(prop.getIndexedName());
            if (metaValue == SystemMetaObject.NULL_META_OBJECT) {
                if (value == null && prop.getChildren() != null) {
                // don't instantiate child path if value is null
                return;
                } else {
                metaValue = objectWrapper.instantiatePropertyValue(name, prop, objectFactory);
                }
            }
            metaValue.setValue(prop.getChildren(), value);
        } else {
        objectWrapper.set(prop, value);//在参数对象中设置keyProperty的字段值（根据反射来操作的）
        }
    }


}
```

**上面的 objectWrapper 为 BeanWrapper，我们直接进入到 BeanWrapper 中。**

```
  private void setBeanProperty(PropertyTokenizer prop, Object object, Object value) {
    try {
      Invoker method = metaClass.getSetInvoker(prop.getName());
      Object[] params = {value};
      try {
        method.invoke(object, params);//设置参数对象的值
      }
```

**这里的 method 为 SetFieldInvoker。invoke 的方法代码如下：**

```
  public Object invoke(Object target, Object[] args) throws IllegalAccessException, InvocationTargetException {
    field.set(target, args[0]);//这个field对象就是java.lang.reflect.Field对象。
    return null;
  }
```

这里就会发现其实`Jdbc3KeyGenerator`在insert操作完获取主键直接可以通过 ResultSet 获取到。而 KeyGenerator.class其实就是个设计模式封装而已。

通过断点发现JDBC 的 ResultSetImpl.class (ResultSet.class 接口实现) ，其实是有字段 resultId 、 updateId (mysql注释: /** Value generated for AUTO_INCREMENT columns */ -> 就是自增主键的值 )

还有个字段是 updateCount (mysql注释: /** How many rows were affected by UPDATE/INSERT/DELETE? */ -> 就是影响行数 )

**由此推断不是 mybatis 实现的获取自增主键的功能，而是 JDBC 原生实现了这个功能。mybatis 只是封装了这个功能**

参考：
- https://juejin.im/post/5b95138de51d450e6237acf8
- https://blog.csdn.net/zknxx/article/details/53558850

