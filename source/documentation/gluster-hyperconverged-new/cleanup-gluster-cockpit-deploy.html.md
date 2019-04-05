---
title: Cleaning up automated Red Hat Gluster Storage deployment errors
---

# Cleaning up automated Red Hat Gluster Storage deployment errors

If the deployment process fails after the physical volumes and volume groups are created, you need to undo that work to start the deployment from scratch. Follow this process to clean up a failed deployment so that you can try again.

**Procedure**

1. Create a volume_cleanup.conf file based on the volume_cleanup.conf file in [Example cleanup configuration files for gdeploy](cleanup-scripts).

2. Run gdeploy using the volume_cleanup.conf file.

   ```
   # gdeploy -c volume_cleanup.conf
   ```

3. Create a lv_cleanup.conf file based on the lv_cleanup.conf file in [Example cleanup configuration files for gdeploy](cleanup-scripts).

4. Run gdeploy using the lv_cleanup.conf file.

   ```
   # gdeploy -c lv_cleanup.conf
   ```

5. Check mount configurations on all hosts

   Check the `/etc/fstab` file on all hosts, and remove any lines that correspond to XFS mounts of automatically created bricks.
