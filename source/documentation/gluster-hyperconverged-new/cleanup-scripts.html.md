---
title: Example cleanup configuration files for gdeploy
---

# Example cleanup configuration files for gdeploy

In the event that deployment fails, it is necessary to clean up the previous deployment attempts before retrying the deployment. The following two example files can be run with gdeploy to clean up previously failed deployment attempts so that deployment can be reattempted.

**volume_cleanup.conf**
```
[hosts]
<Gluster_Network_NodeA>
<Gluster_Network_NodeB>
<Gluster_Network_NodeC>

[volume1]
action=delete
volname=engine

[volume2]
action=delete
volname=vmstore

[volume3]
action=delete
volname=data

[peer]
action=detach
```

**lv_cleanup.conf**
```
[hosts]
<Gluster_Network_NodeA>
<Gluster_Network_NodeB>
<Gluster_Network_NodeC>

[backend-reset]
pvs=sdb,sdc
unmount=yes
```

