[[init_bucket]]
[[lyl的笔记本/ceph/rgw/OBJECT_RM|OBJECT_RM]]
bucket名称规则:
```cpp
rgw_bucket_parse_bucket_key
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
```