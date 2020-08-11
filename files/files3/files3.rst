.. _files3:

----------
NFS export
----------

Creating an NFS export
......................

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

.. Testing with client desktop
.. ...........................
..
.. AutoAD is pre-populated with the following Users and Groups for your use:
..
..    .. list-table::
..       :widths: 25 35 40
..       :header-rows: 1
..
..       * - Group
..         - Username(s)
..         - Password
..       * - Administrators
..         - Administrator
..         - nutanix/4u
..       * - SSP Admins
..         - adminuser01-adminuser25
..         - nutanix/4u
..       * - SSP Developers
..         - devuser01-devuser25
..         - nutanix/4u
..       * - SSP Consumers
..         - consumer01-consumer25
..         - nutanix/4u
..       * - SSP Operators
..         - operator01-operator25
..         - nutanix/4u
..       * - SSP Custom
..         - custom01-custom25
..         - nutanix/4u
..       * - Bootcamp Users
..         - user01-user25
..         - nutanix/4u
..
..
.. #. Deploy new Windows 10 VM.
..
.. #. Configure static IP, and configure DNS to point to AutoAD.
..
.. #. Change the computer Name.
..
.. #. Join the *ntnxlab.local* domain.
..
.. #. Login to domain as chosen user from above list.
..
.. #. Map the newly created share(s) in your directory. In the Windows client, you can map to the network and create folders at the top level of the file share.
..
..    - In the Windows client VM, open *File Explorer*. Right click on **This PC** and select **Map Network Drives**.
..
..    - Select the drive letter to use for the share. Enter the path to the share in the `\\`*FileServerFQDN*`\`*share* format. Click the **Reconnect at sign-in** box, and then click **Finish**.
..
..    .. figure:: images/12.png
..
..    A new window will open displaying the contents of the share. You may close this window.
..
.. #. Repeat the process for any additional shares.

Testing the NFS export
......................

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
