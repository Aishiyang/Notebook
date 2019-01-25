# Effective Java

### 第一章 引言



### 第二章 创建和销毁对象



##### 第一条：考虑用静态工厂方法代替构造器

<font size="2">**原因：**</font>



**<font size="2">方案：</font>**





##### 第二条：遇到多个构造器参数时要考虑用构构建器

<font size="2">**原因：**</font>



**<font size="2">方案：</font>**





##### 第三条：用私有构造器或者枚举类型强化Singleton属性

<font size="2">**原因：**</font>



**<font size="2">方案：</font>**





##### 第四条：通过私有构造器强化不可实例化的能力

<font size="2">**原因：**</font>

​	<font size="2">有一些工具类，比如Math、Arrays和Collections类，并不希望被实例化，实例对他没有任何意义。</font>

**<font size="2">方案：</font>**

​	<font size="2">只有当类不包含显式的构造器时，编译器才会生成缺省的构造器，因此只要让这个类包含私有构造器,他就不能被实例化。 </font>

```java
private UtilClass{ 
	throw new AssertionError();
}
```



##### 第五条：避免创建不必要的对象

<font size="2">**原因：**</font>



**<font size="2">方案：</font>**















<font size="2">**原因：**</font>



**<font size="2">方案：</font>**



<font size="2">**原因：**</font>



**<font size="2">方案：</font>**



<font size="2">**原因：**</font>



**<font size="2">方案：</font>**



<font size="2">**原因：**</font>



**<font size="2">方案：</font>**



<font size="2">**原因：**</font>



**<font size="2">方案：</font>**



<font size="2">**原因：**</font>



**<font size="2">方案：</font>**



<font size="2">**原因：**</font>



**<font size="2">方案：</font>**





<font size="2">**原因：**</font>



**<font size="2">方案：</font>**



<font size="2">**原因：**</font>



**<font size="2">方案：</font>**



<font size="2">**原因：**</font>



**<font size="2">方案：</font>**











































