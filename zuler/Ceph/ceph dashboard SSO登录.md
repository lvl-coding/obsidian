# dashboard端配置
1. 参考社区文档描述[ceph dashboard启用SSO登录](https://docs.ceph.com/en/pacific/mgr/dashboard/#enabling-single-sign-on-sso)
```
$ceph dashboard sso setup saml2 <ceph_dashboard_base_url> <idp_metadata> {<idp_username_attribute>} {<idp_entity_id>} {<sp_x_509_cert>} {<sp_private_key>}
Parameters:
	<ceph_dashboard_base_url>: Base URL where Ceph Dashboard is accessible (e.g., https://cephdashboard.local)
	<idp_metadata>: URL to remote (http://, https://) or local (file://) path or content of the IdP metadata XML (e.g., https://myidp/metadata, file:///home/myuser/metadata.xml).
	<idp_username_attribute> _(optional)_: Attribute that should be used to get the username from the authentication response. Defaults to uid.
	<idp_entity_id> _(optional)_: Use this when more than one entity id exists on the IdP metadata.
	<sp_x_509_cert> / <sp_private_key> _(optional)_: File path of the certificate that should be used by Ceph Dashboard (Service Provider) for signing and encryption.
```
2. dashboard的metadata XML 为`<ceph_dashboard_base_url>/auth/saml2/metadata`url中，用于提供给ZIDATEL进行配置
3. idp_metadata即为ZIDATEL的metadata XML信息，为`https://sftpgouser-bd6yhs.us1.zitadel.cloud/saml/v2/metadata`，
4. 从上述XML文件中可知ZIDADEl中并未返回uid作为用户名，因此需指定`idp_username_attribute`为Username

# ZITADEL端配置
1. 新增Project
![[Pasted image 20250321154117.png]]
2. 新增application
![[Pasted image 20250321154208.png]]
3. 指定SAML
![[Pasted image 20250321154344.png]]
4. 准备dashboard的metadata XML文件， 由于集群dashboard为内网，ZITADEL无法访问XML页面，因此下载`<ceph_dashboard_base_url>/auth/saml2/metadata`文件，保存在本地，采用上传文件的方式
5. 向ZITADEL上传dashboard的metadata文件
![[Pasted image 20250321155033.png]]
6. 创建application完成
![[Pasted image 20250321155121.png]]
![[Pasted image 20250321155236.png]]
7. application中配置登录用户的权限
![[Pasted image 20250321161336.png]]
![[Pasted image 20250321161409.png]]
![[Pasted image 20250321161435.png]]
# 验证登录成功