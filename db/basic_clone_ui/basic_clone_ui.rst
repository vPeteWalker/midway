.. _basic_clone_ui:

-------------------------
Time Machines and Cloning
-------------------------

Era provides the *Time Machine* functionality to simplify cloning operations. Time Machine captures and maintains snapshots and transactional logs of your source databases as defined in the schedule. For every source database you register with Era, a Time Machine is created for that source database. You'd then create clones that are not only space efficient, but enable you to refresh clones either to a point in time (by using transactional logs) or by using snapshots. These operations eliminate what was a previously manual process, thus allowing the DBA to focus their efforts on more pressing endeavors.

Copy Data Management (CDM) (a.k.a. database cloning) is a critical day 2 database operation. Multiple teams, such as developers, QA, and analysts often request separate copies of production databases for their own purposes. Each additional clone comes with a cost: an ever-increasing strain on the company's storage capacity. Two clones? Double the storage, and so forth. An additional challenge is providing these teams with the most up-to-date production data from which to test. All too often this is a manual task, requiring the full attention of a database administrator (DBA).

In this lab you will use Era to create a clone of your SQL Server database to be used as part of a test environment. After making changes to your production database, you will refresh your test environment.

Cloning from the Era UI
+++++++++++++++++++++++

In this exercise you will explore the workflow for cloning a database through the Era web interface.

#. In **Era**, select **Time Machines** from the dropdown menu.

#. Select the Time Machine associated with your production database (e.g. *fiesta_TM*).

#. Select **Actions > Create Database Clone > Database**.

By default, a clone will be created from the most recent *Point in Time*. Alternatively, you can specify a previous point in time or snapshot.

#. Click **Next**.

   .. figure:: images/1.png

   Databases can be cloned alongside an entirely new server, automatically provisioned by Era, or as an additional database inside an existing database server.

#. Make the following selections and click **Next**:

   - **Database Server** - Create New Server
   - **Database Server Name** - FiestaDev
   - **Compute Profile** - DEFAULT_OOB_COMPUTE
   - **Network Profile** - DEFAULT_OOB_SQLSERVER_NETWORK
   - **Administrator Password** - nutanix/4u
   - Select **Join Domain**
   - **Windows Domain Profile** - NTNXLAB
   - **Domain User Account** - ntnxlab.local\\Administrator

   .. figure:: images/2.png THIS NEEDS A NEW SS WITH FIESTADEV NAME

#. Click **Clone**.

#. From the dropdown, select **Operations** to monitor the progress. This should take approximately 15 minutes.

Refreshing Cloned Databases
+++++++++++++++++++++++++++

Now that you have a functioning "development" environment, it's time to create some changes within your production environment.

#. In a new browser tab, return to your *Production* Fiesta web app. Click **Products > Add New Product**.

   .. figure:: images/16.png

#. Fill out the following fields and click **Submit**:

   - **Product Name** - The Best Balloons
   - **Suggested Retail Price** - 100.00
   - **Product Image URL** - `https://partycity6.scene7.com/is/image/PartyCity/_pdp_sq_?$_1000x1000_$&$product=PartyCity/251182`
   - **Product Comments** - Everybody Knows

   .. figure:: images/17.png

#. Click **Stores** from the menu and select **View Store** from one of the available stores.

#. Click **Add New Store Product**. Fill out the following fields and click **Submit**:

   - **Product Name** - The Best Balloons
   - **Local Product Price** - 99.99
   - **Initial Qty** - 1000

#. Verify the inventory for the added product appears on the **Store Details** page.

   .. figure:: images/18.png

#. In a separate browser tab, open your **Dev** Fiesta web app. Confirm that the products and inventory added to the **Production** instance are not present.

#. In **Era > Time Machines**, select the Time Machine that corresponds to your production database. Select **Actions > Log Catch Up > Yes** to ensure the latest database entries have been flushed to disk.

#. Monitor the log catch up on the **Operations** page. This should take approximately 1 minute.

#. In **Era > Databases > Clones**, select your cloned database and click **Refresh**.

   .. figure:: images/21.png

#. By default, the database will be refreshed to the most recent **Point in Time**, but you can manually specify a time or individual snapshot. For the purposes of this exercise, use the most recent time. Click **Refresh**.

#. Monitor the refresh on the **Operations** page. This should take approximately 5 minutes.

#. Once the refresh has completed, open your **Dev** Fiesta web app and validate the product and inventory data now matches your production database.

   .. figure:: images/18.png

   With a few mouse clicks, your DBA was able to push current production data to the cloned database. This could be further automated through the Era CLI or APIs.

Takeaways
+++++++++

What are the key things we learned in this lab?

- Era makes it simple to create space efficient, zero-byte database clones to any point-in-time.
- Era provides production-like quality of service (QoS) for clones, with fast creation and data refresh.
