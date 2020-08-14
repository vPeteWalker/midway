.. _singlevmwin:

-------------------------
Flow Logging with GrayLog
-------------------------

In this module you'll deploy and configure an open source version of GrayLog, an application used to collect, store, and analyze log data. GrayLog is distributed as a virtual appliance, but also as a pre-seeded Calm Marketplace Blueprint, making it easy to deploy in your POC environment. This can be used for demonstating AHV/API audit logging and Flow policy hit logging, if a customer does not have a syslog server that can be used for your POC.

If a customer already has a logging solution that can be used, such as Splunk, you can still follow these instructions to understand how the listening should be configured, how to divide logs into separate streams, and how to configure Prism Central to connect to syslog.

.. note::

   This setup is for minimal retention on a single GrayLog instance without blowing out your cluster storage. Do not follow these instructions if you need longer retention or a distributed system. This should be great for a demo - but will be different for production.

**Prerequisites:** Completion of :ref:`calmenable`

**Expected Module Duration:** 30 minutes

**Covered Test IDs:** N/A

Configuring Project Environment
+++++++++++++++++++++++++++++++

*To launch a Blueprint directly from the Marketplace, we need to define a set of defaults to ensure our Project has all of the requisite environment details to satisfy the Blueprint. These are configured on a per Cloud type basis, allowing a single Blueprint to potentially be deployed to multiple types of Clouds.*

#. Select |projects| **Projects** from the left-hand toolbar.

#. Select your **POC-Project**.

#. Select the **Environment** tab.

#. Under **Credential**, click :fa:`plus-circle` and enter the following:

   - **Credential Name** - CENTOS
   - **Username** - centos
   - **Secret** - SSH Private Key
   - **SSH Private Key** - Generate your own Private Key, or use the provided key below:

   ::

      -----BEGIN RSA PRIVATE KEY-----
      MIIEowIBAAKCAQEAii7qFDhVadLx5lULAG/ooCUTA/ATSmXbArs+GdHxbUWd/bNG
      ZCXnaQ2L1mSVVGDxfTbSaTJ3En3tVlMtD2RjZPdhqWESCaoj2kXLYSiNDS9qz3SK
      6h822je/f9O9CzCTrw2XGhnDVwmNraUvO5wmQObCDthTXc72PcBOd6oa4ENsnuY9
      HtiETg29TZXgCYPFXipLBHSZYkBmGgccAeY9dq5ywiywBJLuoSovXkkRJk3cd7Gy
      hCRIwYzqfdgSmiAMYgJLrz/UuLxatPqXts2D8v1xqR9EPNZNzgd4QHK4of1lqsNR
      uz2SxkwqLcXSw0mGcAL8mIwVpzhPzwmENC5OrwIBJQKCAQB++q2WCkCmbtByyrAp
      6ktiukjTL6MGGGhjX/PgYA5IvINX1SvtU0NZnb7FAntiSz7GFrODQyFPQ0jL3bq0
      MrwzRDA6x+cPzMb/7RvBEIGdadfFjbAVaMqfAsul5SpBokKFLxU6lDb2CMdhS67c
      1K2Hv0qKLpHL0vAdEZQ2nFAMWETvVMzl0o1dQmyGzA0GTY8VYdCRsUbwNgvFMvBj
      8T/svzjpASDifa7IXlGaLrXfCH584zt7y+qjJ05O1G0NFslQ9n2wi7F93N8rHxgl
      JDE4OhfyaDyLL1UdBlBpjYPSUbX7D5NExLggWEVFEwx4JRaK6+aDdFDKbSBIidHf
      h45NAoGBANjANRKLBtcxmW4foK5ILTuFkOaowqj+2AIgT1ezCVpErHDFg0bkuvDk
      QVdsAJRX5//luSO30dI0OWWGjgmIUXD7iej0sjAPJjRAv8ai+MYyaLfkdqv1Oj5c
      oDC3KjmSdXTuWSYNvarsW+Uf2v7zlZlWesTnpV6gkZH3tX86iuiZAoGBAKM0mKX0
      EjFkJH65Ym7gIED2CUyuFqq4WsCUD2RakpYZyIBKZGr8MRni3I4z6Hqm+rxVW6Dj
      uFGQe5GhgPvO23UG1Y6nm0VkYgZq81TraZc/oMzignSC95w7OsLaLn6qp32Fje1M
      Ez2Yn0T3dDcu1twY8OoDuvWx5LFMJ3NoRJaHAoGBAJ4rZP+xj17DVElxBo0EPK7k
      7TKygDYhwDjnJSRSN0HfFg0agmQqXucjGuzEbyAkeN1Um9vLU+xrTHqEyIN/Jqxk
      hztKxzfTtBhK7M84p7M5iq+0jfMau8ykdOVHZAB/odHeXLrnbrr/gVQsAKw1NdDC
      kPCNXP/c9JrzB+c4juEVAoGBAJGPxmp/vTL4c5OebIxnCAKWP6VBUnyWliFhdYME
      rECvNkjoZ2ZWjKhijVw8Il+OAjlFNgwJXzP9Z0qJIAMuHa2QeUfhmFKlo4ku9LOF
      2rdUbNJpKD5m+IRsLX1az4W6zLwPVRHp56WjzFJEfGiRjzMBfOxkMSBSjbLjDm3Z
      iUf7AoGBALjvtjapDwlEa5/CFvzOVGFq4L/OJTBEBGx/SA4HUc3TFTtlY2hvTDPZ
      dQr/JBzLBUjCOBVuUuH3uW7hGhW+DnlzrfbfJATaRR8Ht6VU651T+Gbrr8EqNpCP
      gmznERCNf9Kaxl/hlyV5dZBe/2LIK+/jLGNu9EJLoraaCBFshJKF
      -----END RSA PRIVATE KEY-----

   - Click the **Running Man** icon above SSH Private Key to mark this variable as **Runtime**.

   .. figure:: images/0.png

   .. note::

      See `here <https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/create-with-openssh/>`_ for instructions on creating SSH keys using macOS or Linux, or `here <https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/create-with-putty/>`_ for Windows.

#. Under **VM Configuration > Nutanix** expand **Linux**, and enter the following:

   - **VM Name** - vm-@@{calm_array_index}@@-@@{calm_time}@@ (Default)
   - **vCPUs** - 4
   - **Cores per vCPU** - 1
   - **Memory** - 4 GiB
   - **Image** - CentOS_7_Cloud
   - **NICs** - Click :fa:`plus-circle`, then selecting **Primary** in the dropdown
   - **Private IP** - Dynamic
   - Select **Check log-in upon create**
   - **Credential** - CENTOS (Defined Above)

   .. figure:: images/1.png
      :align: left
      :scale: 50%
   .. figure:: images/2.png
      :align: right
      :scale: 50%

#. Click **Save**.

Launching the GrayLog Blueprint
+++++++++++++++++++++++++++++++

#. Select |marketplacemgr| **Marketplace Manager** in the left-hand toolbar to view and manage Marketplace Blueprints.

   *Here we'll see all of the pre-seeded Blueprints included with Nutanix Calm available for publishing into your Marketplace. Because we only need to provision a single instance of this appliance, and don't want to offer it as a self-service application to users, we'll launch the Blueprint directly rather than publishing.*

#. Enter *graylog* in the **Search** field and select the latest **GrayLog** Blueprint.

   .. figure:: images/3.png

#. Click **Launch** and select your **POC-Project** from the drop-down menu. Click **Launch**.

#. Fill out the following fields and click **Create**:

   - **Name of the Application** - Syslog
   - **ADMIN_EMAIL** - *You or your customer's e-mail address*
   - **ADMIN_PASSWORD** - Nutanix/4u

   .. figure:: images/4.png

#. Wait for your application to enter a **Running** state (~10 minutes).

Configuring GrayLog
+++++++++++++++++++

*Before we can begin exporting our logs from Prism Central, we'll need to complete some quick configuration steps for our syslog server.*

#. Note the **IP Address** of your GrayLog VM.

   .. figure:: images/5.png

#. In your web browser, open http://<GRAYLOG-VM-IP>:9000 to access the GrayLog web interface.

#. Log in:

   - **Username** - admin
   - **Password** *The ADMIN_PASSWORD value set when launching the Blueprint (e.g. Nutanix/4u)*

Adding Inputs
.............

*First we need to configure GrayLog to listen on a specific TCP or UDP port for incoming messages.*

#. Click **System > Inputs**.

   .. figure:: images/6.png

#. In the **Select input** field, search for **Syslog TCP** and click **Launch new input**.

   .. figure:: images/7.png

#. Fill out the following fields (leave others as default), and click **Save**:

   - Select **Global**
   - **Title** - NutanixSyslogTCP
   - **Bind address** - 0.0.0.0 (Ensures GrayLog is listening on all network interfaces, though there should be only 1 in this scenario)
   - **Port** - 20514

   .. note::

      Prism Central `currently only supports <https://jira.nutanix.com/browse/ENG-224739>`_ the following ports for sending syslog messages: 601, 6514, 10514, 20514

   .. figure:: images/8.png

#. Verify the input shows as **Running**, otherwise click **Start Input**.

   .. figure:: images/9.png

Configuring Indexes
...................

*Indexes allow you to manage storage and retention policies for certain types of data. If you have data that you need to keep for a year, vs data you only need to keep for a few days - you can create separate indexes for them. Let’s create THREE indexes on our server and use time as a boundary for data retention. If you were VERY concerned about storage you can also rotate indices based on storage size. A few PC clusters for demo should stay well within 40GB with these settings. This is important because the graylog server from the blueprint deploys with a single fixed size disk.*

#. Select **System > Indices** from the toolbar.

#. Click **Create index set**.

   *Our first index is for Flow Policy Hit Logs, which is something you may want to keep for 1-2 months. So we'll keep 6 indices of 1 week each, for a total of 6 weeks.*

#. Fill out the following fields (leaving others as default), and click **Save**:

   - **Title** - flow_hit
   - **Description** - Flow policy hit logs
   - **Index Prefix** - flow_hit
   - **Index Shards** - 1
   - **Select rotation strategy** - Index Time
   - **Rotation period** - P1W (7 Days)
   - **Max number of indices** - 6

   .. figure:: images/10.png

   .. note::

      We are ignoring shards and segments entirely in this example because this is a single VM deployment. This would be important to consider for larger deployments or production. Don’t worry about it for your Flow demos.

#. Click **Create index set**.

   *Next we'll create an index for Flow audit logs, which will show what user made which policy changes, and when. This is something you may want to keep for several months, so we'll create 6 indices of 1 month each, for a total of 6 months.*

#. Fill out the following fields (leaving others as default), and click **Save**:

   - **Title** - flow_audit
   - **Description** - Flow audit logs
   - **Index Prefix** - flow_audit
   - **Index Shards** - 1
   - **Select rotation strategy** - Index Time
   - **Rotation period** - P1M (1 Month)
   - **Max number of indices** - 6

#. Under **Default index set**, click **Edit**.

   .. figure:: images/11.png

   *The last index will be a catch all for "everything else." As we don't care about this data, we'll set it to rotate every day and only keep the last 2 days of data.*

#. Fill out the following fields (leaving others as default), and click **Save**:

   - **Select rotation strategy** - Index Time
   - **Rotation period** - P1D (1 Day)
   - **Max number of indices** - 2

Configuring Streams
...................

*There are two data streams that come from the cluster, and it’s useful to separate them. First, we have the policy hit logs which are a series of new, updated, or destroyed connections containing connection details. The next set is audit logs, which track who made what change, and when, to policies. You probably only want to look at one set of data at a time, so we separate them into two streams.*

#. Select **Streams** from the toolbar.

   .. figure:: images/12.png

   *First we'll create a stream to filter Audit logs into our Audit index.*

#. Click **Create Stream**, fill out the following fields, and click **Save**:

   - **Title** - Flow Audit Log Stream
   - **Description** - Flow Audit Logs
   - **Index Set** - Select flow_audit from the drop-down menu
   - Select **Remove matches from 'All Messages' stream**

   .. figure:: images/13.png

#. Under your newly created stream, click **Manage Rules**.

   .. figure:: images/14.png

#. Click **Add Stream Rule** and fill out the following fields:

   - **Field** - message
   - **Type** - contain
   - **Value** - consolidated_audit

   .. figure:: images/15.png

   *To define rules you need some understanding of what the payload of your logging messages looks like, in this case we know that Flow audit logs always contain the string "consolidated_audit" in the "message" field. Regular expressions can also be used to build more complex rules for filtering messages.*

#. Click **Save**. Click **I'm done!**.

   *Next we'll create a stream to filter Policy Hit logs into our policy hit index.*

#. Click **Create Stream**, fill out the following fields, and click **Save**:

   - **Title** - Flow Hit Log Stream
   - **Description** - Flow Hit Logs
   - **Index Set** - Select flow_hit from the drop-down menu
   - Select **Remove matches from 'All Messages' stream**

#. Under your newly created stream, click **Manage Rules**.

#. Click **Add Stream Rule** and fill out the following fields:

   - **Field** - message
   - **Type** - match regular expression
   - **Value** - hitCount[1-4]

#. Click **Save**. Click **I'm done!**.

#. For each of your two created streams, click **Start Stream**.

   .. figure:: images/16.png

Configuring Syslog in Prism Central
+++++++++++++++++++++++++++++++++++

*Now that we have a properly configured syslog server, all that's left to do is tell Prism Central how to access syslog, and select which types of logs we want to send.*

#. Select :fa:`bars` **> Prism Central Settings**.

#. Under **Alerts and Notifications**, select **Syslog Server**.

#. Click **+ Configure Syslog Server**.

#. Fill out the following fields and click **Configure**:

   - **Server Name** - Syslog (or something else descriptive)
   - **IP Address** - Your Syslog (GrayLog VM) IP
   - **Port** - Your previously configured Syslog listening port (e.g. 20514)
   - **Transport Protocol** - TCP

   .. figure:: images/17.png

# Under **Data Sources**, click **+Edit**.

   .. figure:: images/18.png

#. Enable **Severity Level 6** logs for both **Audit** and **Flow**. Click **Save**.

   .. figure:: images/19.png

Enabling Policy Logging
+++++++++++++++++++++++

Policy Hit logging is enabled on a *per Security Policy* basis. When creating or editing a policy, such as those in :ref:`flowbasic` or :ref:`flowappsec`, simply select **Enabled** under **Policy Hit Logs**, as seen below.

.. figure:: images/20.png

Viewing Data in GrayLog
+++++++++++++++++++++++

#. In **GrayLog**, click **Streams** and select either your **Flow Audit Log Stream** or **Flow Hit Log Stream**.

   .. figure:: images/21.png

#. You can use filtering, live updating, time range selection, etc. or use the search feature to target specific policy names or IPs that may be trying to access resources blocked by a policy.

   .. figure:: images/22.png

.. |marketplacemgr| image:: images/marketplacemanager.png
.. |marketplace| image:: images/marketplace.png
.. |Blueprints| image:: images/Blueprints.png
.. |applications| image:: images/Blueprints.png
.. |projects| image:: images/projects.png
