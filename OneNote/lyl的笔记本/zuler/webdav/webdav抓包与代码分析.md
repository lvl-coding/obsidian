![Exported image](Exported%20image%2020250319155946-0.png)

下载请求会对文件进行拆分，分段下载，分段大小为0-1048575 1024*1204-1
 ![Exported image](Exported%20image%2020250319155948-1.png)

http下载分包规则，会按照1380bytes字节的大小分为多个HTTP请求进行文件下载
 ![Exported image](Exported%20image%2020250319155950-2.png)

后端S3同样采用分段下载，分段大小为1048575 1024*1204-1
 
PUT请求后会对PUT的文件进行PROPFIND操作
   

# S3操作

PUT生成个空文件  
获取文件信息，获取文件的同名目录信息
 
|   |   |   |   |   |
|---|---|---|---|---|
|客户端行为|端口|webdav报文|操作计数|报文结果|
|存在旧记录自动尝试连接/目录初始映射|1/2|options /|2|401 Unauthorized|
|输入密码|3|options /|1|401 Unauthorized|
||3|options /携带用户名密码|长时间后的首次操作出现|Allow: OPTIONS, LOCK, DELETE, PROPPATCH, COPY, MOVE, UNLOCK, PROPFIND|
||4|PROPFIND /无账户密码|1|401 Unauthorized|
||4|PROPFIND / depth 0|2|自身目录信息|
|打开根目录|4|PROPFIND /Desktop.ini|1|1|
||4|PROPFIND / depth 1|3|自身目录信息和子目录信息|
|打开子目录，目录下27个项目|4|PROPFIND /s3 depth 1|1|自身目录信息和子目录信息，子目录文件较多，自动分包|
|||PROPFIND /s3/Desktop.ini|1|404 Not Found|
|||PROPFIND /s3/draw|2||
|||PROPFIND /s3/typora|1||
|||PROPFIND /s3/netease|1||
|||PROPFIND /s3/wireshark|1||
|||PROPFIND /s3/msys2|3||
|新端口连接|5|PROPFIND /s3 depth 1|1|自身目录信息和子目录信息，子目录文件较多，自动分包|
|||PROPFIND /s3/draw|2||
|||PROPFIND /s3/typora|1||
|||PROPFIND /s3/wireshark|1||
|||PROPFIND /s3/postman|2||
|||PROPFIND /s3/vSCODE|1||
|新端口连接|6|PROPFIND /s3/git|2||
|||PROPFIND /s3/lghub|2||
|||PROPFIND /s3/wireshark|2||
|||PROPFIND /s3/vSCODE|1||
|新端口连接|7|PROPFIND /s3/lghub|1||
|||PROPFIND /s3/netease|1||
|||PROPFIND /s3/git|2||
|||PROPFIND /s3/postman|1||
|||PROPFIND /s3/wireshark|1||
|||PROPFIND /s3/vSCODE|1||
|新端口连接|8|PROPFIND /s3/netease|2||
|||PROPFIND /s3/git|1||
|||PROPFIND /s3/draw|1||
|||PROPFIND /s3/lghub|1||
|||PROPFIND /s3/postman|2||
|||PROPFIND /s3/vSCODE|1||
|新端口连接|9|PROPFIND /s3/typora|3||
|||PROPFIND /s3/lghub|1||
|||PROPFIND /s3/netease|1||
|||PROPFIND /s3/msys2|2||
|||PROPFIND /s3/VSCODE|1||
==右键文件。端口重新计数，旧端口超时断开== ==10== ==PROPFIND /s3/wireshark== ==5====  
==
==  
====  
====  
PROPFIND /s3 depth 0
====1====  
==
==  
====  
====  
PROPFIND /s3/wireshark
====2====  
==
==  
====  
====  
PROPFIND /AutoRun.inf
====1== ==404 Not Found==
==  
====11== ==PROPFIND /== ==1====  
==
==  
====  
====  
PROPFIND /desktop.ini
====1== ==404 Not Found==
==  
====  
====PROPFIND /s3/wireshark== ==7====  
==
||||||
    
==100KB*1000分布在10*2级目录下的上传Webdav OP统计==

21. ==本地使用脚本生成1000个100KB文件，并随机分布在两级目录下，每个级别有10个目录==
22. ==sftpgo管理页面中创建虚拟目录，后端使用S3对象存储，将虚拟目录映射到用户s3目录下==
23. ==使用windows网络磁盘映射功能，将webdav网盘映射为本地磁盘==
24. ==分别在windows中使用wireshark抓取与webdav服务侧的报文和在sftpgo服务器上使用tcpdump抓取与S3对象存储的报文==
25. ==拷贝1中生成的目录到webdav网络磁盘中的s3目录下==
26. ==windows与sftpgo服务端报文交互抓包统计==

  ==Webdav http分组计数器统计：==  

```
=======================================================================================================================================

HTTP / Packet Counter:

Packet Type             Count         Average       Min Val       Max Val       Rate (ms)     Percent       Burst Rate    Burst Start

---------------------------------------------------------------------------------------------------------------------------------------

  
HEAD                  
2000
                                                    
0.0006
        
20.32
%        
0.0200
        
20.734
     

  
LOCK                  
1000
                                                    
0.0003
        
10.16
%        
0.0100
        
20.576
     

  
MKCOL                 
111
                                                     
0.0000
        
1.13
%         
0.0100
        
14.467
     

  
PROPFIND              
1505
                                                    
0.0005
        
15.29
%        
0.0200
        
2.476
      

  
PROPPATCH             
2222
                                                    
0.0007
        
22.58
%        
0.0100
        
15.455
     

  
PUT                   
2000
                                                    
0.0006
        
20.32
%        
0.0100
        
18.846
     

  
UNLOCK                
1000
                                                    
0.0003
        
10.16
%        
0.0100
        
22.659
     

---------------------------------------------------------------------------------------------------------------------------------------


=======================================================================================================================================

HTTP / Packet Counter:

Packet Type             Count         Average       Min Val       Max Val       Rate (ms)     Percent       Burst Rate    Burst Start 

---------------------------------------------------------------------------------------------------------------------------------------

  
PUT                   
2111
                                                    
0.0008
        
7.08
%         
0.0100
        
9.893
       

  
HEAD                  
24747
                                                   
0.0094
        
82.96
%        
0.0800
        
19.882
      

  
GET                   
2971
                                                    
0.0011
        
9.96
%         
0.0300
        
14.060
      

---------------------------------------------------------------------------------------------------------------------------------------































=======================================================================================================================================

HTTP / Packet Counter:

Packet Type             Count         Average       Min Val       Max Val       Rate (ms)     Percent       Burst Rate    Burst Start

---------------------------------------------------------------------------------------------------------------------------------------

  
GET                   
1000
                                                    
0.0055
        
57.08
%        
0.0200
        
29.357
     

  
PROPFIND              
748
                                                     
0.0041
        
42.69
%        
0.0200
        
3.100
      

---------------------------------------------------------------------------------------------------------------------------------------

=======================================================================================================================================

HTTP / Packet Counter:

Packet Type             Count         Average       Min Val       Max Val       Rate (ms)     Percent       Burst Rate    Burst Start 

--------------------------------------------------------------------------------------------------------------------------------------- 

  
HEAD                  
4277
                                                    
0.0013
        
75.41
%        
0.0600
        
2104.481
    

  
GET                   
1395
                                                    
0.0004
        
24.59
%        
0.0300
        
10.612
      

---------------------------------------------------------------------------------------------------------------------------------------
























































































































































































```
 HTTP / Packet Counter:  
Packet Type             Count         Average       Min Val       Max Val       Rate (ms)     Percent       Burst Rate    Burst Start  
---------------------------------------------------------------------------------------------------------------------------------------  
  HEAD                  2000                                                    0.0006        20.32%        0.0200        20.734       
  LOCK                  1000                                                    0.0003        10.16%        0.0100        20.576       
  MKCOL                 111                                                     0.0000        1.13%         0.0100        14.467       
  PROPFIND              1505                                                    0.0005        15.29%        0.0200        2.476        
  PROPPATCH             2222                                                    0.0007        22.58%        0.0100        15.455       
  PUT                   2000                                                    0.0006        20.32%        0.0100        18.846       
  UNLOCK                1000                                                    0.0003        10.16%        0.0100        22.659       
---------------------------------------------------------------------------------------------------------------------------------------  
==S3 http分组计数器统计==  
=======================================================================================================================================  
HTTP / Packet Counter:  
Packet Type             Count         Average       Min Val       Max Val       Rate (ms)     Percent       Burst Rate    Burst Start   
---------------------------------------------------------------------------------------------------------------------------------------  
  PUT                   2111                                                    0.0008        7.08%         0.0100        9.893         
  HEAD                  24747                                                   0.0094        82.96%        0.0800        19.882        
  GET                   2971                                                    0.0011        9.96%         0.0300        14.060        
---------------------------------------------------------------------------------------------------------------------------------------  
==单个操作的报文请求==

|
|
==文件操作== ==webdav报文请求==  ==返回结果== ==s3报文请求== ==返回结果== ==重复计数==
==文件操作== ==webdav报文请求==  ==返回结果== ==s3报文请求== ==返回结果== ==重复计数==
|文件|PROPFIND /s3/test100K/L1_1/L2_1/file1.txt|404 Not Found|HEAD /test/test100K/L1_1/L2_1/file1.txt|404 Not Found|2|
||PUT /s3/test100K/L1_1/L2_1/file1.txt|201 Created|GET /test?list-type=2&max-keys=2&prefix=test100K/L1_1/L2_1/file1.txt/|count 0||
||LOCK /s3/test100K/L1_1/L2_1/file1.txt|Lock-Token: <1736941106>|HEAD /test/test100K/L1_1/L2_1/file1.txt/|404 Not Found||
||PROPPATCH /s3/test100K/L1_1/L2_1/file1.txt|自身元数据信息|PUT /test/test100K/L1_1/L2_1/file1.txt [Content length: 0]|200 OK|1|
||HEAD /s3/test100K/L1_1/L2_1/file1.txt|401 Unauthorized|HEAD /test/test100K/L1_1/L2_1/file1.txt|元数据信息|8|
||HEAD /s3/test100K/L1_1/L2_1/file1.txt|Lock-Token: <1736941106>|HEAD /test/test100K/L1_1/L2_1/file1.txt/|404 Not Found||
||PUT /s3/test100K/L1_1/L2_1/file1.txt Content-Length: 102400|201 Created|PUT /test/test100K/L1_1/L2_1/file1.txt [Content length: 102400]|200 OK|1|
||PROPPATCH /s3/test100K/L1_1/L2_1/file1.txt|自身元数据信息|HEAD /test/test100K/L1_1/L2_1/file1.txt|元数据信息|3|
||UNLOCK /s3/test100K/L1_1/L2_1/file1.txt|204 No Content||||
|目录|PROPFIND /s3/test100K/L1_1/L2_1 Depth: 0|自身目录信息|HEAD /test/test100K/L1_9/L2_2|404 Not Found|2|
||MKCOL /s3/test100K/L1_1|201 Created|GET /test?list-type=2&max-keys=2&prefix=test100K/L1_1/L2_1/file1.txt/|count 0||
||PROPPATCH /s3/test100K/L1_1/L2_1|自身元数据信息|HEAD /test/test100K/L1_9/L2_2/|404 Not Found||
||PROPFIND /s3/test100K/L1_1/L2_1 Depth: 0|自身目录信息|PUT /test/test100K/L1_9/L2_2/|200 OK|1|
||PROPPATCH /s3/test100K/L1_1/L2_1|自身元数据信息|HEAD /test/test100K/L1_9/L2_2|404 Not Found|5|
||||GET /test?list-type=2&max-keys=2&prefix=test100K/L1_1/L2_1/file1.txt/|count 1||
||||HEAD /test/test100K/L1_9/L2_2/|元数据信息||

==100KB*1000分布在10*2级目录下的下载OP统计==  
=======================================================================================================================================  
HTTP / Packet Counter:  
Packet Type             Count         Average       Min Val       Max Val       Rate (ms)     Percent       Burst Rate    Burst Start  
---------------------------------------------------------------------------------------------------------------------------------------  
  GET                   1000                                                    0.0055        57.08%        0.0200        29.357       
  PROPFIND              748                                                     0.0041        42.69%        0.0200        3.100        
---------------------------------------------------------------------------------------------------------------------------------------  
=======================================================================================================================================  
HTTP / Packet Counter:  
Packet Type             Count         Average       Min Val       Max Val       Rate (ms)     Percent       Burst Rate    Burst Start   
---------------------------------------------------------------------------------------------------------------------------------------   
  HEAD                  4277                                                    0.0013        75.41%        0.0600        2104.481      
  GET                   1395                                                    0.0004        24.59%        0.0300        10.612        
---------------------------------------------------------------------------------------------------------------------------------------
   

|
|
==文件操作== ==报文请求==  ==返回结果== ==备注== ==s3报文请求== ==重复计数==
|文件|GET /s3/test100K/L1_1/L2_1/file1.txt|201 Created||GET /test/test100K/L1_1/L2_1/file398.txt|1|
||PROPPATCH /s3/test100K/L1_1/L2_1/file1.txt|自身元数据信息|获取文件的元数据信息比例约为总体的50%，获取策略未发现|HEAD /test/test100K/L1_1/L2_1/file398.txt|3或4共计3994|
||PROPPATCH /s3/test100K/L1_1/L2_1|自身元数据信息||||
      
       
## 上传异常

虚拟磁盘驱动器并没有执行上传数据操作，而是生成了一个/s3/node-v22.12.0-x64.msi:SmartScreen:$DATA的文件自行上传，且内容长度不正确。
   

# S3操作
               

|   |   |   |   |   |   |   |   |
|---|---|---|---|---|---|---|---|
|tcp端口\webdav报文|4|5|6|7|8|9|sum|
|PROPFIND /s3/mo||||||||
|PROPFIND /s3/7z||||||||
|PROPFIND /s3/xlsx||||||||
|PROPFIND /s3/zip||||||||
|PROPFIND /s3/chrome||||||||
|PROPFIND /s3/draw|2|2|||1|||
|PROPFIND /s3/git|||2|2|1|||
|PROPFIND /s3/go||||||||
|PROPFIND /s3/offline||||||||
|PROPFIND /s3/lghub|||2|1|1|1||
|PROPFIND /s3/mingw-build||||||||
|PROPFIND /s3/mingw-get||||||||
|PROPFIND /s3/msys2|3|||||2||
|PROPFIND /s3/netease|1|||1|2|1||
|PROPFIND /s3/node||||||||
|PROPFIND /s3/note||||||||
|PROPFIND /s3/nacap||||||||
|PROPFIND /s3/npp||||||||
|PROPFIND /s3/onenote||||||||
|PROPFIND /s3/postman||2||1|2|||
|PROPFIND /s3/s3browser||||||||
|PROPFIND /s3/typora|1|1||||3||
|PROPFIND /s3/script||||||||
|PROPFIND /s3/VNC||||||||
|PROPFIND /s3/vSCODE||1|1|1|1|1||
|PROPFIND /s3/wireshark|1|1|2|1||||
|sum|8|7|7|7|8|8||