---
title: Example gdeploy configuration file for configuring compression and deduplication
---

# Example gdeploy configuration file for configuring compression and deduplication

Virtual Data Optimizer (VDO) volumes are supported as of Red Hat Hyperconverged Infrastructure for Virtualization 1.5 when enabled at deployment time. VDO cannot be enabled on existing deployments.

Deploying Red Hat Hyperconverged Infrastructure for Virtualization 1.5 with a Virtual Data Optimizer volume reduces the actual disk space required for a workload, as it enables data compression and deduplication capabilities. This reduces capital and operating expenses.

The `gdeployConfig.conf` file is located at `/var/lib/ovirt-hosted-engine-setup/gdeploy/gdeployConfig.conf`. This configuration file is applied when *Enable Dedupe & Compression* is checked during deployment.

```
# VDO Configuration
[vdo1:@HOSTNAME@]
action=create
devices=sdb,sdd
names=vdo_sdb,vdo_sdd
logicalsize=164840G,2000G

# Logical size(G) is ten times of actual brick size. If logicalsize >= 1000G, then slabsize=32G.

blockmapcachesize=128M
readcache=enabled
readcachesize=20M
emulate512=on
writepolicy=sync
ignore_vdo_errors=no
slabsize=32G,32G


# Create physical volume on all hosts
[pv1]
action=create
devices=/dev/mapper/vdo_sdb
ignore_pv_errors=no


# Create volume group on all hosts
[vg1]
action=create
vgname=gluster_vg_sdb
pvname=/dev/mapper/vdo_sdb
ignore_vg_errors=no
```
