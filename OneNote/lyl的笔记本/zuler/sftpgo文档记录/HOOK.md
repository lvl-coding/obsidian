基本概念：  
hook是一种特殊的消息处理机制，它可以监视系统或者进程中的各种事件消息，截获发往目标窗口的消息并进行处理。所以说，我们可以在系统中自定义钩子，用来监视系统中特定事件的发生，完成特定功能
 
HOOK在使用http url时采用的是POST方法  
HOOK参数可定义为程序的绝对路径或HTTP URL

1. external_auth_hook
2. keyboard_interactive_auth_hook
3. pre_login_hook
4. actions
    
    1. 文件操作事件
    2. ==download==
    3. ==first-download==
    4. ==pre-download==
    5. ==upload==
    6. ==first-upload==
    7. ==pre-upload==
    8. ==delete==
    9. ==pre-delete==
    10. ==rename==
    11. ==mkdir==
    12. ==rmdir==
    13. ==ssh_cmd==
    14. ==copy==
    
    upload包括新上传和覆盖上传，如果由于配额导致上传失败，服务会尝试删除部分文件，所以会有配额不足和文件为0的异常。  
    first-download和first-upload要求操作成功，且不会覆盖掉普通的download和upload。  
    针对目录的操作，显式操作会触发，而隐式的自动创建删除不会触发  
    pre-*是同步的，可能导致客户端超市，其他操作是异步的
    
    16. 可支持后端数据库中对象的增删改操作
    17. ==user==
    18. ==folder==
    19. ==group==
    20. ==admin==
    21. ==api_key==
    22. ==share==
    23. ==event_action==
    24. ==event_rule==

pre-*操作和事件通知插件的区别在于可进行交互操作，而事件通知插件仅仅进行转发

6. post_connect_hook
7. post-login-hook
8. post_disconnect_hook

对无状态协议不会触发，比如http和webdav

10. check_password_hook
11. data_retention_hook