.. _advanced_aag:

Please complete :ref:`era_mssql` before proceeding.

-----------------------------------
Microsoft SQL Database Availability
-----------------------------------

Up to this point, we have been using Era to create single instance databases. For a production database, a clustered solution to provide high availability is necessary to reduce the chance of downtime for your application, and therefore your business. Era supports provisioning and managing Microsoft SQL Server Always-On Availability Groups (AAG) and Oracle RAC clustered databases.

SQL Server AAG clusters have many moving parts, and manually deploying a single cluster can easily take several hours.

In this lab you will clone an existing production SQL Server database, turn it into a database cluster, and test its availability using the Fiesta app.

Creating an Era Managed Network
+++++++++++++++++++++++++++++++

#. In Era, from the dropdown, choose **Administration**. Click **Era Resources** from the left-hand side.

   .. figure:: images/3.png

#. Fill out the fields and click **Add**.

   .. figure:: images/4.png

#. From the dropdown, choose **Profiles**. Click **Network** from the left-hand side.

#. Click :ref:`plus` **Create > Microsoft SQL Server > Database Server VMs**.

#. Fill out the following fields:

   - **Name** - ERAMANAGED_MSSQL
   - **Public Service VLAN** - Era

   .. figure:: images/5.png

#. Click **Create**.

Provisioning an AAG
+++++++++++++++++++

Before deploying our AAG, let's make a quick update to our application.

#. Open `<http://FIESTAWEB_PROD-IP-address:5001>`_ in another browser tab. (ex. `<http://10.42.212.50:5001>`_)

#. Under **Stores**, click **Add New Store** and fill out the required fields. Validate your new store appears in the UI.

   .. figure:: images/5a.png

#. From within Era, select **Time Machines** from the dropdown menu.

#. Select **FiestaDB_Prod_TM**, then from the *Actions* menu, choose **Create Database Clone > Availability Database**.

   By default, a clone will be created from the most recent *Point in Time*. Alternatively you can explicitly specify a previous point in time or snapshot.

   The *Create SQL Server Availability Database Clone from Time Machine* window will appear, beginning with the *Time/Snapshot* section.

#. Click **Next**.

   .. figure:: images/6.png

   The *Server Cluster* section will appear.

      - *New Server Cluster* section:

         - **Windows Cluster Name** - FiestaCluster (cluster name has a 15 character limit)
         - **Windows Domain Profile** - NTNXLAB
         - **Network Profile** - ERAMANAGED_MSSQL

      - *Database Server VMs in the Cluster* section:

         - **Compute Profile** - DEFAULT_OOB_COMPUTE
         - **Administrator Password** - nutanix/4u

      - *SQL Server Instance: MSSQLSERVER* section:

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

Monitor the refresh on the **Operations** page. This operation should take approximately 35 minutes. You can proceed to the next section while your clustered database servers are provisioned.

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
