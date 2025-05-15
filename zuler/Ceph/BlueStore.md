网页资料：
https://www.cnblogs.com/hukey/p/11910741.html
https://blog.csdn.net/easonwx/article/details/125949915
https://blog.csdn.net/DeamonXiao/article/details/120866790
[Bluestore下的OSD开机自启动分析](https://cloud.tencent.com/developer/article/1171493)
# 优势
- 对于大型写入操作，避免了原来FileStore的两次写入
- 对于小型写入，BlueStore比FileStore with journal性能还要好
- 对于Key/value数据BlueStore性能明显提升
- 当集群数据接近爆满时，BlueStore避免了FileStore的性能陡降问题
- 在BlueStore上使用raw库的小型顺序读性能有降低，和BlueStore不采用预读有关，但可以通过上层接口来实现性能提升
- BlueStore在RBD卷或CephFS文件中采用了copy-on-write提升了性能
![[filestore-vs-bluestore-2.png.webp]]
![[Pasted image 20250515161242.png]]
# LVM基本构架
一个简单LVM逻辑卷的组成如下
![[Pasted image 20250514154517.png]]
- LVM逻辑卷的基本物理存储单元是块设备，比如一个分区或者整张磁盘，将这个设备初始化为LVM物理卷(PV)
- 要创建一个LVM逻辑卷，就要将物理卷合并到卷组(VG)中。这就生成了磁盘空间池，用它可分配LVM逻辑卷(LV)。这个过程和将磁盘分区的过程类似。逻辑卷由文件系统和应用程序(比如数据库)使用
# LVM tag简介
![[Pasted image 20250514154800.png]]
- tag信息存储在PV的metadata中
- tag名称最长可为1024个字符的字符串。可使用小横线开始。允许的字符包括`[A-Za-z0-9_+.-]`，后续扩大包含"/"、"="、"!"、":"、"#" 和"&" 字符
# BlueStore打标签
打标签是在OSD初始化的prepare阶段进行，各种tag的含义直接在源码中标注一下，具体代码如下：
```python
#ceph-12.2.5/src/ceph-volume/ceph_volume/devices/lvm/prepare.py
def prepare(self, args):
        # FIXME we don't allow re-using a keyring, we always generate one for the
        # OSD, this needs to be fixed. This could either be a file (!) or a string
        # (!!) or some flags that we would need to compound into a dict so that we
        # can convert to JSON (!!!)
        secrets = {'cephx_secret': prepare_utils.create_key()}
        cephx_lockbox_secret = ''
        encrypted = 1 if args.dmcrypt else 0
        cephx_lockbox_secret = '' if not encrypted else prepare_utils.create_key()
        if encrypted:
            secrets['dmcrypt_key'] = encryption_utils.create_dmcrypt_key()
            secrets['cephx_lockbox_secret'] = cephx_lockbox_secret
        cluster_fsid = conf.ceph.get('global', 'fsid')
        osd_fsid = args.osd_fsid or system.generate_uuid()
        crush_device_class = args.crush_device_class
        if crush_device_class:
            secrets['crush_device_class'] = crush_device_class
        # reuse a given ID if it exists, otherwise create a new ID
        self.osd_id = prepare_utils.create_id(osd_fsid, json.dumps(secrets), osd_id=args.osd_id)
        tags = {
            'ceph.osd_fsid': osd_fsid, #osd 的uuid，目前官方是通过ceph osd new UUID去创建OSD ID
            'ceph.osd_id': self.osd_id, #osd 的ID序号
            'ceph.cluster_fsid': cluster_fsid, #ceph集群的fsid
            'ceph.cluster_name': conf.cluster, #集群名称，默认是ceph
            'ceph.crush_device_class': crush_device_class, #存储介质类型，目前默认提供HDD和SSD
        }
        if args.filestore:
            if not args.journal:
                raise RuntimeError('--journal is required when using --filestore')
            data_lv = self.get_lv(args.data)
            if not data_lv:
                data_lv = self.prepare_device(args.data, 'data', cluster_fsid, osd_fsid)
            tags['ceph.data_device'] = data_lv.lv_path #LV 逻辑卷的块设备地址            tags['ceph.data_uuid'] = data_lv.lv_uuid #LV 逻辑卷的UUID
            tags['ceph.cephx_lockbox_secret'] = cephx_lockbox_secret #加密密钥
            tags['ceph.encrypted'] = encrypted #是否加密
            tags['ceph.vdo'] = api.is_vdo(data_lv.lv_path) #是否为VDO设备
            journal_device, journal_uuid, tags = self.setup_device('journal', args.journal, tags)
            tags['ceph.type'] = 'data' #这个对应Filestore的数据存储类型
            data_lv.set_tags(tags)
            prepare_filestore(
                data_lv.lv_path,
                journal_device,
                secrets,
                tags,
                self.osd_id,
                osd_fsid,
            )
        elif args.bluestore:
            block_lv = self.get_lv(args.data)
            if not block_lv:
                block_lv = self.prepare_device(args.data, 'block', cluster_fsid, osd_fsid)
            tags['ceph.block_device'] = block_lv.lv_path
            tags['ceph.block_uuid'] = block_lv.lv_uuid
            tags['ceph.cephx_lockbox_secret'] = cephx_lockbox_secret
            tags['ceph.encrypted'] = encrypted
            tags['ceph.vdo'] = api.is_vdo(block_lv.lv_path)
            wal_device, wal_uuid, tags = self.setup_device('wal', args.block_wal, tags)
            db_device, db_uuid, tags = self.setup_device('db', args.block_db, tags)
            tags['ceph.type'] = 'block' #这个对应Bluestore的数据存储类型
            block_lv.set_tags(tags) #设置LVM的tag标记
            prepare_bluestore(
                block_lv.lv_path,
                wal_device,
                db_device,
                secrets,
                tags,
                self.osd_id,
                osd_fsid,
            )
```
最终的效果可以通过下面命令查看
```
[root@demo cephuser]# /usr/sbin/lvs --noheadings --readonly --separator="   " -o lv_tags,lv_path,lv_name,vg_name,lv_uuid
     /dev/centos/root   root   centos   fq7hUc-BtCA-AeIk-VvBD-Mxad-7uix-ZtK4I6
     /dev/centos/swap   swap   centos   r7oGD8-Tf15-v4ir-eOR3-LeDg-qqrf-gblNWI
  ceph.block_device=/dev/ceph-508f2cbf-23e3-4b62-a6e1-7143b00d08dd/osd-block-903b1698-9271-4040-8c8f-cd09417a7aaa,ceph.block_uuid=261j3z-Sk9A-LVQS-dFrB-YnvD-ZcaW-x8IdUf,ceph.cephx_lockbox_secret=,ceph.cluster_fsid=21cc0dcd-06f3-4d5d-82c2-dbd411ef0ed9,ceph.cluster_name=ceph,ceph.crush_device_class=None,ceph.encrypted=0,ceph.osd_fsid=903b1698-9271-4040-8c8f-cd09417a7aaa,ceph.osd_id=1,ceph.type=block,ceph.vdo=0   /dev/ceph-508f2cbf-23e3-4b62-a6e1-7143b00d08dd/osd-block-903b1698-9271-4040-8c8f-cd09417a7aaa   osd-block-903b1698-9271-4040-8c8f-cd09417a7aaa   ceph-508f2cbf-23e3-4b62-a6e1-7143b00d08dd   261j3z-Sk9A-LVQS-dFrB-YnvD-ZcaW-x8IdUf
```
# ceph-volume服务自启动OSD
查看`/usr/lib/systemd/system/ceph-volume@.service`，ceph-volume服务对应的内容如下：
```
[Unit]
Description=Ceph Volume activation: %i
After=local-fs.target
Wants=local-fs.target

[Service]
Type=oneshot
KillMode=none
Environment=CEPH_VOLUME_TIMEOUT=10000
ExecStart=/bin/sh -c 'timeout $CEPH_VOLUME_TIMEOUT /usr/sbin/ceph-volume-systemd  %i' #调用ceph-volume-systemd
TimeoutSec=0

[Install]
WantedBy=multi-user.target
```
查看ceph-volume-systemd会发现是调用的ceph-volume trigger去触发读取lv tag，代码如下
```python
def main(args=None):
...
    command = ['ceph-volume', sub_command, 'trigger', extra_data]
    tries = os.environ.get('CEPH_VOLUME_SYSTEMD_TRIES', 30)
    interval = os.environ.get('CEPH_VOLUME_SYSTEMD_INTERVAL', 5)
    while tries > 0:
        try:
            # don't log any output to the terminal, just rely on stderr/stdout
            # going to logging
            process.run(command, terminal_logging=False)
            logger.info('successfully trggered activation for: %s', extra_data)
            break
        except RuntimeError as error:
            logger.warning(error)
            logger.warning('failed activating OSD, retries left: %s', tries)
            tries -= 1
            time.sleep(interval)
```
trigger类具体的实现如下
```python
class Trigger(object):
    help = 'systemd helper to activate an OSD'
    def __init__(self, argv):
        self.argv = argv
    @decorators.needs_root
    def main(self):
        sub_command_help = dedent("""
        ** DO NOT USE DIRECTLY **
        This tool is meant to help the systemd unit that knows about OSDs.
        Proxy OSD activation to ``ceph-volume lvm activate`` by parsing the
        input from systemd, detecting the UUID and ID associated with an OSD::
            ceph-volume lvm trigger {SYSTEMD-DATA}
        The systemd "data" is expected to be in the format of::
            {OSD ID}-{OSD UUID}
        The lvs associated with the OSD need to have been prepared previously,
        so that all needed tags and metadata exist.
        """)
        parser = argparse.ArgumentParser(
            prog='ceph-volume lvm trigger',
            formatter_class=argparse.RawDescriptionHelpFormatter,
            description=sub_command_help,
        )
        parser.add_argument(
            'systemd_data',
            metavar='SYSTEMD_DATA',
            nargs='?',
            help='Data from a systemd unit containing ID and UUID of the OSD, like asdf-lkjh-0'
        )
        if len(self.argv) == 0:
            print(sub_command_help)
            return
        args = parser.parse_args(self.argv)
        osd_id = parse_osd_id(args.systemd_data)
        osd_uuid = parse_osd_uuid(args.systemd_data)
        Activate(['--auto-detect-objectstore', osd_id, osd_uuid]).main() #实例化Activate 
```
跟Activate实现，最终干活的就是activate_bluestore，具体看代码
```python
def activate_bluestore(lvs, no_systemd=False):
    # find the osd
    osd_lv = lvs.get(lv_tags={'ceph.type': 'block'}) #获取数据类型，类型为Bluestore数据存储
    if not osd_lv:
        raise RuntimeError('could not find a bluestore OSD to activate')
    is_encrypted = osd_lv.tags.get('ceph.encrypted', '0') == '1' #是否加密
    dmcrypt_secret = None
    osd_id = osd_lv.tags['ceph.osd_id'] #获取osd id
    conf.cluster = osd_lv.tags['ceph.cluster_name'] #获取集群名称
    osd_fsid = osd_lv.tags['ceph.osd_fsid'] #获取ceph集群的fsid
    # mount on tmpfs the osd directory 挂载tmpfs目录，依次建立好 block、block.db或者block.wal的软连接
    osd_path = '/var/lib/ceph/osd/%s-%s' % (conf.cluster, osd_id)
    if not system.path_is_mounted(osd_path):
        # mkdir -p and mount as tmpfs
        prepare_utils.create_osd_path(osd_id, tmpfs=True)
    # XXX This needs to be removed once ceph-bluestore-tool can deal with
    # symlinks that exist in the osd dir
    for link_name in ['block', 'block.db', 'block.wal']: 
        link_path = os.path.join(osd_path, link_name)
        if os.path.exists(link_path):
            os.unlink(os.path.join(osd_path, link_name))
    # encryption is handled here, before priming the OSD dir
    if is_encrypted:
        osd_lv_path = '/dev/mapper/%s' % osd_lv.lv_uuid
        lockbox_secret = osd_lv.tags['ceph.cephx_lockbox_secret']
        encryption_utils.write_lockbox_keyring(osd_id, osd_fsid, lockbox_secret)
        dmcrypt_secret = encryption_utils.get_dmcrypt_key(osd_id, osd_fsid)
        encryption_utils.luks_open(dmcrypt_secret, osd_lv.lv_path, osd_lv.lv_uuid)
    else:
        osd_lv_path = osd_lv.lv_path
    db_device_path = get_osd_device_path(osd_lv, lvs, 'db', dmcrypt_secret=dmcrypt_secret)
    wal_device_path = get_osd_device_path(osd_lv, lvs, 'wal', dmcrypt_secret=dmcrypt_secret)
    # Once symlinks are removed, the osd dir can be 'primed again.
    process.run([
        'ceph-bluestore-tool', '--cluster=%s' % conf.cluster,
        'prime-osd-dir', '--dev', osd_lv_path,
        '--path', osd_path])
    # always re-do the symlink regardless if it exists, so that the block,
    # block.wal, and block.db devices that may have changed can be mapped
    # correctly every time
    process.run(['ln', '-snf', osd_lv_path, os.path.join(osd_path, 'block')])
    system.chown(os.path.join(osd_path, 'block'))
    system.chown(osd_path)
    if db_device_path:
        destination = os.path.join(osd_path, 'block.db')
        process.run(['ln', '-snf', db_device_path, destination])
        system.chown(db_device_path)
    if wal_device_path:
        destination = os.path.join(osd_path, 'block.wal')
        process.run(['ln', '-snf', wal_device_path, destination])
        system.chown(wal_device_path)
    if no_systemd is False:
        # enable the ceph-volume unit for this OSD 目录挂载完毕以后，通过systemd启动对应OSD服务
        systemctl.enable_volume(osd_id, osd_fsid, 'lvm')
        # start the OSD
        systemctl.start_osd(osd_id)
    terminal.success("ceph-volume lvm activate successful for osd ID: %s" % osd_id)
```
流程图
![[Pasted image 20250514161429.png]]
# Tag手动维护
新建lv tag
```
[root@demo cephuser]# lvchange --addtag ceph.cephx_lockbox_secret= /dev/mapper/ceph--508f2cbf--23e3--4b62--a6e1--7143b00d08dd-osd--block--903b1698--9271--4040--8c8f--cd09417a7aaa
  Logical volume ceph-508f2cbf-23e3-4b62-a6e1-7143b00d08dd/osd-block-903b1698-9271-4040-8c8f-cd09417a7aaa changed.
```
删除lv tag
```
[root@demo cephuser]# lvchange --deltag ceph.block_device /dev/mapper/ceph--508f2cbf--23e3--4b62--a6e1--7143b00d08dd-osd--block--903b1698--9271--4040--8c8f--cd09417a7aaa
  Logical volume ceph-508f2cbf-23e3-4b62-a6e1-7143b00d08dd/osd-block-903b1698-9271-4040-8c8f-cd09417a7aaa changed.
```
查看lvtag
```
[root@demo cephuser]# /usr/sbin/lvs --noheadings --readonly -o lv_tags
```