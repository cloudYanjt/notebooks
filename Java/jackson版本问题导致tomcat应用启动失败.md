# jackson版本问题导致tomcat应用启动失败

SEVERE: Unable to process Jar entry [module-info.class] from Jar [jar:file: ... .../WEB-INF/lib/jackson-core-2.11.0.jar!/] for annotations
org.apache.tomcat.util.bcel.classfile.ClassFormatException: Invalid byte tag in constant pool: 19

进入 .m2\repository\com\fasterxml\jackson\core\jackson-core\2.11.0 目录，打开压缩包jackson-core-2.11.0.jar可以看到有一个 module-info.class 文件，这是java9特性里的东西。当前版本的tomcat不识别所以导致启动时报错了。
![](_v_images/20201228130422244_21567.png)

解决办法，1、删除jar包里的 module-info.class 文件
2、使用没有 module-info.class 版本的 jar 包