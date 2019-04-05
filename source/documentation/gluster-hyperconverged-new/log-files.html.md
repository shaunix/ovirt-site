---
title: Log file locations
---

# Log file locations

During the deployment process, progress information is displayed in the web browser. This information is also stored on the local file system so that the information logged can be archived or reviewed at a later date, for example, if the web browser stops responding or is closed before the information has been reviewed.

The log file for the Cockpit based deployment process (documented in [Configure Red Hat Gluster Storage for Hosted Engine using the Cockpit UI](configure-gluster-cockpit)) is stored in the `~/.gdeploy/logs/gdeploy.log` file, where `~` is the home directory of the administrative user logged in to the Cockpit UI. If you log in to the Cockpit UI as root, the log file is stored as `/root/.gdeploy/logs/gdeploy.log`.

The log files for the Hosted Engine setup portion of the deployment process (documented in [Deploy the Hosted Engine using the Cockpit UI](deploy-he-cockpit)) are stored in the `/var/log/ovirt-hosted-engine-setup` directory, with file names of the form `ovirt-hosted-engine-setup-<date>.log`.
