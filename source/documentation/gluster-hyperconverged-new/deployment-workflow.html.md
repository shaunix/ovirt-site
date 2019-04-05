---
title: Deployment workflow
---

# Deployment workflow

The workflow for deploying Red Hat Hyperconverged Infrastructure for Virtualization (RHHI for Virtualization) is as follows:

1. Verify that your planned deployment meets support requirements: [Support requirements](support-requirements).

2. Install the physical machines that will act as virtualization hosts: [Install Host Physical Machines](install-host-physical-machines).

3. Configure key-based SSH authentication without a password to enable automated configuration of the hosts: [Configure Public Key based SSH Authentication without a password](configure-key-based-ssh-auth).

4. Configure Red Hat Gluster Storage on the physical hosts using the Cockpit UI: [Configure Red Hat Gluster Storage for Hosted Engine using the Cockpit UI](configure-gluster-cockpit).

5. Deploy the Hosted Engine using the Cockpit UI: [Deploy the Hosted Engine using the Cockpit UI](deploy-he-cockpit).

6. Configure the Red Hat Gluster Storage nodes using the Red Hat Virtualization management UI: [Log in to Red Hat Virtualization Manager to complete configuration](https://access.redhat.com/documentation/en-us/red_hat_hyperconverged_infrastructure_for_virtualization/1.5/html-single/deploying_red_hat_hyperconverged_infrastructure_for_virtualization/#configure-gluster-ovirt-manager).
