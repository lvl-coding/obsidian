[https://blog.csdn.net/tph5559/article/details/141642217](https://blog.csdn.net/tph5559/article/details/141642217)  
==用户认证：S3用户身份认证机制基于密钥认证。用户在发送请求前，使用私有密钥和请求内容计算数字签名，并将数字签名和访问密钥封装在请求中发送给RGW。RGW收到请求后，使用访问密钥作为索引从RADOS集群中读取用户信息，并验证数字签名，以确保用户身份的真实性。==  
==权限控制：除了身份认证外，RGW还实现了对资源（如存储桶和对象）的访问权限控制。用户必须具备相应的访问权限（如读、写、删除）才能对资源进行操作。==