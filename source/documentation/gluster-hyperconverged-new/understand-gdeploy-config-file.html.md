---
title: Understanding the generated gdeploy configuration file
---

# Understanding the generated gdeploy configuration file

Gdeploy automatically provisions one or more machines with Red Hat Gluster Storage based on a configuration file.

The Cockpit UI provides provides a wizard that allows users to generate a gdeploy configuration file that is suitable for performing the base-level deployment of Red Hat Hyperconverged Infrastructure for Virtualization.

This section explains the gdeploy configuration file that would be generated if the following configuration details were specified in the Cockpit UI:

* 3 hosts with IP addresses 192.168.0.101, 192.168.0.102, and 192.168.0.103
* Arbiter configuration for non-engine volumes.
* Three-way replication for the engine volume.
* 12 bricks that are configured with RAID 6 with a stripe size of 256 KB.

This results in a gdeploy configuration file with the following sections.

For further details on any of the sections defined here, see the Red Hat Gluster Storage *Administration Guide*: [https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.4/html/administration_guide/chap-red_hat_storage_volumes#chap-Red_Hat_Storage_Volumes-gdeploy_configfile](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.4/html/administration_guide/chap-red_hat_storage_volumes#chap-Red_Hat_Storage_Volumes-gdeploy_configfile).

**\[hosts] section**
```
[hosts]
192.168.0.101
192.168.0.102
192.168.0.103
```

The `[hosts]` section defines the IP addresses of the three physical machines to be configured according to this configuration file.

**\[script1] section**
```
[script1]
action=execute
ignore_script_errors=no
file=/usr/share/ansible/gdeploy/scripts/grafton-sanity-check.sh -d sdb -h 192.168.0.101,192.168.0.102,192.168.0.103
```

The `[script1]` section specifies a script to run to verify that all hosts are configured correctly in order to allow gdeploy to run without error.

**Underlying storage configuration**
```
[disktype]
raid6

[diskcount]
12

[stripesize]
256
```

The `[disktype]` section specifies the hardware configuration of the underlying storage for all hosts.

The `[diskcount]` section specifies the number of disks in RAID storage. This can be omitted for JBOD configurations.

The `[stripesize]` section specifies the RAID storage stripe size in kilobytes. This can be omitted for JBOD configurations.

**Enable and restart chronyd**
```
[service1]
action=enable
service=chronyd

[service2]
action=restart
service=chronyd
```

These service sections enable and restart the network time service, chronyd, on all servers.

**Create physical volume on all hosts**
```
[pv1]
action=create
devices=sdb
ignore_pv_errors=no
```

The `[pv1]` section creates a physical volume on the `sdb` device of all hosts.

NOTE: If you enable dedeuplication and compression during deployment time, devices in `[pv1]` and pvname in `[vg1]` will be `/dev/mapper/vdo_sdb`. For more information on VDO configuration, see [Example gdeploy configuration file for configuring compression and deduplication](configure-vdo-gdeploy).

**Create volume group on all hosts**
```
[vg1]
action=create
vgname=gluster_vg_sdb
pvname=sdb
ignore_vg_errors=no
```

The `[vg1]` section creates a volume group in the previously created physical volume on all hosts.

**Create the logical volume thin pool**
```
[lv1:{192.168.0.101,192.168.0.102}]
action=create
poolname=gluster_thinpool_sdb
ignore_lv_errors=no
vgname=gluster_vg_sdb
lvtype=thinpool
poolmetadatasize=16GB
size=1000GB

[lv2:192.168.0.103]
action=create
poolname=gluster_thinpool_sdb
ignore_lv_errors=no
vgname=gluster_vg_sdb
lvtype=thinpool
poolmetadatasize=16GB
size=20GB
```

The `[lv1:*]` section creates a 1000 GB thin pool on the first two hosts with a meta data pool size of 16 GB.

The `[lv2:*]` section creates a 20 GB thin pool on the third host with a meta data pool size of 16 GB. This is the logical volume used for the arbiter brick.

The `chunksize` variable is also available, but should be used with caution. `chunksize` defines the size of the chunks used for snapshots, cache pools, and thin pools. By default this is specified in kilobytes. For RAID 5 and 6 volumes, gdeploy calculates the default chunksize by multiplying the stripe size and the disk count.

WARNING: Red Hat recommends using at least the default chunksize. If the chunksize is too small and your volume runs out of space for metadata, the volume is unable to create data. Red Hat recommends monitoring your logical volumes to ensure that they are expanded or more storage created before metadata volumes become completely full.

**Create underlying engine storage**
```
[lv3:{192.168.0.101,192.168.0.102}]
action=create
lvname=gluster_lv_engine
ignore_lv_errors=no
vgname=gluster_vg_sdb
mount=/gluster_bricks/engine
size=100GB
lvtype=thick

[lv4:192.168.0.103]
action=create
lvname=gluster_lv_engine
ignore_lv_errors=no
vgname=gluster_vg_sdb
mount=/gluster_bricks/engine
size=10GB
lvtype=thick
```

The `[lv3:*]` section creates a 100 GB thick provisioned logical volume called *gluster_lv_engine* on the first two hosts. This volume is configured to mount on `/gluster_bricks/engine`.

The `[lv4:*]` section creates a 10 GB thick provisioned logical volume for the engine on the third host. This volume is configured to mount on `/gluster_bricks/engine`.

**Create underlying data and virtual machine boot disk storage**
```
[lv5:{192.168.0.101,192.168.0.102}]
action=create
lvname=gluster_lv_data
ignore_lv_errors=no
vgname=gluster_vg_sdb
mount=/gluster_bricks/data
lvtype=thinlv
poolname=gluster_thinpool_sdb
virtualsize=500GB

[lv6:192.168.0.103]
action=create
lvname=gluster_lv_data
ignore_lv_errors=no
vgname=gluster_vg_sdb
mount=/gluster_bricks/data
lvtype=thinlv
poolname=gluster_thinpool_sdb
virtualsize=10GB

[lv7:{192.168.0.101,192.168.0.102}]
action=create
lvname=gluster_lv_vmstore
ignore_lv_errors=no
vgname=gluster_vg_sdb
mount=/gluster_bricks/vmstore
lvtype=thinlv
poolname=gluster_thinpool_sdb
virtualsize=500GB

[lv8:192.168.0.103]
action=create
lvname=gluster_lv_vmstore
ignore_lv_errors=no
vgname=gluster_vg_sdb
mount=/gluster_bricks/vmstore
lvtype=thinlv
poolname=gluster_thinpool_sdb
virtualsize=10GB
```

The `[lv5:*]` and `[lv7:*]` sections create 500 GB logical volumes as bricks for the data and vmstore volumes on the first two hosts.

The `[lv6:*]` and `[lv8:*]` sections create 10 GB logical volumes as arbiter bricks for the data and vmstore volumes on the third host.

The data bricks are configured to mount on `/gluster_bricks/data`, and the vmstore bricks are configured to mount on `/gluster_bricks/vmstore`.

**Configure SELinux file system labels**
```
[selinux]
yes
```

The `[selinux]` section specifies that the storage created should be configured with appropriate SELinux file system labels for Gluster storage.

**Start glusterd**
```
[service3]
action=start
service=glusterd
slice_setup=yes
```

The `[service3]` section starts the glusterd service and configures a control group to ensure glusterd cannot consume all system resources; see the Red Hat Enterprise Linux _Resource Management Guide_ for details: [https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Resource_Management_Guide/index.html](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Resource_Management_Guide/index.html).

**Configure the firewall**
```
[firewalld]
action=add
ports=111/tcp,2049/tcp,54321/tcp,5900/tcp,5900-6923/tcp,5666/tcp,16514/tcp,54322/tcp
services=glusterfs
```

The `[firewalld]` section opens the ports required to allow gluster traffic.

**Disable gluster hooks**
```
[script2]
action=execute
file=/usr/share/ansible/gdeploy/scripts/disable-gluster-hooks.sh
```

The `[script2]` section disables gluster hooks that can interfere with the Hyperconverged Infrastructure.

**Create gluster volumes**
```
[volume1]
action=create
volname=engine
transport=tcp
replica=yes
replica_count=3
key=group,storage.owner-uid,storage.owner-gid,network.ping-timeout,performance.strict-o-direct,network.remote-dio,cluster.granular-entry-heal,features.shard-block-size
value=virt,36,36,30,on,off,enable,64MB
brick_dirs=192.168.0.101:/gluster_bricks/engine/engine,192.168.0.102:/gluster_bricks/engine/engine,192.168.0.103:/gluster_bricks/engine/engine
ignore_volume_errors=no

[volume2]
action=create
volname=data
transport=tcp
replica=yes
replica_count=3
key=group,storage.owner-uid,storage.owner-gid,network.ping-timeout,performance.strict-o-direct,network.remote-dio,cluster.granular-entry-heal,features.shard-block-size
value=virt,36,36,30,on,off,enable,64MB
brick_dirs=192.168.0.101:/gluster_bricks/data/data,192.168.0.102:/gluster_bricks/data/data,192.168.0.103:/gluster_bricks/data/data
ignore_volume_errors=no
arbiter_count=1

[volume3]
action=create
volname=vmstore
transport=tcp
replica=yes
replica_count=3
key=group,storage.owner-uid,storage.owner-gid,network.ping-timeout,performance.strict-o-direct,network.remote-dio,cluster.granular-entry-heal,features.shard-block-size
value=virt,36,36,30,on,off,enable,64MB
brick_dirs=192.168.0.101:/gluster_bricks/vmstore/vmstore,192.168.0.102:/gluster_bricks/vmstore/vmstore,192.168.0.103:/gluster_bricks/vmstore/vmstore
ignore_volume_errors=no
arbiter_count=1
```

The `[volume*]` sections configure Red Hat Gluster Storage volumes. The `[volume1]` section configures one three-way replicated volume, *engine*. The additional `[volume*]` sections configure two arbitrated replicated volumes: **data** and **vmstore**, which have one arbiter brick on the third host.

The `key` and `value` parameters are used to set the following options:

* `group=virt`
* `storage.owner-uid=36`
* `storage.owner-gid=36`
* `network.ping-timeout=30`
* `performance.strict-o-direct=on`
* `network.remote-dio=off`
* `cluster.granular-entry-heal=enable`
* `features.shard-block-size=64MB`
