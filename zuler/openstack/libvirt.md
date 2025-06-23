Nova 使用 libvirt 管理 QEMU/KVM 虚机
# Nova 中 libvirt 的使用
Nova 使用 libvirt 来管理虚机，包括：
- 创建虚机
- 虚机的生命周期管理
- 添加和删除连接到别的网络的网卡 （interface）
- 添加和删除 Cinder 卷 （volume）

![[Pasted image 20250612165910.png]]
![[Pasted image 20250612165921.png]]
Libvirtd是目前使用最广泛的对kvm虚拟机进行管理的工具和api。Libvirtd是一个Domain进程可以被本地virsh调用，也可以被远端的virsh调用，libvirtd调用kvm-qemu控制虚拟机。
libvirtd由几个不同的部分组成，其中包括应用程序编程接口（API）库，一个守护进程（libvirtd）和一个默认的命令行工具(virsh)，libvirtd守护进程负责对虚拟机的管理，因此要确保这个进程的运行。
![[Pasted image 20250612174956.png]]