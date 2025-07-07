```bash
apt install ipvsadm 
modprobe ip_vs  
modprobe ip_vs_rr  
modprobe ip_vs_wrr  
modprobe ip_vs_sh
```
确认服务 虚IP配置，RealIp配置
```
ipvsadm -A -t 10.10.1.21:5201 -s rr
ipvsadm -a -t 10.10.1.21:5201 -r 10.10.0.21:5201 -i （隧道模式）
ipvsadm -a -t 10.10.1.21:5201 -r 10.10.0.21:5201 -m （路由模式）
ipvsadm -L -n
```
删除的方法：
```
ipvsadm -D -t 10.10.1.21:5201
```