配置参数：  
"webdavd"**:** **{**  
"bindings"**:** **[**  
**{**  
"port"**:** **0****,**  
"address"**:** ""**,**  
"enable_https"**:** false**,**  
"certificate_file"**:** ""**,**  
"certificate_key_file"**:** ""**,**  
"min_tls_version"**:** **12****,**  
"client_auth_type"**:** **0****,**  
"tls_cipher_suites"**:** **[]****,**  
"tls_protocols"**:** **[]****,**  
"prefix"**:** ""**,**  
"proxy_allowed"**:** **[]****,**  
"client_ip_proxy_header"**:** ""**,**  
"client_ip_header_depth"**:** **0****,**  
"disable_www_auth_header"**:** false  
**}**  
**]****,**  
"certificate_file"**:** ""**,**  
"certificate_key_file"**:** ""**,**  
"ca_certificates"**:** **[]****,**  
"ca_revocation_lists"**:** **[]****,**  
"cors"**:** **{**  
"enabled"**:** false**,**  
"allowed_origins"**:** **[]****,**  
"allowed_methods"**:** **[]****,**  
"allowed_headers"**:** **[]****,**  
"exposed_headers"**:** **[]****,**  
"allow_credentials"**:** false**,**  
"max_age"**:** **0****,**  
"options_passthrough"**:** false**,**  
"options_success_status"**:** **0****,**  
"allow_private_network"**:** false  
**}****,**  
"cache"**:** **{**  
"users"**:** **{**  
"expiration_time"**:** **0****,**  
"max_size"**:** **50**  
**}****,**  
"mime_types"**:** **{**  
"enabled"**:** true**,**  
"max_size"**:** **1000****,**  
"custom_mappings"**:** **[]**  
**}**  
**}**  
**}****,**  
可通过bindings结构指定多个webdav服务端  
prefix默认为空，或以根目录开头eg./dev
 
webdav没有会话概念，每个命令都是一个单独的 HTTP 请求，必须经过身份验证，为了提高性能，SFTPGo 会缓存经过身份验证的用户。  
用户缓存配置：  
expiration_time //分钟，命中缓存并不会更新用户的last_login和触发其他登录验证类的hook  
max_size //0表示无限制，超出限制时过多时则将最老的用户记录释放
 
webdav需要每个文件的MIME类型，首先会通过扩展名猜测文件类型，猜测失败则向后端(本地做存储则直接获取文件属性?)发送HEAD请求，最终会尝试读取前512个字节来判断类型。可配置MIME缓存来保存首次判断的结果，默认为内存缓存，可配置/etc/mime.types为持久化缓存
 
启用反向代理时必须保留Host表头，否则COPY/MOVE操作将失败，比如apache的配置ProxyPreserveHost On
 
已知缺陷：  
循环删除目录树中最后一个目录时会有not found的错误  
读取目录需要下载和列举权限，上传文件需要上传和列举权限  
列举目录时如果文件没有访问权限则会被省略，如果有其他异常，则列举会失败。其他sftp和ftp协议则是在访问无权限的文件时才会出错。  
windows使用webdav的非https时需要修改注册表，默认的大小限制是50MB
 
webdav有对用户自定义dead属性的最小实现，支持设置修改时间，不过会在live属性中体现。建议通过插件的形式将自定义属性保存在其他数据库中，支持X-OC-Mtime设置修改时间