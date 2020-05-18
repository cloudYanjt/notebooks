## Orika使用

1、导入maven依赖包

```xml
<dependency>
<groupId>ma.glasnost.orika</groupId>
<artifactId>orika-core</artifactId>
<version>1.5.4</version>
</dependency>
```

2、构造一个MapperFactory
```java
private static final MapperFactory mapperFactory = new DefaultMapperFactory.Builder().build();
```

3、注册字段映射
```java
static{
	mapperFactory.classMap(Source.class, Destination.class)
	.field("firstName","givenName")
	.field("lastName","sirName")
	.byDefault()
	.register();
}
```

4、映射
```java
public static <S, T> T convert(S source, Class<T> target) {
	MapperFacade mapper = mapperFactory.getMapperFacade();
	T map = mapper.map(source,target);
	return map;
}
```
