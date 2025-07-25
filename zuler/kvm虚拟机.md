```bash
$ qemu-img create -f qcow2 vda.qcow2 250G

$ virsh define vm.xml
$ virsh edit lyl-g

```

```xml
<domain type="kvm">
    <name>lyl-g</name> <!-- 虚拟机名称 -->
    <memory unit='Gb'>16</memory> <!-- 内存-->
    <cpu>8</cpu>
    <os>
        <type>hvm</type>  <!-- 虚拟机开机引导项设置为光盘(cdrom)-->
        <boot dev="cdrom"/> <!-- 虚拟机开机引导项设置为光盘-->
    </os>
    <cpu mode='host-model'> <!--使虚拟机也支持虚拟化，支持嵌套虚拟化，需要在虚拟机看到xmv指令集（/proc/cpuinfo） -->
    </cpu>
    <features>
        <acpi />
        <apic />
    </features>
    <clock offset="utc">
        <timer name="pit" tickpolicy="delay" />
        <timer name="rtc" tickpolicy="catchup" />
        <timer name="hpet" present="no" />
    </clock>
    <devices>
        <disk device="disk" type="file">
            <target bus="virtio" dev="vda" />
            <source file="/home/kvirtadm/vms/lyl/lyl-a/vda.qcow2" /> <!--系统盘 -->
            <driver type="qcow2" name="qemu" />
        </disk>
        <disk device="cdrom" type="file">
            <target bus="ide" dev="hdc" />
            <source file="/home/kvirtadm/vms/ubuntu-20.04.3-live-server-amd64.iso" />
            <driver type="raw" name="qemu" />
        </disk>
        <interface type="bridge"> <!--选择绑定了主机网卡的网桥 -->
            <source bridge="br0" />
            <model type="virtio" />
        </interface>
        <interface type="bridge">
            <source bridge="br1" />
            <model type="virtio" />
        </interface>
        <interface type="bridge">
            <source bridge="br2" />
            <model type="virtio" />v
        </interface>
        <input type="tablet" bus="usb" />
        <graphics type="vnc" autoport="yes" keymap="en-us" listen="0.0.0.0" /> <!--开启VNC -->
        <console type='pty'> <!--开启串口，可以快速登录服务器进行配置 -->
            <target type='serial' port='0'/>
        </console>
        <video>
            <model type="cirrus" />
        </video>
        <memballoon model="virtio">
            <stats period="10" />
        </memballoon>
    </devices>
</domain>
```