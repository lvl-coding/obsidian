# 预准备

# 预准备

2. 修改主机名
    
    1. sudo hostname cluster92/cluster91/cluster90
    2. sudo vim /etc/hostname
3. 修改host文件
    
    1. sudo vim /etc/hosts
    2. 每个节点增加其他两节点的主机名
4. 添加scadmin用户
    
    1. sudo useradd scadmin
    2. sudo passwd scadmin

# 软件安装

6. 安装docker和python3
    
    1. [【](https://blog.csdn.net/Stromboli/article/details/142486565)Docker】在 Ubuntu 上安装 Docker 的详细指南_ubuntu安装docker-CSDN博客
7. 下载cephadm包
    
    1. [Files](http://gitlab.zuler.sh/virt-infra/cloudspace/kvirtdesk-offline/-/tree/for-ceph) · for-ceph · vInfra / CloudSpace / kvirtdesk-offline · GitLab
    2. 解压后修改cephadm中配置参数
        
        1. 本次采用社区版本安装，直接使用的社区的P版本docker
            
            1. DEFAULT_IMAGE = 'ceph/daemon:latest-pacific'
    3. 安装ceph-common包
        
        1. sudo apt install -y ceph-common

# 集群配置

9. 初始化集群
    
    1. sudo cephadm bootstrap --fsid $(uuidgen) --config "ceph/initial-ceph.conf" --mon-ip "192.168.10.92" --cluster-network "192.168.10.0/24" --ssh-user "scadmin" --log-to-file --initial-dashboard-user "admin" --initial-dashboard-password "admin" --skip-monitoring-stack --allow-overwrite
10. 修改dashboard参数
    
    1. sudo cephadm shell **--** ceph config **set** mgr mgr**/**dashboard**/**standby_behaviour "error"
    2. sudo cephadm shell **--** ceph config **set** mgr mgr**/**dashboard**/**standby_error_status_code **503**
    3. sudo cephadm shell **--** ceph mgr module **enable** prometheus
    4. sudo cephadm shell **--** ceph mgr module disable dashboard
    5. sudo cephadm shell **--** ceph mgr module **enable** dashboard
    6. sudo cephadm shell **--** ceph config **set** mgr mgr**/**cephadm**/**device_enhanced_scan true
    7. sudo cephadm shell **-m** passwd.txt**:/**var**/**run**/**password.txt **--** ceph dashboard feature **enable** rbd
    8. sudo cephadm shell **-m** passwd.txt**:/**var**/**run**/**password.txt **--** ceph dashboard feature status
    9. sudo cephadm shell **-m** passwd.txt**:/**var**/**run**/**password.txt **--** ceph dashboard **set-grafana-api-password -i** **/**var**/**run**/**password.txt
    10. sudo cephadm shell **-m** passwd.txt**:/**var**/**run**/**password.txt **--** ceph dashboard **set-grafana-api-url** " [http://grafana.shcm.virt.io](http://grafana.shcm.virt.io)"
    11. sudo cephadm shell **-m** passwd.txt**:/**var**/**run**/**password.txt **--** ceph dashboard **set-alertmanager-api-host** " [http://192.168.10.92:9093](http://192.168.10.92:9093)"
    12. sudo cephadm shell **-m** passwd.txt**:/**var**/**run**/**password.txt **--** ceph dashboard **set-prometheus-api-host** " [http://192.168.10.92:9091](http://192.168.10.92:9091)"
    13. sudo cephadm shell **-m** passwd.txt**:/**var**/**run**/**password.txt **--** ceph dashboard **set-grafana-api-ssl-verify** false
    14. sudo cephadm shell **-m** passwd.txt**:/**var**/**run**/**password.txt **--** ceph dashboard **set-prometheus-api-ssl-verify** false
    15. sudo cephadm shell **-m** passwd.txt**:/**var**/**run**/**password.txt **--** ceph dashboard **set-alertmanager-api-ssl-verify** false
11. 拷贝ssh密钥到其他节点
    
    1. ssh-copy-id -f -i /etc/ceph/ceph.pub cluster91
    
    ssh-copy-id -f -i /etc/ceph/ceph.pub cluster90
    
12. 在dashboard页面或命令行添加其他节点
    
    1. sudo ceph orch host add cluster91 **192.168.10.91**
    2. sudo ceph orch host add cluster90 **192.168.10.90**
    3. sudo ceph orch host ls
13. 添加OSD
    
    1. sudo ceph orch daemon add osd cluster92**:/**dev**/**vdb**,/**dev**/**vdc
    2. sudo ceph orch daemon add osd cluster91**:/**dev**/**vdb**,/**dev**/**vdc
    3. sudo ceph orch daemon add osd cluster90**:/**dev**/**vdb**,/**dev**/**vdc
    4. 等待OSD创建完成……
14. 在dashboard页面创建rgw网关服务，此时在网关页面并看不到RGW服务状态，这是由于没有设置rgw用户权限提供给dashboard访问，后续添加后即可访问
15. 创建用户radosgw-admin user create --uid=user01 --display-name=user01 --system，保存返回的access_key和secret_key
16. 为dashboard配置用户密钥
    
    1. sudo ceph dashboard **set-rgw-api-access-key -i** access_key.txt
    2. sudo ceph dashboard **set-rgw-api-secret-key -i** secret_key.txt
    3. dashboard网关页面即可进行rgw的相关管理操作，可创建用户和bucket
    4. sudo ceph dashboard set-rgw-api-port *可修改dashboard中访问rgw网关服务的端口，要与实际rgw服务进程一致。
17. 下载s3 Browser验证RGW服务是否正常
    
    1. 添加用户信息时需取消掉TSL/SSL选项的勾选，目前RGW服务是通过http直接访问的

# sftpgo后台采用s3作存储的配置

19. 在admin页面对选定用户修改即可

|   |   |   |
|---|---|---|
|参数项|值||
|Storage|S3 (Compatible)||
|Bucket|test|对象网关创建的bucket，须已存在|
|Region|cn-north-1|虽然是自定义的s3服务，但仍要填写，留空则出错|
|Access Key|-|记录的access_key和|
|Access Secret|-|记录的secret_key|
|Endpoint|[http://192.168.10.92:80](http://192.168.10.92:80)|须携带http|

21. 勾选Skip TLS verify. This should be used only for testing
22. 通过client页面登陆后上传下载文件即可，同时可在s3 browser中看到文件变动

# 软件安装

# 集群配置

# sftpgo后台采用s3作存储的配置