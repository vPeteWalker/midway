.. _advanced_aag:

Please complete :ref:`era_mssql` before proceeding.

-----------------------------------
Microsoft SQL Database Availability
-----------------------------------

Up to this point, we have been using Era to create single instance databases. For a production database, a clustered solution to provide high availability is necessary to reduce the chance of downtime for your application, and therefore your business. Era supports provisioning and managing Microsoft SQL Server Always-On Availability Groups (AAG) and Oracle RAC clustered databases.

SQL Server AAG clusters have many moving parts, and manually deploying a single cluster can easily take several hours.

In this lab you will clone an existing production SQL Server database, turn it into a database cluster, and test its availability using the Fiesta app. This requires completion of the :ref:`calmenable` section.

Provision Fiesta Web Tier
+++++++++++++++++++++++++

In this section you'll deploy the web tier of the application, and connect it to your production database.

.. #. `Download the Fiesta Blueprint by right-clicking here <https://raw.githubusercontent.com/nutanixworkshops/EraWithMSSQL/master/deploy_mssql_era/FiestaNoDB.json>`_. This single-VM Blueprint is used to provision only the web tier portion of the application.
..
.. #. From Prism Central, click on :fa:`bars` **Services > Calm**.
..
.. #. Select **Blueprints** from the left-hand menu and click **Upload Blueprint**.
..
.. #. Select **FiestaNoDB.json**.
..
.. #. Select **POC-Project** as the Calm project, and click **Upload**.
..
.. #. Select the **WebServer** Service, and in the **VM** Configuration menu on the right, select **Primary** as the **NIC 1** network.
..
.. #. Click **Credentials**.
..
.. #. Expand the **CENTOS** credential, and paste in the following value as the **SSH Private Key**. Click the icon in the upper right-hand corner of the below window to copy the commands to your clipboard. You may then paste that within Calm.
..
..    ::
..
..      -----BEGIN RSA PRIVATE KEY-----
..      MIIEowIBAAKCAQEAii7qFDhVadLx5lULAG/ooCUTA/ATSmXbArs+GdHxbUWd/bNG
..      ZCXnaQ2L1mSVVGDxfTbSaTJ3En3tVlMtD2RjZPdhqWESCaoj2kXLYSiNDS9qz3SK
..      6h822je/f9O9CzCTrw2XGhnDVwmNraUvO5wmQObCDthTXc72PcBOd6oa4ENsnuY9
..      HtiETg29TZXgCYPFXipLBHSZYkBmGgccAeY9dq5ywiywBJLuoSovXkkRJk3cd7Gy
..      hCRIwYzqfdgSmiAMYgJLrz/UuLxatPqXts2D8v1xqR9EPNZNzgd4QHK4of1lqsNR
..      uz2SxkwqLcXSw0mGcAL8mIwVpzhPzwmENC5OrwIBJQKCAQB++q2WCkCmbtByyrAp
..      6ktiukjTL6MGGGhjX/PgYA5IvINX1SvtU0NZnb7FAntiSz7GFrODQyFPQ0jL3bq0
..      MrwzRDA6x+cPzMb/7RvBEIGdadfFjbAVaMqfAsul5SpBokKFLxU6lDb2CMdhS67c
..      1K2Hv0qKLpHL0vAdEZQ2nFAMWETvVMzl0o1dQmyGzA0GTY8VYdCRsUbwNgvFMvBj
..      8T/svzjpASDifa7IXlGaLrXfCH584zt7y+qjJ05O1G0NFslQ9n2wi7F93N8rHxgl
..      JDE4OhfyaDyLL1UdBlBpjYPSUbX7D5NExLggWEVFEwx4JRaK6+aDdFDKbSBIidHf
..      h45NAoGBANjANRKLBtcxmW4foK5ILTuFkOaowqj+2AIgT1ezCVpErHDFg0bkuvDk
..      QVdsAJRX5//luSO30dI0OWWGjgmIUXD7iej0sjAPJjRAv8ai+MYyaLfkdqv1Oj5c
..      oDC3KjmSdXTuWSYNvarsW+Uf2v7zlZlWesTnpV6gkZH3tX86iuiZAoGBAKM0mKX0
..      EjFkJH65Ym7gIED2CUyuFqq4WsCUD2RakpYZyIBKZGr8MRni3I4z6Hqm+rxVW6Dj
..      uFGQe5GhgPvO23UG1Y6nm0VkYgZq81TraZc/oMzignSC95w7OsLaLn6qp32Fje1M
..      Ez2Yn0T3dDcu1twY8OoDuvWx5LFMJ3NoRJaHAoGBAJ4rZP+xj17DVElxBo0EPK7k
..      7TKygDYhwDjnJSRSN0HfFg0agmQqXucjGuzEbyAkeN1Um9vLU+xrTHqEyIN/Jqxk
..      hztKxzfTtBhK7M84p7M5iq+0jfMau8ykdOVHZAB/odHeXLrnbrr/gVQsAKw1NdDC
..      kPCNXP/c9JrzB+c4juEVAoGBAJGPxmp/vTL4c5OebIxnCAKWP6VBUnyWliFhdYME
..      rECvNkjoZ2ZWjKhijVw8Il+OAjlFNgwJXzP9Z0qJIAMuHa2QeUfhmFKlo4ku9LOF
..      2rdUbNJpKD5m+IRsLX1az4W6zLwPVRHp56WjzFJEfGiRjzMBfOxkMSBSjbLjDm3Z
..      iUf7AoGBALjvtjapDwlEa5/CFvzOVGFq4L/OJTBEBGx/SA4HUc3TFTtlY2hvTDPZ
..      dQr/JBzLBUjCOBVuUuH3uW7hGhW+DnlzrfbfJATaRR8Ht6VU651T+Gbrr8EqNpCP
..      gmznERCNf9Kaxl/hlyV5dZBe/2LIK+/jLGNu9EJLoraaCBFshJKF
..      -----END RSA PRIVATE KEY-----
..
.. #. Click **Save**, and then click **Back** once the Blueprint has completed saving.
..
.. #. Click **Launch**. Fill out the following fields:
..
..    - **Name of the Application** - FiestaWeb
..    - **db_password** - nutanix/4u
..    - **db_name** - Fiesta
..    - **db_dialect** - mssql
..    - **db_domain_name** - ntnxlab.local
..    - **db_username** - Administrator
..    - **db_host_address** - The IP of your *MSSQL2* VM
..
.. #. Click **Create**.
..
.. #. Select the **Audit** tab to monitor the deployment. This process should take <5 minutes.
..
.. #. Once the application status changes to **Running**, select the **Services** tab, and then select the **WebServer** service to obtain the **IP Address** of your web server.

#. In **Prism Central**, select :fa:`bars` **Virtual Infrastructure > VMs**.

#. Click **Create VM** and fill out the following fields:

   - **Name** - FiestaWebProd
   - **vCPUs** - 4
   - **Number of Cores Per vCPU** - 1
   - **Memory** - 4 GiB
   - Click **+ Add New Disk**

      - **Type** - Disk
      - **Operation** - Clone from Image Service
      - **Bus Type** - SCSI
      - **Image** - CentOS_7_Cloud.qcow2
      - Click **Add**

   - Click **+ Add New NIC**

      - **Network Name** - Primary
      - Click **Add**

   - Select **Custom Script**
   - Select **Type or Paste Script**. Click the icon in the upper right-hand corner of the below window to copy the script to your clipboard. You may then paste the following **Cloud-init** script:

   .. literalinclude:: webserver.cloudconfig
    :linenos:
    :language: YAML

   .. warning::

      Before proceeding, modify the **YOUR-MSSQL-VM-IP-ADDRESS** entry within line 104 in the Cloud-init script. No other modifications are necessary.

      Example: `- sed -i 's/REPLACE_DB_HOST_ADDRESS/10.42.69.85/g' /home/centos/Fiesta/config/config.js`

#. Click **Save**.

#. Power on the VM and click **Launch Console**.

#. Monitor the VM deployment until the console indicates that Cloud-init has finished.

#. In your browser, browse to the IP address of your **WebServer** VM and validate the application is running (as seen below).

   .. figure:: images/19.png

#. Open `http://<WEBSERVER-IP-ADDRESS>:5001` in a new browser tab to access the *Fiesta* application.

Creating an Era Managed Network
+++++++++++++++++++++++++++++++

#. In Era, from the dropdown, choose **Administration**. Click **Era Resources** from the left-hand side.

   .. figure:: images/3.png

#. Fill out the fields and click **Add**.

   .. figure:: images/4.png

#. From the dropdown, choose **Profiles**. Click **Network** from the left-hand side.

#. Click :ref:`plus`**Create > Microsoft SQL Server > Database Server VMs**.

#. Fill out the following fields:

   - **Name** - ERAMANAGED_MSSQL
   - **Public Service VLAN** - Era

   .. figure:: images/5.png

#. Click **Create**.

Provisioning an AAG
+++++++++++++++++++

Before deploying our AAG, let's make a quick update to our application.

#. Open `<http://WebServer-IP-address:5001>`_ in another browser tab. (ex. `<http://10.42.212.50:5001>`_)

#. Under **Stores**, click **Add New Store** and fill out the required fields. Validate your new store appears in the UI.

   .. figure:: images/5a.png

#. From within Era, select **Time Machines** from the dropdown menu.

#. Select **fiesta_TM**, then from the *Actions* menu, choose **Create Database Clone > Availability Database**.

   By default, a clone will be created from the most recent *Point in Time*. Alternatively you can explicitly specify a previous point in time or snapshot.

The *Create SQL Server Availability Database Clone from Time Machine* window will appear, beginning with the *Time/Snapshot* section.

#. Click **Next**.

   .. figure:: images/6.png

The *Server Cluster* section will appear.

#. *New Server Cluster* section:

   - **Windows Cluster Name** - FiestaCluster (cluster name has a 15 character limit)
   - **Windows Domain Profile** - NTNXLAB
   - **Network Profile** - ERAMANAGED_MSSQL

#. *Database Server VMs in the Cluster* section:

   - **Compute Profile** - DEFAULT_OOB_COMPUTE
   - **Administrator Password** - nutanix/4u

#. *SQL Server Instance: MSSQLSERVER* section:

   - **SQL Service Startup Account** - ntnxlab.local\\Administrator
   - **SQL Service Startup Account Password** - nutanix/4u

   .. figure:: images/7a.png

#. Click **Next**.

The *AG* section will appear.

#. Click **Next**.

   .. note::

      SQL 2016 and above supports up to 9 secondary replicas.

      The **Primary** server indicates which host you want the AAG to start on.

      **Auto Failover** allows the AAG to failover automatically when it detects the **Primary** host is unavailable. This is preferred in most deployments as it requires no additional administrator intervention, allowing for maximum application uptime.

      **Availability Mode** can be configured as either **Synchronous** or **Asynchronous**.

      - **Synchronous-commit replicas** - Data is committed to both primary and secondary nodes at the same time. This mode supports both **Automatic** and **Manual Failover**.
      - **Asynchronous-commit replicas** - Data is committed to primary first and then after some time-interval, data is committed to the secondary nodes. This mode only supports **Manual Failover**.

      **Readable Secondaries** allows you to offload your secondary read-only workloads from your primary replica, which conserves its resources for your mission critical workloads. If you have mission critical read-workload or the workload that cannot tolerate latency (up to a few seconds), you should run it on the primary.

The *Database* section will appear.

#. Click **Clone**.

#. Monitor the refresh on the **Operations** page. This operation should take approximately 35 minutes. You can proceed to the next section while your clustered database servers are provisioned.

Configure Fiesta for AAG
++++++++++++++++++++++++

Rather than deploy an additional Fiesta web server VM, you will update the configuration of your existing webserver to reference the newly-created database cluster, instead of a single database server. A real world equivalent would be a small customer with a single database and webserver (perhaps a single VM or physical server running both database and webserver). Era could be used in that scenario to clone the existing database into two database servers, configured with Always-On Availability. The result would be greatly reduced or eliminated potential downtime.

#. From within Era, select **Databases** from the dropdown, and from the left-hand side, choose **Clones**.

#. Expand the *FiestaCluste_AG* selection, and then click on the most recent clone to view the details of the AAG deployment. Note the *Listener IP Address* within the *Always on Availability Group* section.

   .. figure:: images/11.png

#. Open an SSH session, and log into the *Fiesta* web server using the following credentials:

   - **Username** - centos
   - **Password** - nutanix/4u

#. Run `cat Fiesta/config/config.js` to display the current Fiesta config.

#. Run `sudo sed -i 's/CURRENT_DB_HOST_ADDRESS_VALUE/AAG_LISTENER_IP_ADDRESS_VALUE/g' ~/Fiesta/config/config.js` (ex. `sudo sed -i 's/10.42.69.62/10.42.69.109/g' ~/Fiesta/config/config.js`) to modify the Fiesta config to reference the newly-created AAG.

#. Run `cat Fiesta/config/config.js` to confirm the update was successful.

   .. figure:: images/12.png

      Before

   .. figure:: images/13.png

      After

#. Run `sudo systemctl restart fiesta` to apply the configuration changes.

.. note::

   The same command can be used to modify any portion of the config.js file used for Fiesta. For example, perhaps you entered a typo in the domain name, and it would be faster to correct it, versus completely redeploying the Fiesta server blueprint.

   `sudo sed -i 's/ntnxlabTYPO.local/ntnxlab.local/g' ~/Fiesta/config/config.js`

Failing A Cluster Server
++++++++++++++++++++++++

#. Within your *Fiesta* web app, make any changes to the store, such as deleting a store and/or adding additional products to a store. Go bananas!

   .. figure:: images/15.png

#. Within Prism Central, click on :fa:`bars` **Virtual Infrastructure > VMs**.

You can determine check which VM is currently the primary member of the AAG by noting which VM currently displays the AAG's Listener IP Address and Windows Cluster IP in Prism Central.

#. Power off the primary VM.

   .. figure:: images/16.png

#. Refresh **Prism Central** and note that the **Listener** and **Cluster** IP addresses are now assigned to the other *FiestaCluster* VM.

   .. figure:: images/17.png

#. Refresh your *Fiesta* web app, and ensure it is operating correctly by making a few more changes.

   .. figure:: images/18.png

Takeaways
+++++++++

What are the key things we learned in this lab?

- Production databases require high levels of availability to prevent downtime.
- Era makes the deployment of complex, clustered databases as easy (and as fast) as single instance databases - and many times faster than deploying manually!
- Existing databases can be easily onboarded into Era, and turned into templates from which to deploy any number of additional database servers.
- Customizable recovery SLAs allow you to tune continuous, daily, and monthly RPO based on your app's requirements.
- Era provides one-click provisioning, and automatic application of database best practices.
