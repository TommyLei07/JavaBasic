# 在ssm中使用pagehelper进行分页

## eclipse中使用
1. import:

- pagehelper-5.1.0-beta2.jar
- jsqlparser-1.0.jar 

2. 修改applicationContext.xml配置

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xsi:schemaLocation="
     http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd
     http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
     http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-3.0.xsd
     http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
     http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
     http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">
     
   <context:annotation-config />
    <context:component-scan base-package="com.how2java.service" />
 
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource"> 
      <property name="driverClassName"> 
          <value>com.mysql.jdbc.Driver</value> 
      </property> 
      <property name="url"> 
          <value>jdbc:mysql://localhost:3306/Datebasename?characterEncoding=UTF-8</value> 
     
      </property> 
      <property name="username"> 
          <value>root</value> 
      </property> 
      <property name="password"> 
          <value>admin</value> 
      </property>    
    </bean>
     
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="typeAliasesPackage" value="com.XXX.pojo" />
        <property name="dataSource" ref="dataSource"/>
        <property name="mapperLocations" value="classpath:com/XXX/mapper/*.xml"/>
        <property name="plugins">
            <array>
              <bean class="com.github.pagehelper.PageInterceptor">
                <property name="properties">
                  <!--使用下面的方式配置参数，一行配置一个 -->
                  <value>
                  </value>
                </property>
              </bean>
            </array>
          </property>    
    </bean>
 
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.XXX.mapper"/>
    </bean>
     
</beans>
```

3. Page类

```
package com.XXX.util;

public class Page {

	int start=0;
	int count=5;
	int last=0;
	public int getStart() {
		return start;
	}
	public void setStart(int start) {
		this.start = start;
	}
	public int getCount() {
		return count;
	}
	public void setCount(int count) {
		this.count = count;
	}
	public int getLast() {
		return last;
	}
	public void setLast(int last) {
		this.last = last;
	}
	
	public void caculateLast(int total) {
		if(0==total%count) {
			last=total-count;
		}
		else {
			last=total-total%count;
		}
	}
	
	
	
}

```

4. Controller

```
package com.XXX.controller;
 
import java.util.List;
 
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.support.PagedListHolder;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;
 
import com.github.pagehelper.PageHelper;
import com.github.pagehelper.PageInfo;
import com.how2java.pojo.Category;
import com.how2java.service.CategoryService;
import com.how2java.util.Page;
 
// 告诉spring mvc这是一个控制器类
@Controller
@RequestMapping("")
public class CategoryController {
    @Autowired
    CategoryService categoryService;
 
    @RequestMapping("listCategory")
    public ModelAndView listCategory(Page page){
        ModelAndView mav = new ModelAndView();
        PageHelper.offsetPage(page.getStart(),5);
        List<Category> cs= categoryService.list();
        int total = (int) new PageInfo<>(cs).getTotal();
         
        page.caculateLast(total);
         
        // 放入转发参数
        mav.addObject("cs", cs);
        // 放入jsp路径
        mav.setViewName("listCategory");
        return mav;
    }
 
}
```