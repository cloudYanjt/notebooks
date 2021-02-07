# IDEA VM 参数设置

```
# 堆栈设置
-Xms4096m
-Xmx4096m
-Xmn3072m
-XX:MetaspaceSize=1024m
-XX:MaxMetaspaceSize=1024m
-XX:+AlwaysPreTouch
-XX:InitialCodeCacheSize=1200m
-XX:ReservedCodeCacheSize=1200m
-XX:+UseCompressedOops
-Dfile.encoding=UTF-8

# 采用何种垃圾回收参数
-XX:+UseConcMarkSweepGC
-XX:ParallelGCThreads=4
-XX:SoftRefLRUPolicyMSPerMB=50
-ea

# JIT 参数
-XX:CICompilerCount=2
-XX:TieredStopAtLevel=1
-XX:MaxInlineLevel=3
-XX:Tier4MinInvocationThreshold=100000
-XX:Tier4InvocationThreshold=110000
-XX:Tier4CompileThreshold=120000

-Dsun.io.useCanonPrefixCache=false
-Djava.net.preferIPv4Stack=true
-Djdk.http.auth.tunneling.disabledSchemes=""
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
-Djdk.attach.allowAttachSelf
-Xverify:none

-XX:ErrorFile=E:\logs\idea\java_error_in_idea_%p.log
-XX:HeapDumpPath=E:\logs\idea\java_error_in_idea.hprof

```
