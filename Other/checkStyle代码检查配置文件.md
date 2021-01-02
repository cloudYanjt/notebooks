# checkStyle代码检查配置文件

*CheckStyle 是一个检验代码规范的插件，官网地址：https://checkstyle.sourceforge.io
在maven中一个名为 `maven-checkstyle-plugin` 的插件，用于执行 CheckStyle task.
IDEA   中可以下载插件 [CheckStyle-IDEA](https://plugins.jetbrains.com/plugin/1065-checkstyle-idea).*

```xml
<?xml version="1.0"?>
<!DOCTYPE module PUBLIC
  "-//Puppy Crawl//DTD Check Configuration 1.3//EN"
  "http://www.puppycrawl.com/dtds/configuration_1_3.dtd">

<module name="Checker">
  <property name="charset" value="UTF-8"/>

  <!-- 指定告警级别，告警级别有 ignore info warning error , 缺省值是 error -->
  <property name="severity" value="warning"/>

  <!-- 排除所有 'module-info.java' 文件 -->
  <module name="BeforeExecutionExclusionFileFilter">
    <property name="fileNamePattern" value="module\-info\.java$"/>
  </module>
  <!-- 检查是否有制表符 -->
  <module name="FileTabCharacter">
    <property name="severity" value="info"/>
    <property name="eachLine" value="true"/>
  </module>
  <!-- 检查文件是否未以一个空行结束 -->
  <module name="NewlineAtEndOfFile">
    <property name="severity" value="info"/>
  </module>
  <!-- 文件长度不超过1500行 -->
  <module name="FileLength">
    <property name="fileExtensions" value="java"/>
    <property name="max" value="1500"/>
  </module>
  <!-- 每行不超过120个字符 -->
  <module name="LineLength">
    <property name="fileExtensions" value="java"/>
    <property name="max" value="120"/>
    <property name="ignorePattern" value="^package.*|^import.*|a href|href|http://|https://|ftp://"/>
  </module>

  <module name="TreeWalker">
    <!-- 检查 public 类名和文件名是不否匹配。 -->
    <module name="OuterTypeFilename"/>
    <!-- 检查指定的令牌文本是否没匹配非法模式。-->
    <module name="IllegalTokenText">
      <property name="tokens" value="STRING_LITERAL, CHAR_LITERAL"/>
      <property name="format"
        value="\\u00(09|0(a|A)|0(c|C)|0(d|D)|22|27|5(C|c))|\\(0(10|11|12|14|15|42|47)|134)"/>
      <property name="message"
        value="Consider using special escape sequence instead of octal value or Unicode escaped value."/>
    </module>
    <!-- 使用 Unicode 转义（如\u221e）限制 -->
    <module name="AvoidEscapedUnicodeCharacters">
      <property name="allowEscapesForControlCharacters" value="true"/>
      <property name="allowByTailComment" value="true"/>
      <property name="allowNonPrintableEscapes" value="true"/>
    </module>

    <!-- import检查 -->
    <!-- 避免 * 级导入 -->
    <module name="AvoidStarImport">
      <property name="severity" value="error"/>
      <property name="excludes" value="java.io,java.net,java.lang.Math"/>
      <!-- 实例；import java.util.*;.-->
      <property name="allowClassImports" value="false"/>
      <!-- 实例 ；import static org.junit.Assert.*;-->
      <property name="allowStaticMemberImports" value="true"/>
    </module>
    <!-- 检查是否从非法的包中导入了类 -->
    <module name="IllegalImport">
      <property name="severity" value="error"/>
    </module>
    <!-- 检查是否导入了多余的包 -->
    <module name="RedundantImport">
      <property name="severity" value="error"/>
    </module>
    <!-- 没用的import检查，比如：1.没有被用到2.重复的3.import java.lang的4.import 与该类在同一个package的 -->
    <module name="UnusedImports">
      <property name="severity" value="error"/>
    </module>
    <!-- 检查所选语句是否是在一行完成的 默认PACKAGE_DEF, IMPORT, STATIC_IMPORT -->
    <module name="NoLineWrap">
      <property name="severity" value="error"/>
      <property name="tokens" value="PACKAGE_DEF, IMPORT, STATIC_IMPORT"/>
    </module>


    <!-- 注释检查 -->
    <!-- 检查是否有行尾注释 -->
    <module name="TrailingComment"/>
    <!-- 检查类的javadoc -->
    <module name="JavadocType">
      <property name="scope" value="public"/>
      <property name="authorFormat" value="\S"/>
      <property name="versionFormat" value="\$.*\$"/>
      <property name="allowedAnnotations" value="SpringBootApplication,Configuration,Generated"/>
      <property name="allowUnknownTags" value="true"/>
    </module>
    <!--检查类上是否没有注释-->
    <module name="MissingJavadocType">
      <property name="scope" value="public"/>
      <property name="excludeScope" value="nothing"/>
      <property name="skipAnnotations" value="SpringBootApplication,Configuration,Generated"/>
      <property name="tokens"
        value="CLASS_DEF, INTERFACE_DEF, ENUM_DEF, RECORD_DEF, ANNOTATION_DEF"/>
    </module>

    <!-- 检查方法的javadoc -->
    <module name="JavadocMethod">
      <property name="scope" value="public"/>
      <property name="validateThrows" value="false"/>
      <property name="allowMissingParamTags" value="true"/>
      <property name="allowMissingReturnTag" value="true"/>
      <property name="allowedAnnotations" value="Override, Bean, RequestMapping, GetMapping,
      	PostMapping, PutMapping, DeleteMapping, PatchMapping, Test, Before, After, BeforeEach,
      	AfterEach, BeforeAll, AfterAll, RepeatedTest"/>
      <property name="tokens" value="METHOD_DEF, CTOR_DEF, ANNOTATION_FIELD_DEF, COMPACT_CTOR_DEF"/>
    </module>
    <!--允许get set 方法没有注释-->
    <module name="MissingJavadocMethod">
      <property name="scope" value="public"/>
      <property name="minLineCount" value="2"/>
      <property name="allowMissingPropertyJavadoc" value="true"/>
      <property name="allowedAnnotations" value="Override, Test"/>
      <property name="tokens" value="METHOD_DEF, CTOR_DEF, ANNOTATION_FIELD_DEF,
                                   COMPACT_CTOR_DEF"/>
    </module>

    <!-- 命名检查 -->
    <!-- 包名的检查（只允许小写字母），默认 ^[a-z]+(\.[a-zA-Z_][a-zA-Z0-9_]*)*$ -->
    <module name="PackageName">
      <property name="severity" value="error"/>
      <property name="format" value="^[a-z]+(\.[a-z][a-z0-9]*)*$"/>
    </module>
    <!-- Class或Interface名检查，默认 ^[A-Z][a-zA-Z0-9]*$ -->
    <module name="TypeName">
      <property name="format" value="^[A-Z][a-zA-Z0-9]*$"/>
      <property name="tokens" value="CLASS_DEF, INTERFACE_DEF, ENUM_DEF,
                    ANNOTATION_DEF, RECORD_DEF"/>
    </module>
    <!-- 抽象类命名检查，默认 ^Abstract.+$ -->
    <module name="AbstractClassName">
      <property name="format" value="^Abstract.+$"/>
      <property name="ignoreName" value="true"/>
    </module>
    <!-- 方法名的检查 默认 ^[a-z][a-zA-Z0-9]*$ -->
    <module name="MethodName">
      <property name="format" value="^[a-z][a-z0-9][a-zA-Z0-9_]*$"/>
    </module>
    <!-- 方法的参数名 -->
    <module name="ParameterName">
      <property name="format" value="^[a-z]([a-z0-9][a-zA-Z0-9]*)?$"/>
    </module>
    <!-- lambda参数名检查 -->
    <module name="LambdaParameterName">
      <property name="format" value="^[a-z]([a-z0-9][a-zA-Z0-9]*)?$"/>
    </module>
    <!-- 异常捕获参数名检查 -->
    <module name="CatchParameterName">
      <property name="format" value="^[a-z]([a-z0-9][a-zA-Z0-9]*)?$"/>
    </module>
    <!-- 局部的final变量，包括catch中的参数的检查 -->
    <module name="LocalFinalVariableName"/>
    <!-- 局部的非final型的变量，包括catch中的参数的检查 -->
    <module name="LocalVariableName">
      <property name="format" value="^[a-z]([a-z0-9][a-zA-Z0-9]*)?$"/>
    </module>
    <module name="PatternVariableName">
      <property name="format" value="^[a-z]([a-z0-9][a-zA-Z0-9]*)?$"/>
    </module>
    <module name="ClassTypeParameterName">
      <property name="format" value="(^[A-Z][0-9]?)$|([A-Z][a-zA-Z0-9]*[T]$)"/>
    </module>
    <module name="RecordTypeParameterName">
      <property name="format" value="(^[A-Z][0-9]?)$|([A-Z][a-zA-Z0-9]*[T]$)"/>
    </module>
    <module name="MethodTypeParameterName">
      <property name="format" value="(^[A-Z][0-9]?)$|([A-Z][a-zA-Z0-9]*[T]$)"/>
    </module>
    <module name="InterfaceTypeParameterName">
      <property name="format" value="(^[A-Z][0-9]?)$|([A-Z][a-zA-Z0-9]*[T]$)"/>
    </module>
    <!-- 仅仅是static型的变量（不包括static final型）的检查 -->
    <module name="StaticVariableName"/>
    <!-- 非static型变量的检查 -->
    <module name="MemberName">
      <property name="format" value="^[a-z][a-z0-9][a-zA-Z0-9]*$"/>
    </module>
    <!-- 常量名的检查（只允许大写），默认^[A-Z][A-Z0-9]*(_[A-Z0-9]+)*$ -->
    <module name="ConstantName"/>
    <!-- 属性命名检查 -->
    <module name="AbbreviationAsWordInName">
      <property name="ignoreFinal" value="false"/>
      <property name="allowedAbbreviationLength" value="3"/>
      <property name="tokens"
        value="CLASS_DEF, INTERFACE_DEF, ENUM_DEF, ANNOTATION_DEF, ANNOTATION_FIELD_DEF,
                    PARAMETER_DEF, VARIABLE_DEF, METHOD_DEF, PATTERN_VARIABLE_DEF, RECORD_DEF,
                    RECORD_COMPONENT_DEF"/>
    </module>

    <!-- 定义检查 -->
    <!-- 检查数组类型定义的样式 -->
    <module name="ArrayTypeStyle"/>
    <!-- 检查long型定义是否没有大写的“L” -->
    <module name="UpperEll"/>

    <!-- 长度检查 -->
    <!-- 方法不超过150行 -->
    <module name="MethodLength">
      <property name="tokens" value="METHOD_DEF"/>
      <property name="max" value="150"/>
    </module>
    <!-- 方法的参数个数不超过7个。 并且不对构造方法进行检查-->
    <module name="ParameterNumber">
      <property name="tokens" value="METHOD_DEF"/>
      <property name="max" value="7"/>
      <property name="ignoreOverriddenMethods" value="true"/>
    </module>

    <!-- 风格检查-->
    <!-- 方法名后跟左圆括号"(" -->
    <module name="MethodParamPad">
      <property name="severity" value="info"/>
      <property name="tokens"
        value="CTOR_DEF, LITERAL_NEW, METHOD_CALL, METHOD_DEF,
                    SUPER_CTOR_CALL, ENUM_CONSTANT_DEF, RECORD_DEF"/>
    </module>
    <!-- 在类型转换时，不允许左圆括号右边有空格，也不允许与右圆括号左边有空格 -->
    <module name="TypecastParenPad">
      <property name="severity" value="info"/>
    </module>
    <!-- 检查在某个特定关键字之后应保留空格 -->
    <module name="NoWhitespaceAfter">
      <property name="severity" value="info"/>
    </module>
    <!-- 检查在某个特定关键字之前应保留空格 -->
    <module name="NoWhitespaceBefore">
      <property name="severity" value="info"/>
      <property name="tokens"
        value="COMMA, SEMI, POST_INC, POST_DEC, DOT,
                    LABELED_STAT, METHOD_REF"/>
      <property name="allowLineBreaks" value="true"/>
    </module>
    <!-- 操作符换行策略检查 -->
    <module name="OperatorWrap">
      <property name="severity" value="info"/>
      <property name="option" value="NL"/>
      <property name="tokens"
        value="BAND, BOR, BSR, BXOR, DIV, EQUAL, GE, GT, LAND, LE, LITERAL_INSTANCEOF, LOR,
                    LT, MINUS, MOD, NOT_EQUAL, PLUS, QUESTION, SL, SR, STAR, METHOD_REF "/>
    </module>
    <!-- 圆括号空白 -->
    <module name="ParenPad">
      <property name="severity" value="info"/>
      <property name="tokens"
        value="ANNOTATION, ANNOTATION_FIELD_DEF, CTOR_CALL, CTOR_DEF, DOT, ENUM_CONSTANT_DEF,
                    EXPR, LITERAL_CATCH, LITERAL_DO, LITERAL_FOR, LITERAL_IF, LITERAL_NEW,
                    LITERAL_SWITCH, LITERAL_SYNCHRONIZED, LITERAL_WHILE, METHOD_CALL,
                    METHOD_DEF, QUESTION, RESOURCE_SPECIFICATION, SUPER_CTOR_CALL, LAMBDA,
                    RECORD_DEF"/>
    </module>
    <!-- 检查分隔符是否不在空白之后 -->
    <module name="WhitespaceAfter">
      <property name="severity" value="info"/>
      <property name="tokens"
        value="COMMA, SEMI, TYPECAST, LITERAL_IF, LITERAL_ELSE,
                    LITERAL_WHILE, LITERAL_DO, LITERAL_FOR, DO_WHILE"/>
    </module>
    <!-- 检查分隔符周围是否没有空白 -->
    <module name="WhitespaceAround">
      <property name="severity" value="info"/>
      <property name="allowEmptyConstructors" value="true"/>
      <property name="allowEmptyLambdas" value="true"/>
      <property name="allowEmptyMethods" value="true"/>
      <property name="allowEmptyTypes" value="true"/>
      <property name="allowEmptyLoops" value="true"/>
      <property name="ignoreEnhancedForColon" value="false"/>
      <property name="tokens"
        value="ASSIGN, BAND, BAND_ASSIGN, BOR, BOR_ASSIGN, BSR, BSR_ASSIGN, BXOR,
                    BXOR_ASSIGN, COLON, DIV, DIV_ASSIGN, DO_WHILE, EQUAL, GE, GT, LAMBDA, LAND,
                    LCURLY, LE, LITERAL_CATCH, LITERAL_DO, LITERAL_ELSE, LITERAL_FINALLY,
                    LITERAL_FOR, LITERAL_IF, LITERAL_RETURN, LITERAL_SWITCH, LITERAL_SYNCHRONIZED,
                    LITERAL_TRY, LITERAL_WHILE, LOR, LT, MINUS, MINUS_ASSIGN, MOD, MOD_ASSIGN,
                    NOT_EQUAL, PLUS, PLUS_ASSIGN, QUESTION, RCURLY, SL, SLIST, SL_ASSIGN, SR,
                    SR_ASSIGN, STAR, STAR_ASSIGN, LITERAL_ASSERT, TYPE_EXTENSION_AND"/>
    </module>
    <!-- 换行位点检查 -->
    <module name="SeparatorWrap">
      <property name="severity" value="info"/>
      <property name="id" value="SeparatorWrapDot"/>
      <property name="tokens" value="DOT"/>
      <property name="option" value="nl"/>
    </module>
    <module name="SeparatorWrap">
      <property name="severity" value="info"/>
      <property name="id" value="SeparatorWrapComma"/>
      <property name="tokens" value="COMMA"/>
      <property name="option" value="EOL"/>
    </module>
    <module name="SeparatorWrap">
      <property name="severity" value="info"/>
      <property name="id" value="SeparatorWrapEllipsis"/>
      <property name="tokens" value="ELLIPSIS"/>
      <property name="option" value="EOL"/>
    </module>
    <module name="SeparatorWrap">
      <property name="severity" value="info"/>
      <property name="id" value="SeparatorWrapArrayDeclarator"/>
      <property name="tokens" value="ARRAY_DECLARATOR"/>
      <property name="option" value="EOL"/>
    </module>
    <module name="SeparatorWrap">
      <property name="severity" value="info"/>
      <property name="id" value="SeparatorWrapMethodRef"/>
      <property name="tokens" value="METHOD_REF"/>
      <property name="option" value="nl"/>
    </module>
    <!-- 检查标头、包、所有导入声明、字段、构造函数、方法、嵌套类、静态初始化器和实例初始化器之后空行分隔符。-->
    <module name="EmptyLineSeparator">
      <property name="severity" value="info"/>
      <property name="tokens"
        value="PACKAGE_DEF, IMPORT, STATIC_IMPORT, CLASS_DEF, INTERFACE_DEF, ENUM_DEF,
                    STATIC_INIT, INSTANCE_INIT, METHOD_DEF, CTOR_DEF, VARIABLE_DEF, RECORD_DEF,
                    COMPACT_CTOR_DEF"/>
      <property name="allowNoEmptyLineBetweenFields" value="true"/>
    </module>

    <!-- 修饰符检查 -->
    <!-- 检查修饰符的顺序是否遵照java语言规范，默认public、protected、private、abstract、static、final、transient、volatile、synchronized、native、strictfp -->
    <module name="ModifierOrder"/>
    <!-- 检查接口和annotation中是否有多余修饰符，如接口方法不必使用public -->
    <module name="RedundantModifier"/>

    <!-- 代码块检查 -->
    <!-- 检查是否有空代码块 -->
    <module name="EmptyBlock">
      <property name="severity" value="info"/>
      <property name="option" value="TEXT"/>
      <property name="tokens"
        value="LITERAL_TRY, LITERAL_FINALLY, LITERAL_IF, LITERAL_ELSE, LITERAL_SWITCH"/>
    </module>
    <!-- 检查是否有嵌套代码块 -->
    <module name="AvoidNestedBlocks">
      <property name="severity" value="info"/>
    </module>
    <!-- 检查代码块是否缺失{} -->
    <module name="NeedBraces">
      <property name="severity" value="info"/>
      <property name="tokens"
        value="LITERAL_DO, LITERAL_ELSE, LITERAL_FOR, LITERAL_IF, LITERAL_WHILE"/>
    </module>
    <!-- 检查左大括号位置 -->
    <module name="LeftCurly">
      <property name="severity" value="info"/>
      <property name="tokens"
        value="ANNOTATION_DEF, CLASS_DEF, CTOR_DEF, ENUM_CONSTANT_DEF, ENUM_DEF,
                    INTERFACE_DEF, LAMBDA, LITERAL_CASE, LITERAL_CATCH, LITERAL_DEFAULT,
                    LITERAL_DO, LITERAL_ELSE, LITERAL_FINALLY, LITERAL_FOR, LITERAL_IF,
                    LITERAL_SWITCH, LITERAL_SYNCHRONIZED, LITERAL_TRY, LITERAL_WHILE, METHOD_DEF,
                    OBJBLOCK, STATIC_INIT, RECORD_DEF, COMPACT_CTOR_DEF"/>
    </module>
    <!-- 检查右大括号位置 -->
    <module name="RightCurly">
      <property name="severity" value="info"/>
      <property name="id" value="RightCurlySame"/>
      <property name="tokens"
        value="LITERAL_TRY, LITERAL_CATCH, LITERAL_FINALLY, LITERAL_IF, LITERAL_ELSE,
                    LITERAL_DO"/>
    </module>
    <!-- 检查右大括号位置 -->
    <module name="RightCurly">
      <property name="severity" value="info"/>
      <property name="id" value="RightCurlyAlone"/>
      <property name="option" value="alone"/>
      <property name="tokens"
        value="CLASS_DEF, METHOD_DEF, CTOR_DEF, LITERAL_FOR, LITERAL_WHILE, STATIC_INIT,
                    INSTANCE_INIT, ANNOTATION_DEF, ENUM_DEF, INTERFACE_DEF, RECORD_DEF,
                    COMPACT_CTOR_DEF"/>
    </module>
    <module name="SuppressionXpathSingleFilter">
      <property name="id" value="RightCurlyAlone"/>
      <property name="query" value="//RCURLY[parent::SLIST[count(./*)=1]
                                     or preceding-sibling::*[last()][self::LCURLY]]"/>
    </module>

    <!-- 检查一行只能有一条语句 -->
    <module name="OneStatementPerLine">
      <property name="severity" value="error"/>
    </module>

    <!-- 代码检查 -->
    <!-- 检查空的代码段 -->
    <module name="EmptyStatement"/>
    <!-- 检查在重写了equals方法后是否未重写hashCode方法 -->
    <module name="EqualsHashCode"/>
    <!-- 检查局部变量或参数是否隐藏了类中的变量 -->
    <module name="HiddenField">
      <property name="severity" value="info"/>
      <property name="tokens" value="VARIABLE_DEF"/>
    </module>
    <!-- 检查是否使用工厂方法实例化 -->
    <module name="IllegalInstantiation"/>
    <!-- 检查子表达式中是否有赋值操作 -->
    <module name="InnerAssignment"/>
    <!-- 检查是否有重复出现的字符串 -->
    <module name="MultipleStringLiterals"/>
    <!-- 检查是否有"魔术"数字 -->
    <module name="MagicNumber">
      <property name="ignoreNumbers" value="-1, 0, 1"/>
      <property name="ignoreAnnotation" value="true"/>
    </module>
    <!-- 检查switch语句是否没有default -->
    <module name="MissingSwitchDefault"/>
    <!-- 检查是否有过度复杂的布尔表达式 -->
    <module name="SimplifyBooleanExpression"/>
    <!-- 检查是否有过于复杂的布尔返回代码段 -->
    <module name="SimplifyBooleanReturn"/>

    <!-- 检查是否不只一个顶级类 -->
    <module name="OneTopLevelClass"/>
    <!-- 检查只有private构造函数的类是否未声明为final -->
    <module name="FinalClass"/>
    <!-- 检查工具类是否没有public的构造器 -->
    <module name="HideUtilityClassConstructor"/>
    <!-- 检查接口是否仅定义类型 -->
    <module name="InterfaceIsType"/>
    <!-- 检查类成员的可见度 检查类成员的可见性。只有static final 成员是public的
    除非在本检查的protectedAllowed和packagedAllowed属性中进行了设置-->
    <module name="VisibilityModifier">
      <property name="packageAllowed" value="true"/>
      <property name="protectedAllowed" value="true"/>
    </module>

    <!-- 语法 -->
    <!-- String的比较不能用!= 和 == -->
    <module name="StringLiteralEquality"/>
    <!-- 限制for循环最多嵌套2层 -->
    <module name="NestedForDepth">
      <property name="max" value="2"/>
    </module>
    <!-- if最多嵌套3层 -->
    <module name="NestedIfDepth">
      <property name="max" value="3"/>
    </module>
    <!-- return个数 3个-->
    <module name="ReturnCount">
      <property name="max" value="3"/>
    </module>
    <!--try catch 异常处理数量 3-->
    <module name="NestedTryDepth ">
      <property name="max" value="3"/>
    </module>
    <!-- clone方法必须调用了super.clone() -->
    <module name="SuperClone"/>
    <!-- finalize 必须调用了super.finalize() -->
    <module name="SuperFinalize"/>

    <!-- 检查未被注释的main方法,排除以Appllication结尾命名的类 -->
    <module name="UncommentedMain">
        <property name="severity" value="error"/>
      <property name="excludedClasses" value=".*(Starter)|(Application)|(Test)|(Tests)$"/>
    </module>
    <!-- 禁止使用System.out.println -->
    <module name="Regexp">
      <property name="severity" value="error"/>
      <property name="format" value="System\.out\.println"/>
      <property name="illegalPattern" value="true"/>
      <property name="ignoreComments" value="true"/>
      <property name="message" value="禁止在代码内使用 System.out.println"/>
    </module>
  </module>
</module>
```