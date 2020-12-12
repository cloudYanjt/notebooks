# Orika 使用入门

## 第一步：导入 maven 依赖包

```xml
<dependency>
<groupId>ma.glasnost.orika</groupId>
<artifactId>orika-core</artifactId>
<version>1.5.4</version>
</dependency>
```

## 第二步：构造一个 MapperFactory

```java
private static final MapperFactory mapperFactory = new DefaultMapperFactory.Builder().build();
```

## 第三步：注册字段映射

*field("A","B") 映射差异字段
exclude("exclude") 排除不想映射的字段
byDefault() 使用缺省配置映射*

```java
static{
	mapperFactory.classMap(Source.class, Target.class)
	.field("firstName","name")
	.field("lastName","lastName")
	.exclude("exclude")
	.byDefault()
	.register();
}
```

## 第四步：提供映射方法

```java
public static <S, T> T convert(S source, Class<T> target) {
	MapperFacade mapper = mapperFactory.getMapperFacade();
	T map = mapper.map(source,target);
	return map;
}
```

*调用 convert 方法，入参为原始对象和要转换成的目标.class 对象*

### List 映射：

*Orika 允许使用索引方式取得集合成员*

```java
mapperFactory.classMap(Source.class, Target.class)
	.field("listName[0]","firstName")
	.field("listName[1]","lastName")
	.register();
```

### Map映射

*Orika 允许使用 key 取 Map 中对应的值*

```java
mapperFactory.classMap(Source.class, Target.class)
      .field("nameMap['first']", "firstName")
      .field("nameMap[\"last\"]", "lastName")
      .register();
```

### 嵌套字段

*Orika 允许使用点取对应的值*

```java
mapperFactory.classMap(Source.class, Target.class)
      .field("name.firstName", "firstName")
      .field("name.lastName", "lastName")
      .register();
```

### 忽略 null 值映射，（使用 fieldMap 可以针对字段级别配置）

```java
//1、可以全局配置，创建MapperFactory时加上mapNulls(false)
MapperFactory mapperFactory = new DefaultMapperFactory.Builder()
  .mapNulls(false).build();

//2、也可以局部配置，mapNulls(false)之后为null的都不会映射，使用mapNullsInReverse(false)可以让mapNullsInReverse(false)之前的为null都不映射
mapperFactory.classMap(Source.class, Target.class)
	.field("firstName","name")
	.mapNulls(false)
	.field("lastName","lastName")
	.byDefault()
	.register();

//3、也可以对单个字段使用，fieldMap("firstName","name").mapNulls(false).add()只对firstName生效
mapperFactory.classMap(Source.class, Target.class)
	.fieldMap("firstName","name")
	.mapNulls(false).add()
	.field("lastName","lastName")
	.byDefault()
	.register();
```



### Orika 自定义映射

上面的方法都映射对象都不能在映射过程中进行转换，对象在映射过程中需要转换就得借助 CustomMapper 实现必要的映射过程转换。

CustomMapper 允许我们处理双向映射

```java
mapperFactory.classMap(Source.class, Target.class)
	.field("firstName","name")
	.field("lastName","lastName")
	.customize(new CustomMapper<Personne3, Person3>() {
		@Override
		public void mapAtoB(Source a, Target b, MappingContext context) {
            //自定义逻辑实现源数据根据需要格式进行转换并复制至目标对象
        }

		@Override
		public void mapBtoA(Target b, Source a, MappingContext context) {
			//自定义逻辑实现源数据根据需要格式进行转换并复制至目标对象(mapBtoA对应反方向的对象映射)
        }
	})
	.register();
```



### 自定义转换器

尽管默认在许多情况下 Orika 都具有内置的映射和转换器，但是也会出现不支持的映射情况。自定义转换器提供了一种解决方案。最简单的方法是继承 ma.glasnost.orika.CustomConverter<A,B>`，如以下示例所示：

```
public class MyConverter extends CustomConverter<Date,MyDate> {
   public MyDate convert(Date source, Type<? extends MyDate> destinationType) {
      // return a new instance of destinationType with all properties filled
   }
}
```

通常定义一个转换器来处理单向映射。如果要控制两个类型之间的双向映射，则需要一个双向转换器。

### 双向转换器

双向转换器用于定义一组给定类型之间的双向转换。可以通过继承`ma.glasnost.orika.converter.BidirectionalConverter<A,B>`方式自定义双向转换器，如下所示：

```java
public class MyConverter extends BidirectionalConverter<Date,MyDate> {

   public MyDate convertTo(Date source, Type<MyDate> destinationType) {
      // convert in one direction
   }

   public Date convertFrom(MyDate source, Type<Date> destinationType) {
      // convert in the other direction
   }
}
```

### 全局注册转换器

为了能够识别转换器并在映射操作中使用它们，必须向 ConverterFactory（与 MapperFactory 关联）注册它们。有两种向转换器工厂注册转换器的方式；一种是全局级别注册转换器，如下所示：

```java
ConverterFactory converterFactory = mapperFactory.getConverterFactory();
converterFactory.registerConverter(new MyConverter());
```

*只要源类型和目标类型与转换器定义的类型兼容，就可以使用在**全局级别**注册的转换器。*

### 注册字段级别转换器

第二种方式是在字段级别注册转换器; 对于不一定要让转换器对特定类型的每个实例都起作用的情况。需要注册在字段使用的转换器，第一步是使用注册方法传入转换器 id（字符串），如下所示：

```java
ConverterFactory converterFactory = mapperFactory.getConverterFactory();
converterFactory.registerConverter("myConverterIdValue", new MyConverter());
```

然后可以将该转换器 Id 专门应用于任何字段映射，如下所示：

```java
mapperFactory.classMap( Source.class, Destination.class )
   .fieldMap("sourceField1", "sourceField2").converter("myConverterIdValue").add()
   ...
   .register();
```
