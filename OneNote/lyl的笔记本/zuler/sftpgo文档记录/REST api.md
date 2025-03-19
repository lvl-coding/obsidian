SFTPGo 几乎支持所有 REST API，无论是针对管理员还是用户。  
```json  
int a;  
```  
REST API 使用 JSON Web Tokens (JWT) 或 API 密钥身份验证进行保护，并可通过 HTTPS 提供服务。除了 JWT 之外，您还可以配置客户端证书身份验证。
 
httpd可以通过密钥在配置中禁用 REST API enable_rest_api。
 
[http://192.168.10.89:8080/openapi/swagger-ui/](http://192.168.10.89:8080/openapi/swagger-ui/)
 
[https://github.com/drakkan/sftpgo/blob/2.6.x/openapi/openapi.yaml](https://github.com/drakkan/sftpgo/blob/2.6.x/openapi/openapi.yaml)