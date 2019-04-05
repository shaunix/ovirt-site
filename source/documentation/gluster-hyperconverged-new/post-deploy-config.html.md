---
title: Post-deployment configuration suggestions
---

# Post-deployment configuration suggestions

Depending on your requirements, you may want to perform some additional configuration on your newly deployed Red Hat Hyperconverged Infrastructure for Virtualization. This section contains suggested next steps for additional configuration.

Details on these processes are available in [*Maintaining Red Hat Hyperconverged Infrastructure for Virtualization*](https://access.redhat.com/documentation/en-us/red_hat_hyperconverged_infrastructure_for_virtualization/1.5/html-single/maintaining_red_hat_hyperconverged_infrastructure_for_virtualization/).


## Configure a logical volume cache for improved performance

If your main storage devices are not Solid State Disks (SSDs), Red Hat recommends configuring a logical volume cache (lvmcache) to achieve the required performance for Red Hat Hyperconverged Infrastructure for Virtualization deployments.

See [Configuring a logical volume cache for improved performance](https://access.redhat.com/documentation/en-us/red_hat_hyperconverged_infrastructure_for_virtualization/1.5/html-single/maintaining_red_hat_hyperconverged_infrastructure_for_virtualization/#config-lvmcache) for details.


## Configure fencing for high availability

Fencing allows a cluster to enforce performance and availability policies and react to unexpected host failures by automatically rebooting virtualization hosts.

See [Configure High Availability using fencing policies](https://access.redhat.com/documentation/en-us/red_hat_hyperconverged_infrastructure_for_virtualization/1.5/html-single/maintaining_red_hat_hyperconverged_infrastructure_for_virtualization/#config-fencing-policy) for further information.


## Configure backup and recovery options

Red Hat recommends configuring at least basic disaster recovery capabilities on all production deployments.

See [Configuring backup and recovery options](https://access.redhat.com/documentation/en-us/red_hat_hyperconverged_infrastructure_for_virtualization/1.5/html/maintaining_red_hat_hyperconverged_infrastructure_for_virtualization/config-backup-recovery) in *Maintaining Red Hat Hyperconverged Infrastructure for Virtualization* for more information.
