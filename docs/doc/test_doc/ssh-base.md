## KD ssh-base-1.0.0-M11 
###  Thinks  @AdvanceOrange  contributions  doc  ,由 hpit-bat 成员 AdvanceOrange 编写的文档
**ssh-base** 是一个基于`struts`，`spring`和`hibernate`的工具，旨在简化在分页，添加，删除，修改，排序查询时的WEB操作。

### 项目结构
```
org
	|---kd
		|---ssh
			|---annotaion
				HpField.java
				HplsDeleteField.java
				|---model
					BaseModel.java
					HpFieldModel.java
					HpLsDeleteFieldModel.java
 			|---base
 				|---action
 					BaseAction.java
 					BaseActionPlus.java
 					BaseActionReturnPlus.java
 					BaseActionSimple.java
 				|---dao
 					BaseDao.java
 					BaseDaoImpl.java
 				|---service
					BaseService.java
 					BaseServiceImpl.java
 			|---page
 				PageBean.java
 			|---util
 				HpHqlUtil.java
				ReadPropertyPlaceholder.java
 				SortList.java
```
### **kd.ssh.annotation包**   _——注解的定义_
#### 1. HpField.java
```String order() default "desc"; //定义升序或降序,取值asc或desc ```
```int orderIndex() default 0; //当前属性的排序级别,如果 属性1 orderIndex() > 属性2 orderIndex(), 会生成这样的sql语句 order by 属性1, 属性2 ```
#### 2. HpIsDeleteField.java
```String isDeleteField() default ""; //定义要修改的属性名，如果不写会取得属性名```
```int isDeleteValue() default 1;//```
```int alreadyDeleteValue() default 0;```

### **kd.ssh.annotation.model包** _——内置模型的定义_
#### 1. BaseMode.java
```创建时间,修改时间,isDelete 属性定义```
#### 2. HpFieldModel.java
```用来保存一个实体类中需要排序属性的属性名，升序或降序，排序级别```
#### 3. HpLsDeleteFieldModel.java
```用来保存一个实体类中需要删除或修改的属性名，属性值，预备删除的值```

### **kd.ssh.base.action包** _——内置Action的定义，通过继承它使你的action来获得一些能力_
#### 1. BaseAction.java
```定义了一些基础的属性，例如分页，泛型实体类[pojo]，封装Json的jsonMap，request对象，以及分页的基础属性，request对象```
#### 2. BaseActionPlus.java
```默认继承了BaseAction，定义了内置的BaseService类baService对象，kdField实体类的id```
| 方法名 | 描述 |
| --- | --- |
| Integer setField() | 调用getIdField("Id")通过反射调用getId()方法，取回Integer属性值。 |
| String setFieldByString()|调用getIdFieldByString("Id")传入"Id" |
| Integer getIdField(String fieldName) | @fieldName:属性名 通过传入的属性名，调用model.get+属性名方法，返回`Integer`类型值。example:@fieldName = id, 调用getId() |
| String getIdFieldByString(String fieldName) | @fieldName:属性名 通过传入的属性名，调用model.get+属性名方法，返回`String`类型值。example:@fieldName = id, 调用getId() |
| String saveUI() | _**返回到```savaUI```视图**_|
| String updateUI() | 调用setField()调用getIdField("Id")通过反射调用getId()方法，取回`Integer`属性值, 根据取回的值获得对象放入request中，key=user 【通过Integer类型的Id属性取回对象】_**返回到```updateUI```视图**_|
| String updateUIByString()| 调用setFieldByString调用setFieldByString, 取回`String`属性值, 根据取回的值获得对象放入request中，key=user 【通过String类型的Id属性取回对象】_**返回到```updateUI```视图**_|
| String list() | _**返回到```list```视图**_ |
| String ajaxList() | 根据pageNum 和 pageSize查询当前页信息， 并返回json数据，封装在page对象中 _**返回到```ajax```视图**_|
| String allAjaxList() | 查询全部数据，并返回json数据，封装在list对象中 _**返回到```ajax```视图**_|
| String save() | 保存一个对象数据 _**返回到```ajax```视图**_|
| String delete() | 【int 类型】删除数据，调用setField(),通过传入属性名（“id”），反射调用相应的model.get属性名方法并返回值， 调用BaseDaoImpl.delete()传入返回值，调用getById(Integer)方法传入参数查询一个对象，然后删除这个对象对应的数据,通过子类覆写setField()方法，你可以指定一个属性名 _**返回到```ajax```视图**_|
| String deleteByString() | 【String 类型】删除数据，调用setFieldByString(),通过传入属性名（“id”），反射调用相应的model.get属性名方法并返回值， 调用BaseDaoImpl.deleteBy()传入返回值，调用getById(String)方法传入参数查询一个对象，然后删除这个对象对应的数据,通过子类覆写setField()方法，你可以指定一个属性名，这样你就可以依据任何属性值删除数据【但是这个属性值必须是Id主键列值，详情见baseDaoImpl.getById(Integer or String)方法】 _**返回到```ajax```视图**_|
| String updateDelete() | 【int 类型】删除数据，调用setField(),通过传入属性名（“id”），反射调用相应的model.get属性名方法并返回值， 调用BaseDaoImpl.updateDelete()传入返回值，调用getById(Integer)方法传入参【通常是id列值】数查询一个对象，然后查看通过反射调用属性上有@HpIsDeleteField注解的set属性()方法，更改有isDeleteField()注解属性的值[_如果该注解值不为"",则选择该注解值所指向的属性_],为注解fieldValue()的值，如果该类的父类不是Object，更改isDeleteField()注解属性的值，为注解AlreadyDeleteValue()的值**此方法要求此属性有HpIsDeleteField注解，且必須要有set属性值（参数为integer类型）**,通过子类覆写setField()方法，你可以指定一个属性名 _**返回到```ajax```视图**_|
| String updateDeleteByString() | 【String 类型】删除数据，调用setField(),通过传入属性名（“id”），反射调用相应的model.get属性名方法并返回值， 调用BaseDaoImpl.updateDelete()传入返回值，调用getById(Integer)方法传入参【通常是id列值】数查询一个对象，然后查看通过反射调用属性上有@HpIsDeleteField注解的set属性()方法，更改有isDeleteField()注解属性的值[_如果该注解值不为"",则选择该注解值所指向的属性_],为注解fieldValue()的值，如果该类的父类不是Object，更改isDeleteField()注解属性的值，为注解AlreadyDeleteValue()的值**此方法要求此属性有HpIsDeleteField注解，且必須要有set属性值（参数为String类型）**,通过子类覆写setField()方法，你可以指定一个属性名 _**返回到```ajax```视图**_|
|String update()|更新一个实体类     _**返回到```ajax```视图**_|
#### 3.BaseActionReturnPlus.java
```和BaseActionPlus区别在于返回的视图不同，以及其它一些细微区别```
| 方法名 | 描述 |
| --- | --- |
|Integer setField()|和BaseActioReturnPlus中的同名方法无区别|
|Integer setFieldByString()|和BaseActioReturnPlus中的同名方法无区别|
|Integer setFieldByString()|和BaseActioReturnPlus中的同名方法无区别|
|getIdField(String fieldName)|和BaseActioReturnPlus中的同名方法无区别|
|getIdFieldByString(String fieldName)|和BaseActioReturnPlus中的同名方法无区别|
|String saveUI()|返回到 _**saveUI**_ 视图
|String updateUI()|调用setField()调用getIdField("Id")通过反射调用getId()方法，取回Integer属性值, 根据取回的值获得对象放入request中，key=model 【通过Integer类型的Id属性取回对象】_**返回到```updateUI```视图**_|
|String updateUIByString()|调用setFieldByString调用setFieldByString, 取回String属性值, 根据取回的值获得对象放入request中，key=model 【通过String类型的Id属性取回对象】_**返回到updateUI视图**_|
|String list()|查询一页的数据，依据pageNum，pageSize，结果保存在request中，key=page，返回到list视图|
|String allList()|查询所有数据，结果保存在request中，key=list，返回到alllist视图|
|String save()|保存一个实体类|
|String delete() | 【int 类型】删除数据，调用setField(),通过传入属性名（“id”），反射调用相应的model.get属性名方法并返回值， 调用BaseDaoImpl.delete()传入返回值，调用getById(Integer)方法传入参数查询一个对象，然后删除这个对象对应的数据,通过子类覆写setField()方法，你可以指定一个属性名，这样你就可以依据任何属性值删除数据【但是这个属性值必须是Id主键列值】 _**返回到```toList```视图**_|
| String deleteByString() | 【String 类型】删除数据，调用setFieldByString(),通过传入属性名（“id”），反射调用相应的model.get属性名方法并返回值， 调用BaseDaoImpl.deleteBy()传入返回值，调用getById(String)方法传入参数查询一个对象，然后删除这个对象对应的数据,通过子类覆写setField()方法，你可以指定一个属性名,这样你就可以依据任何属性值删除数据 _**返回到```toList```视图**_|
| String updateDelete() | 【int 类型】删除数据，调用setField(),通过传入属性名（“id”），反射调用相应的model.get属性名方法并返回值， 调用BaseDaoImpl.updateDelete()传入返回值，调用getById(Integer)方法传入参【通常是id列值】数查询一个对象，然后查看通过反射调用属性上有@HpIsDeleteField注解的set属性()方法，更改有isDeleteField()注解属性的值[_如果该注解值不为"",则选择该注解值所指向的属性_],为注解fieldValue()的值，如果该类的父类不是Object，更改isDeleteField()注解属性的值，为注解AlreadyDeleteValue()的值**此方法要求此属性有HpIsDeleteField注解，且必須要有set属性值（参数为integer类型）**,通过子类覆写setField()方法，你可以指定一个属性名 _**返回到```toList```视图**_|
| String updateDeleteByString() | 【String 类型】删除数据，调用setField(),通过传入属性名（“id”），反射调用相应的model.get属性名方法并返回值， 调用BaseDaoImpl.updateDelete()传入返回值，调用getById(Integer)方法传入参【通常是id列值】数查询一个对象，然后查看通过反射调用属性上有@HpIsDeleteField注解的set属性()方法，更改有isDeleteField()注解属性的值[_如果该注解值不为"",则选择该注解值所指向的属性_],为注解fieldValue()的值，如果该类的父类不是Object，更改isDeleteField()注解属性的值，为注解AlreadyDeleteValue()的值**此方法要求此属性有HpIsDeleteField注解，且必須要有set属性值（参数为String类型）**,通过子类覆写setField()方法，你可以指定一个属性名，这样你就可以依据任何属性值修改数据【但是这个属性值必须是Id主键列值】 _**返回到```toList```视图**_|
|String update()|更新一个实体类     _**返回到```toList```视图**_|
|String saveUpdateUI()| setField() -> getIdField("Id") ->	依据传入的属性名调用model.get属性名方法，返回方法的返回值，根据这个返回值查询出一个实体类保存在request上，key=model _**返回到```saveUpdateUI```视图**_|
|String saveUpdate()|setField() -> getIdField("Id") -> 依据传入的属性名调用model.get属性名方法，返回方法的返回值，如果有返回值就执行update()，没有返回值执行save()|

### **org.kd.ssh.base.dao包** _——内置Dao的定义，做一些和数据库相关的操作_
#### 1.BaseDao.java
```一个dao接口，BaseDaoImpl实现了它```
#### 2.BaseDaoImpl.java
| 方法名 | 描述 |
| --- | --- |
|BaseDaoImpl()|构造方法，用来实例化泛型实体类model，还会调用setHpFieldModel方法做一写注解扫描的工作|
|void save(T model)|保存一个实体类|
|void delete(Integer id)|通过传入的id值，调用getById()查询到主键为id值的对象，如果查询到，就删除它，否则抛出异常|
|void delete(String id)|通过传入的id值，调用getById()查询到主键为id值的对象，如果查询到，就删除它，否则抛出异常，与上述方法不同的是id值是String类型的|
|void update(T model)|修改一个实体类数据|
|List<T> queryAllList()|先调用getWhereHql()获得经过扫描HpIsDeleteField注解获得的条件语句，然后调用getOrderByHql()获得经过扫描HpField注解生成了order by排序语句，然后联合where语句和order by语句来查询数据|
|T getById(Integer id)|根据主键为Integer类型并且值为id查询出一个实体类|
|T getById(String id)|根据主键为String类型并且值为id查询出一个实体类|
|Session getHibernateSession()|获得一个session|
|List<T> queryPageList(Integer pageNum, Integer pageSize)|_@pageNum_:当前页 _@pageSize_:一页的结果数量，和querAllList()方法不同的是，除了能进行条件查询，该方法还能实现分页|
|List<T> queryParamsAllList(String whereSql, Map<String, Object> map)|_@whereSql_:条件语句， _@map_:字段名，字段值映射map集合，传入whereSql预处理hql语句，map中保存填?的值，进行对应填写|
|List<T> queryPageParamsList(Integer pageNum, Integer pageSize, String whereSql, Map<String, Object> map)|_@pageNum_:当前页 _@pageSize_:一页的结果数量，_@whereSql_:条件语句， _@map_:字段名，字段值映射map集合，传入whereSql预处理hql语句，map中保存填?的值，进行对应填写，和上述方法不同的是，它实现了分页功能|
|Long getCount()|获得一个结果集长度|
|Long getWhereHqlCount(String whereHql, Map<String, Object> map)|_@whereSql_:条件语句， _@map_:字段名，字段值映射map集合，传入whereSql预处理hql语句，map中保存填?的值，进行对应填写，和上述方法不同的是，它获得一个有条件的结果集长度|
|void updateDelete(Integer id)|首先需要实体类中必须有HpIsDeleteField注解，否则抛出异常，通过传入的Integer类型id值，查询一个实体类，并判断它的父类是否是Object，如果是就调用set属性名或set注解HpIsDeleteField.isDeleteField()值，传入HpIsDeleteField.isDeleteValue()的值，最后调用修改方法，如果父类不是Object类型，除了调用父类的set***方法外，传入的值变为hpIsDeleteFieldModel.getAlreadyDeleteValue()的值|
|void updateDelete(String id)|和上述方法不同的是，查询实体类id是String类型的|
|void setHpFieldModel(Class<T> clazz)|_@clazz_:一个对象的Class，对注解进行扫描，一般是实体类，并生成hpIsDeleteFieldModel对象和添加hpFieldModelList集合值_HpFieldModel对象_，如果clazz的父类不是Object，那么添加到hpFieldModelList集合的值，会变为父类注解扫描所生成的HpFieldModel对象|
|String getWhereHql()| 判断扫描注解HpIsDeleteField所生成的hpIsDeleteFieldModel对象是否为null，不为空，生成where 1=1 and 注解isDeleteValue()值 = isDeleteValue()值或者alreadyDeleteValue()值的hql语句|
| String getOrderByHql()|根据HpField注解来产生order by排序语句，orderIndex()值越大，排序语句位置越靠前，example:@HpField(orderIndex = 1)String username;@HpField(orderIndex = 2)String userage ... 产生order by username desc, userage desc...;|
|static <T> void sort(List<T> targetList, final String sortField, final boolean sortMode)|@targetList:排序的集合用来产生排序语句，_@sortField_:"orderIndex"要调用的get***方法名，_@sortMode_:决定是obj1比较obj2，还是obj2比较obj1|

### **org.kd.ssh.base.service包** _——内置Service的定义，大部分是直接调用dao层方法，做一些分页中间处理_
#### 1.BaseService.java
```一个service接口，BaseServiceImpl实现了它```
#### 2.BaseServiceImpl.java
```直接调用dao的方法省略```
| 方法名 | 描述 |
| --- | --- |
|PageBean<T> queryPageList(Integer pageNum, Integer pageSize)|_@pageNum_:当前页，_@pageSize_:每页数量，调用baseDao.queryPageList()，调用baseDao.getCount()方法，将pageSize,pageNum,list,total封装为PageBean对象|
|PageBean<T> queryPageParamsList(Integer pageNum, Integer pageSize, String whereSql,Map<String, Object> map)|_@pageNum_:当前页，_@pageSize_:每页数量，_@whereSql_:预处理hql语句，_@map_，预处理中要填值的map集合，调用baseDao.queryPageParamsList()，调用baseDao.getWhereHqlCount()，和上述方法不同的是，本方法支持条件分页查询|
|List<T> queryParamsAllList(String whereSql, Map<String, Object> map)|，_@whereSql_:预处理hql语句，_@map_，预处理中要填值的map集合，调用baseDao.queryParamsAllList(whereSql, map)，依据条件查询所有数据并返回|

### **org.kd.ssh.page包** _——分页的实体类_
#### 1.PageBean.java
```一个实体类，用来保存分页的数据```

### **org.kd.ssh.util包** _——分页的实体类_
#### 1.HpHqlUtil.java
```一个工具类```
| 方法 | 描述|
| --- | ---|
|String getWhereHql(Map<String, Object> map)|_@map_:属性名集合，依据map生成预处理语句|
|String getFieldMethod(String prefix, String field) |_@prefix:_ 前缀，_@field:_ 字段名，生成一些方法名，example: prefix = "set",field = "name" -> setName|
    
#### 2.ReadPropertyPlaceholder.java
```用来读取property配置文件```

#### 3.SortList.java
```用来生成排序语句```
| 方法 | 描述|
| --- | ---|
|static <T> void sort(List<T> targetList, final String sortField, final boolean sortMode)|_@targetList_:排序的集合用来产生排序语句，_@sortField_ :"orderIndex"要调用的get***方法名，_@sortMode_:决定是obj1比较obj2，还是obj2比较obj1||
    

### 如何使用？
#### struts.xml配置
```xml
<package name="kd" extends="json-default">
		<!--小写类名_方法名_目录名.后缀名   路由-->
		<action name="*_*_*" class="{1}" method="{2}">
			<result name="toList" type="redirectAction">{1}_list_{3}</result>
			<result name="{2}">/WEB-INF/page/{3}/{2}.jsp</result>
		</action>
</package>
```
#### spring-baen.xml配置
```xml
	<!-- 引入属性文件 -->
	<context:property-placeholder location="classpath:properties/jdbc.properties" />
 
 	<!-- 引入系统属性文件 -->
	<bean id="propertyConfigurer" class="org.kd.ssh.util.ReadPropertyPlaceholder">
		<property name="locations">
			<list>
				<value>classpath:properties/dev.properties</value>
			</list>
		</property>
	</bean>
	<!-- 自动扫描(自动注入) -->
	<context:component-scan base-package="org.kd.action" />
	<context:component-scan base-package="org.kd.dao.impl" />
 	<context:component-scan base-package="org.kd.service.impl" />
 	
 	<!-- base 系列 要注入的 -->
    <context:component-scan base-package="org.kd.ssh.base" />
```

#### spring-hibernate.xml配置
```xml
	<!-- 引入属性文件 -->
	<context:property-placeholder location="classpath:properties/jdbc.properties" />
 
 	<!-- 引入系统属性文件 -->
	<bean id="propertyConfigurer" class="org.kd.ssh.util.ReadPropertyPlaceholder">
		<property name="locations">
			<list>
				<value>classpath:properties/dev.properties</value>
			</list>
		</property>
	</bean>
	<!-- 自动扫描(自动注入) -->
	<context:component-scan base-package="org.kd.action" />
	<context:component-scan base-package="org.kd.dao.impl" />
 	<context:component-scan base-package="org.kd.service.impl" />
 	
 	<!-- base 系列 要注入的 -->
    <context:component-scan base-package="org.kd.ssh.base" />
```    
