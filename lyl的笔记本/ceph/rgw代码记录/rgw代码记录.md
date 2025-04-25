|   |
|---|
```
rgw_bucket_parse_bucket_key
```
bucket名称规则|
      

|
|
==
1  
2  
3  
4  
5  
6  
7  
8  
9  
10  
11  
12  
13  
14  
15  
16  
17
====
static int get_user_policy_from_attr(const DoutPrefixProvider *dpp,  
                                     CephContext * const cct,  
             rgw::sal::RGWRadosStore * const store,  
             map<string, bufferlist>& attrs,  
             RGWAccessControlPolicy& policy    /* out */)  
{  
  auto aiter = attrs.find(RGW_ATTR_ACL);  
  if (aiter != attrs.end()) {  
    int ret = decode_policy(dpp, cct, aiter->second, &policy);  
    if (ret < 0) {  
      return ret;  
    }  
  } else {  
    return -ENOENT;  
  }  
  return 0;  
}
==