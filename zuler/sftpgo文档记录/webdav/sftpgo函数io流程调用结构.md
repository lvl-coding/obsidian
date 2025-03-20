HTTP服务框架 net/http
 
- Initialize
    
    - mimeTypeCache
    - webDAVUsersCache
    - exitChannel
    - Bindings
        
        - listenAndServe
        - HTTPListenAndServe
            
            - ServeHTTP
                
                - GetFilesystemForPath
                    
                    - GetFilesystem
                        
                        - NewS3Fs
            - LimitRate
   

- ServeHTTP
- LimitRate
- authenticate
- NewBaseConnection
- updateLoginMetrics
- Handler
    
    - handleOptions
    - handleGetHeadPost
    - handleDelete
    - handlePut
    - handleMkcol
    - handleCopyMove
    - handleLock
    - handleUnlock
    - handlePropfind
    - handleProppatch
   

- handlePut
    
    - stripPrefix
    - confirmLocks
    - f = OpenFile
        
        - UpdateLastActivity
        - GetFsAndResolvedPath获取后端文件系统
        - getFile// Download, Stat, Readdir or simply open/close
        - putFile(vfs.Fs...
            
            - fs.Lstat
            - handleUploadToNewFile
                
                - HasSpace
                - ExecutePreAction
                - fs.Create
                - SetPathPermissions
                - NewBaseTransfer
                - newWebDavFile
            - handleUploadToExistingFile
                
                - HasSpace
                - ExecutePreAction
                - AtomicUpload
                    
                    - fs.Rename
                - fs.Create
                - GetMaxWriteSize
                - NewBaseTransfer
                - newWebDavFile
    - io.Copy(f, r.Body)
    - Stat
    - Close
 
- handleGetHeadPost
    
    - f = OpenFile
        
        - getFile
            
            - newWebDavFile
    - ServeContent
        
        - CopyN
            
            - Read
                
                - Fs.Open
   

s3读写使用pipeat传输数据，并在用户的homedir下创建临时文件，homedir为空时则在系统的tmp目录下，创建后即删除，待句柄关闭后则自动删除文件

![Exported image](Exported%20image%2020250319155937-0.png)  
![Exported image](Exported%20image%2020250319155939-1.png)