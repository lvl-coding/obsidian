[WebDAV](https://fullstackplayer.github.io/WebDAV-RFC4918-CN/) 规范文档（RFC4918）中文版 | WebDAV 规范文档

[HTTP Extensions for Web Distributed Authoring and Versioning (WebDAV) 4918](https://datatracker.ietf.org/doc/html/rfc4918)

[HTTP Extensions for Web Distributed Authoring and Versioning (WebDAV) 2518](https://datatracker.ietf.org/doc/html/rfc2518)
# PUT请求创建了文件

在现有资源上执行的PUT将替换资源GET得到的响应实体。在PUT处理期间，资源上定义的属性可以重新计算，但是资源本身内容不受影响。例如，如果服务器识别出请求body的内容类型，则它可能能够自动从属性中提取有帮助的信息。  
PUT请求会导致一个资源的创建，如果没有恰当的父集合空间，PUT必须失败，并显示409（冲突）。  
PUT请求允许客户端指示实体body具有哪种媒体类型，以及是否应重写该媒体类型。因此，客户端应为新资源提供Content-Type（如果已知）header。如果客户端没有为新资源提供Content-Type，则服务器可能会创建未分配Content-Type的资源，或者也可能会尝试分配一个Content-Type。  
请注意，尽管服务端接收者通常应将HTTP请求提供的元数据视为靠谱的，但实际上并不能保证服务器将接受客户端提供的元数据（例如，任何以“Content-”开头的请求标头）。首先，许多服务器不允许在每个资源的基础上配置Content-Type。因此，客户端不能总是拥有依靠包含Content-Type的请求header来直接影响内容类型的能力。  
不支持批量创建，可能返回不允许使用的405错误码，创建目录应该使用MKCOL
 
# PROPFIND 

PROPFIND method负责检索资源的属性  
infinity资源深度  
prop获取指定命名的属性  
propname获取全部属性名称  
allprop获取全部属性  
文件属性以XML形式传输

# PROPPATCH

设置或删除资源上的属性，具有原子特性  
propertyupdate 

# MKCOL

创建一个新的集合，已有资源则失败

# GET/HEAD

检索资源信息

# POST

由服务测定义

# DELETE

删除资源，并销毁资源的锁

# COPY

从源url拷贝到Destination header中  
固定属性也会被复制，但活属性的值根据计算方法导致可能不同  
可设置Depth header拷贝深度，父目录拷贝失败会影响子成员，但不会影响其他同级目录  
Overwriting header决定是否覆盖，覆盖集合不会合并子成员，而是完全替换

# MOVE

copy后删除源资源

# LOCK

共享锁和独享锁  
创建锁  
刷新锁  
Depth集合锁  
锁定空的URL

# UNLOCK

Lock-Token header标记锁