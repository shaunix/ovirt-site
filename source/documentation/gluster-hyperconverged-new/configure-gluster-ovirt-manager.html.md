---
title: Configure Red Hat Gluster Storage as a Red Hat Virtualization storage domain
---

# Configure Red Hat Gluster Storage as a Red Hat Virtualization storage domain

## Create the logical network for gluster traffic

1. **Log in to the engine**

   Browse to the engine user interface (for example, **http://engine.example.com/ovirt-engine**) and log in using the administrative credentials you configured in [Deploy the Hosted Engine using the Cockpit UI](deploy-he-cockpit).

2. **Create a logical network for gluster traffic**

   1. Click **Network** -> **Networks** and then click **New**. The **New Logical Network** wizard appears.

   2. On the **General** tab of the wizard, provide a **Name** for the new logical network, and uncheck the **VM Network** checkbox.

   3. On the **Cluster** tab of the wizard, uncheck the **Required** checkbox.

   4. Click **OK** to create the new logical network.

3. **Enable the new logical network for gluster**

   1. Click the **Network** -> **Networks** and select the new logical network.

   2. Click the **Clusters** subtab and then click **Manage Network**. The **Manage Network** dialogue appears.

   3. In the **Manage Network** dialogue, check the **Migration Network** and **Gluster Network** checkboxes.

   4. Click **OK** to save.

4. **Attach the gluster network to the host**

   1. Click **Compute** -> **Hosts** and select the host.

   2. Click the **Network Interfaces** subtab and then click **Setup Host Networks**. The **Setup Host Networks** window opens.

   3. Drag and drop the newly created network to the correct interface.

   4. Ensure that the **Verify connectivity between Host and Engine** checkbox is checked.

   5. Ensure that the **Save network configuration** checkbox is checked.

   6. Click **OK** to save.

5. **Verify the health of the network**

   Check the state of the host's network. If the network interface enters an "Out of sync" state or does not have an IPv4 Address, click **Management** -> **Refresh Capabilities**.


## Add additional virtualization hosts to the hosted engine

If you did not specify additional virtualization hosts as part of [Configure Red Hat Gluster Storage for Hosted Engine using the Cockpit UI](configure-gluster-cockpit), follow these steps in Red Hat Virtualization Manager for each of the other virtualization hosts.

1. **Add virtualization hosts to the host inventory**

   1. Click **Compute** -> **Hosts** and then click **New** to open the _New Host_ window.

   2. Provide the **Name**, **Hostname**, and **Password** for the host that you want to manage.

   3. Under **Advanced Parameters**, uncheck the **Automatically configure host firewall** checkbox, as firewall rules are already configured by gdeploy.

   4. In the **Hosted Engine** tab of the _New Host_ dialog, set the value of **Choose hosted engine deployment action** to **Deploy**. This ensures that the hosted engine can run on the new host.

   5. Click **OK**.

2. **Attach the gluster network to the new host**

   1. Click the name of the newly added host to go to the host page.

   2. Click the **Network Interfaces** subtab and then click **Setup Host Networks**.

   3. Drag and drop the newly created network to the correct interface.

   4. Ensure that the **Verify connectivity** checkbox is checked.

   5. Ensure that the **Save network configuration** checkbox is checked.

   6. Click **OK** to save.

3. In the **General** subtab for this host, verify that the value of **Hosted Engine HA** is _Active_, with a positive integer as a score.

   **IMPORTANT:** If **Score** is listed as **N/A**, you may have forgotten to select the **deploy** action for **Choose hosted engine deployment action**. Follow the steps in [Reinstalling a virtualization host](https://access.redhat.com/documentation/en-us/red_hat_hyperconverged_infrastructure_for_virtualization/1.5/html-single/maintaining_red_hat_hyperconverged_infrastructure_for_virtualization/#task-reinstall-host) in _Maintaining Red Hat Hyperconverged Infrastructure for Virtualization_ to reinstall the host with the **deploy** action.

4. **Verify the health of the network**

   Check the state of the host's network. If the network interface enters an "Out of sync" state or does not have an IPv4 Address, click **Management** -> **Refresh Capabilities**.

See the Red Hat Virtualization 4.2 _Self-Hosted Engine Guide_ for further details: [https://access.redhat.com/documentation/en-us/red_hat_virtualization/4.2/html/self-hosted_engine_guide/chap-installing_additional_hosts_to_a_self-hosted_environment](https://access.redhat.com/documentation/en-us/red_hat_virtualization/4.2/html/self-hosted_engine_guide/chap-installing_additional_hosts_to_a_self-hosted_environment)
