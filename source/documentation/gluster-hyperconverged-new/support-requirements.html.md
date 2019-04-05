---
title: Requirements
---

# Support requirements

Review this section to ensure that your planned deployment meets the requirements for support by Red Hat.

## Operating system

Red Hat Hyperconverged Infrastructure for Virtualization (RHHI for Virtualization) uses Red Hat Virtualization Host 4.2 as a base for all other configuration. The following table shows the the supported versions of each product to use for a supported RHHI for Virtualization deployment.

**Version compatibility**

| RHHI version | RHGS version | RHV version
|-
| 1.0   | 3.2   | 4.1.0 to 4.1.7 |
| 1.1   | 3.3.1 | 4.1.8 to 4.2.0 |
| 1.5   | 3.4 Batch 1 Update | 4.2.7 to current |
| 1.5.1 | 3.4 Batch 2 Update | 4.2.8 to current |

See [Requirements](https://access.redhat.com/documentation/en-us/red_hat_virtualization/4.2/html/planning_and_prerequisites_guide/rhv_requirements) in the Red Hat Virtualization *Planning and Prerequisites Guide* for details on requirements of Red Hat Virtualization.


## Physical machines

Red Hat Hyperconverged Infrastructure for Virtualization (RHHI for Virtualization) requires **at least 3 physical machines**. Scaling to 6, 9, or 12 physical machines is also supported; see [Scaling](#scaling) for more detailed requirements.

Each physical machine must have the following capabilities:

* at least 2 NICs (Network Interface Controllers) per physical machine, for separation of data and management traffic (see [Networking](#networking) for details)
* for small deployments:
  * at least 12 cores
  * at least 64GB RAM
  * at most 48TB storage
* for medium deployments:
  * at least 12 cores
  * at least 128GB RAM
  * at most 64TB storage
* for large deployments:
  * at least 16 cores
  * at least 256GB RAM
  * at most 80TB storage


## Hosted Engine virtual machine

The Hosted Engine virtual machine requires at least the following:

* 1 dual core CPU (1 quad core or multiple dual core CPUs recommended)
* 4GB RAM that is not shared with other processes (16GB recommended)
* 25GB of local, writable disk space (50GB recommended)
* 1 NIC with at least 1Gbps bandwidth

For more information, see [Requirements](https://access.redhat.com/documentation/en-us/red_hat_virtualization/4.2/html-single/planning_and_prerequisites_guide/#RHV_requirements) in the Red Hat Virtualization 4.2 *Planning and Prerequisites Guide*.


## Networking

**Fully-qualified domain names that are forward and reverse resolvable by DNS are required** for all hyperconverged hosts and for the Hosted Engine virtual machine that provides Red Hat Virtualization Manager.

Client storage traffic and management traffic in the cluster must use separate networks: a **front-end management network* and a *back-end storage network**.

**Each node requires two Ethernet ports, one for each network.** This ensures optimal performance. For high availability, place each network on a separate network switch. For improved fault tolerance, provide a separate power supply for each switch.

**Front-end management network**
: * Used by Red Hat Virtualization and virtual machines.
  * Requires at least one 1Gbps Ethernet connection.
  * IP addresses assigned to this network must be on the same subnet as each other, and on a different subnet to the back-end storage network.
  * IP addresses on this network can be selected by the administrator.

**Back-end storage network**
: * Used by storage and migration traffic between hyperconverged nodes.
  * Requires at least one 10Gbps Ethernet connection.
  * Requires maximum latency of 5 milliseconds between peers.

Network fencing devices that use Intelligent Platform Management Interfaces (IPMI) require a separate network.

If you want to use DHCP network configuration for the Hosted Engine virtual machine, then you must have a DHCP server configured prior to configuring Red Hat Hyperconverged Infrastructure for Virtualization.

If you want to use geo-replication to store copies of data for disaster recovery purposes, a reliable time source is required.

**Before you begin the deployment process**, determine the following details:

* IP address for a gateway to the virtualization host. This address must respond to ping requests.
* IP address of the front-end management network.
* Fully-qualified domain name (FQDN) for the Hosted Engine virtual machine.
* MAC address that resolves to the static FQDN and IP address of the Hosted Engine.


## Storage

A hyperconverged host stores configuration, logs and kernel dumps, and uses its storage as swap space. This section lists the minimum directory sizes for hyperconverged hosts. Red Hat recommends using the default allocations, which use more storage space than these minimums.

* `/` (root) - 6GB
* `/home` - 1GB
* `/tmp` - 1GB
* `/boot` - 1GB
* `/var` - 22GB
* `/var/log` - 15GB
* `/var/log/audit` - 2GB
* `swap` - 1GB (for the recommended swap size, see https://access.redhat.com/solutions/15244)
* Anaconda reserves 20% of the thin pool size within the volume group for future metadata expansion. This is to prevent an out-of-the-box configuration from running out of space under normal usage conditions. Overprovisioning of thin pools during installation is also not supported.
* **Minimum Total - 52GB**


### Disks

Red Hat recommends Solid State Disks (SSDs) for best performance. If you use Hard Drive Disks (HDDs), you should also configure a smaller, faster SSD as an LVM cache volume.

4K native devices are not supported with Red Hat Hyperconverged Infrastructure for Virtualization, as Red Hat Virtualization requires 512 byte emulation (512e) support.


### RAID
RAID5 and RAID6 configurations are supported. However, RAID configuration limits depend on the technology in use.

* SAS/SATA 7k disks are supported with RAID6 (at most 10+2)
* SAS 10k and 15k disks are supported with the following:
  * RAID5 (at most 7+1)
  * RAID6 (at most 10+2)

RAID cards must use flash backed write cache.

Red Hat further recommends providing at least one hot spare drive local to each server.


### JBOD

As of Red Hat Hyperconverged Infrastructure for Virtualization 1.5, JBOD configurations are fully supported and no longer require architecture review.


### Logical volumes

The logical volumes that comprise the **engine** gluster volume must be thick provisioned. This protects the Hosted Engine from out of space conditions, disruptive volume configuration changes, I/O overhead, and migration activity.

When VDO is not in use, the logical volumes that comprise the **vmstore** and optional **data** gluster volumes must be thin provisioned. This allows greater flexibility in underlying volume configuration. If your thin provisioned volumes are on Hard Drive Disks (HDDs), configure a smaller, faster Solid State Disk (SSD) as an lvmcache for improved performance.

Thin provisioning is not required for the **vmstore** and **data** volumes if VDO is being used on these volumes.


### Red Hat Gluster Storage volumes

Red Hat Hyperconverged Infrastructure for Virtualization is expected to have 3–4 Red Hat Gluster Storage volumes.

* 1 **engine** volume for the Hosted Engine
* 1 **vmstore** volume for virtual machine boot disk images
* 1 optional **data** volume for other virtual machine disk images
* 1 **shared_storage** volume for geo-replication metadata

A Red Hat Hyperconverged Infrastructure for Virtualization deployment can contain at most 1 geo-replicated volume.


### Volume types

Red Hat Hyperconverged Infrastructure for Virtualization (RHHI for Virtualization) supports only the following volume types:

* [Replicated volumes](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.4/html/administration_guide/sect-creating_replicated_volumes) (3 copies of the same data on 3 bricks, across 3 nodes).
* [Arbitrated replicated volumes](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.4/html/administration_guide/creating_arbitrated_replicated_volumes) (2 full copies of the same data on 2 bricks and 1 arbiter brick that contains metadata. across three nodes).
* [Distributed volumes](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.4/html/administration_guide/creating_distributed_volumes) (1 copy of the data, no replication to other bricks).

All replicated and arbitrated-replicated volumes must span exactly three nodes.

Note that arbiter bricks store only file names, structure, and metadata. This means that a three-way arbitrated replicated volume requires about 75% of the storage space that a three-way replicated volume would require to achieve the same level of consistency. However, because the arbiter brick stores only metadata, a three-way arbitrated replicated volume only provides the availability of a two-way replicated volume.

For more information on laying out arbitrated replicated volumes, see [Creating multiple arbitrated replicated volumes across fewer total nodes](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.4/html/administration_guide/creating_arbitrated_replicated_volumes#sect-Create_Multiple_Arbitrated_Volumes) in the Red Hat Gluster Storage *Administration Guide*.


## Virtual Data Optimizer (VDO)

A Virtual Data Optimizer (VDO) layer is supported as of Red Hat Hyperconverged Infrastructure for Virtualization 1.5.

The following limitations apply to this support:

* VDO is supported only on new deployments.
* VDO is compatible only with thick provisioned volumes. VDO and thin provisioning are not supported on the same device.


## Scaling

Initial deployments of Red Hat Hyperconverged Infrastructure for Virtualization are either 1 node or 3 nodes.

1 node deployments cannot be scaled.

3 node deployments can be scaled to 6, 9, or 12 nodes using one of the following methods:

1. Add new hyperconverged nodes to the cluster, in sets of three, up to the maximum of 12 hyperconverged nodes.
2. Create new Gluster volumes using new disks on existing hyperconverged nodes.

You cannot create a volume that spans more than 3 nodes, or expand an existing volume so that it spans across more than 3 nodes at a time.


## Existing Red Hat Gluster Storage configurations

Red Hat Hyperconverged Infrastructure for Virtualization is supported only when deployed as specified in this document. Existing Red Hat Gluster Storage configurations cannot be used in a hyperconverged configuration. If you want to use an existing Red Hat Gluster Storage configuration, refer to the traditional configuration documented in [Configuring Red Hat Virtualization with Red Hat Gluster Storage](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.4/html/configuring_red_hat_virtualization_with_red_hat_gluster_storage/).


## Disaster recovery

Red Hat strongly recommends configuring a disaster recovery solution. For details on configuring geo-replication as a disaster recovery solution, see *Maintaining Red Hat Hyperconverged Infrastructure for Virtualization*: [](https://access.redhat.com/documentation/en-us/red_hat_hyperconverged_infrastructure_for_virtualization/1.5/html/maintaining_red_hat_hyperconverged_infrastructure_for_virtualization/config-backup-recovery).


### Prerequisites for geo-replication

Be aware of the following requirements and limitations when configuring geo-replication:

**One geo-replicated volume only**
: Red Hat Hyperconverged Infrastructure for Virtualization (RHHI for Virtualization) supports only one geo-replicated volume. Red Hat recommends backing up the volume that stores the data of your virtual machines, as this is usually contains the most valuable data.
**Two different managers required**
: The source and destination volumes for geo-replication must be managed by different instances of Red Hat Virtualization Manager.


### Prerequisites for failover and failback configuration

**Versions must match between environments**
: Ensure that the primary and secondary environments have the same version of Red Hat Virtualization Manager, with identical data center compatibility versions, cluster compatibility versions, and PostgreSQL versions.

**No virtual machine disks in the hosted engine storage domain**
: The storage domain used by the hosted engine virtual machine is not failed over, so any virtual machine disks in this storage domain will be lost.

**Execute Ansible playbooks manually from a separate master node**
: Generate and execute Ansible playbooks manually from a separate machine that acts as an Ansible master node.


## Additional requirements for single node deployments

Red Hat Hyperconverged Infrastructure for Virtualization is supported for deployment on a single node provided that all [Support Requirements](https://access.redhat.com/documentation/en-us/red_hat_hyperconverged_infrastructure_for_virtualization/1.5/html-single/deploying_red_hat_hyperconverged_infrastructure_for_virtualization/#rhhi-requirements) are met, with the following additions and exceptions.

A single node deployment requires a physical machine with:

* 1 Network Interface Controller
* at least 12 cores
* at least 64GB RAM
* at most 48TB storage

Single node deployments cannot be scaled, and are not highly available.
