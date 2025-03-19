```
init_bucket  
store->svc()->sysobj->init_obj_ctx();  
store->getRados()->get_bucket_info() or store->getRados()->get_bucket_instance_info(）  
rgw_remove_object  
store->getRados()->delete_obj()  
If (params.versioning_status & BUCKET_VERSIONED || explicit_marker_version)  
if (instance.empty() || explicit_marker_version)  
if ((params.versioning_status & BUCKET_VERSIONS_SUSPENDED) == 0) {  
        store->gen_rand_obj_instance_name(&marker);  
else  
store->set_olh()  
get_obj_state()  
olh_init_modification()  
olh_init_modification_impl()  
setxattr()  
RGW_ATTR_ID_TAG  
RGW_ATTR_OLH_ID_TAG  
RGW_ATTR_OLH_VER  
bucket_index_link_olh()  
if (ret < 0)  
repair_olh()  
store->bi_get_instance()  
store->unlink_obj_instance()  
fi  
target->get_bucket_shard()  
store->svc.datalog_rados->add_entry()  
fi  
store->get_obj_head_ref()  
target->get_state()  
if (!real_clock::is_zero(params.unmod_since)) {  
store->cls_obj_check_mtime()  
Fi  
target->prepare_atomic_modification()  
index_op.prepare(CLS_RGW_OP_DEL, &state->write_tag, y);  
store->remove_rgw_head_obj(op);  
rgw_rados_operate()  
if (r >= 0)  
index_op.complete_del()  
target->complete_atomic_modification();  
else  
index_op.cancel()  
if (need_invalidate)  
target->invalidate_state();  
store->quota_handler->update_stats()











































```