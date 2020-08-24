.. _files3:

----------
NFS export
----------

**Prerequisites:** Completion of :ref:`vmmanage`

**Expected Module Duration:** 45 minutes

**Covered Test IDs:** N/A

**FEEDBACK** :ref:`feedback`

Creating an NFS export
++++++++++++++++++++++

During this exercise, you will create an NFS export.

#. In the Prism web console, go to the *File Server Dashboard* page by clicking **File Server** from the dropdown.

#. Click **+ Share/Export** action link.

#. Fill out the following fields:

   - **Name** - logs

   - **Description (Optional)** - File share for system logs

   - **File Server** - Files

   - **Select Protocol** - NFS

   .. figure:: images/24b.png

#. Click **Next**.

#. Fill out the following fields:

   - **Default Access (For All Clients)** - No Access

   - Select **+ Add exceptions**.

   - **Clients with Read-Write Access** - *The first 3 octets of your cluster network*\ .* (e.g. 10.38.1.\*)

      .. figure:: images/25b.png

   By default an NFS export will allow read/write access to any host that mounts the export, but this can be restricted to specific IPs or IP ranges.

#. Click **Next**.

#. Review the **Summary** and click **Create**.

Testing the NFS export
++++++++++++++++++++++

In the following exercise you will:

   - Utilize a Linux VM as a client for your Files NFS export.
   - Create random data within the NFS export.
   - Monitor performance and usage of the NFS export.

#. Note the IP address of the previously created CentOS VM within Prism, and connect via SSH using the following credentials:

   - **Username** - root
   - **Password** - nutanix/4u

#. Execute the following:

   Overwrite the contents of the existing resolv.conf with the IP of your AutoAD VM to handle DNS queries. Example: sudo sh -c "echo nameserver 10.38.212.50 > /etc/resolv.conf":

   .. code-block:: bash

      sh -c "echo nameserver *AutoAD VM or customer-provided domain controller IP* > /etc/resolv.conf"

   Install the NFSv4 client:

   .. code-block:: bash

      yum install -y nfs-utils

   Create a directory named /filesmnt:

   .. code-block:: bash

      mkdir /filesmnt

   Mount the NFS export to the /filesmnt directory:

   .. code-block:: bash

      mount.nfs4 files.ntnxlab.local:/ /filesmnt/

   Show disk utilization for a Linux file system. Observe that the **logs** directory is mounted in ``/filesmnt/logs``:

   .. code-block:: bash

      df -kh

   You will see output similar to the below.

   .. figure:: images/29.png

   Add 100 2MB files filled with random data to ``/filesmnt/logs``:

   .. code-block:: bash

      mkdir /filesmnt/logs/host1
      for i in {1..100}; do dd if=/dev/urandom bs=8k count=256 of=/filesmnt/logs/host1/file$i; done

#. Return to **Prism > File Server > Share > logs** to monitor performance and usage.

Note that the utilization data is updated every 10 minutes.
