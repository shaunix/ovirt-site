---
title: Verify your deployment
---

# Verify your deployment

After deployment is complete, verify that your deployment has completed successfully.

1. Browse to the engine user interface, for example, `http://engine.example.com/ovirt-engine`.

   **Administration Console Login**

   ![Login page for the Administration Console](images/admin-console-login.png)

2. Log in using the administrative credentials added during hosted engine deployment.

   When login is successful, the Dashboard appears.

   **Administration Console Dashboard**

   ![Administration Console Dashboard](images/admin-console-dashboard.png)

3. Verify that your cluster is available.

   **Administration Console Dashboard - Clusters**

   ![The cluster widget with one cluster showing](images/admin-console-cluster-widget.png)

4. Verify that at least one host is available.

   If you provided additional host details during Hosted Engine deployment, 3 hosts are visible here, as shown.

   **Administration Console Dashboard - Hosts**

   ![The hosts widget with 3 hosts showing](images/admin-console-host-widget.png)

   1. Click **Compute** -> **Hosts**.

   2. Verify that all hosts are listed with a **Status** of `Up`.

      **Administration Console - Hosts**

      ![Administration Console host dashboard](images/admin-console-host-dashboard.png)

5. Verify that all storage domains are available.

   1. Click **Storage** -> **Domains**.

   2. Verify that the `Active` icon is shown in the first column.

      **Administration Console - Storage Domains**

      ![Administration Console storage domain dashboard](images/admin-console-storage-domain-dashboard.png)
