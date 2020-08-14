.. _flowappsec:

---------------------
Securing Applications
---------------------

In this module you will use Flow to secure communications within an example multi-tier application consisting of webserver and database CentOS VMs. For the purpose of streamlining the POC, the application has been provided in the form of a Calm Blueprint. The same overall principles for creating the policy and talking points would apply if using a customer provided application, you would simply need to account for differences in category assignment and connection ports/protocols.

**Pre-requisites:** Completion of :ref:`calmenable` and :ref:`deploypocapp`

**(Optional) Pre-requisite:** Completion of :ref:`deploygraylog`

**Expected Module Duration:** 45 minutes

**Covered Test IDs:** N/A

*With a real application in place to provide HTTP and database traffic, we can now walk through how Flow can be used to provide even finer-grained controls within the datacenter.*

Adding Categories
+++++++++++++++++

*We'll continue to make use of the built-in categories, but add some new values that will be specific to our new use case.*

#. In **Prism Central**, select :fa:`bars` **> Virtual Infrastructure > Categories**.

#. Select the checkbox for **AppType** and click **Actions > Update**.

   .. note::

      Alternatively you can click on the **Name** of the Category to see all the values and associated entities, and then click **Update**.

#. Click the :fa:`plus-circle` icon beside the last value to add an additional Category value.

#. Specify **POCApp** as the value name.

   .. figure:: images/0.png

#. Click **Save**.

#. Select the checkbox for **AppTier** and click **Actions > Update**.

#. Click the :fa:`plus-circle` icon beside the last value to add an additional Category value.

#. Specify **Web** as the value name. This category will be applied to the application's web tier.

#. Click :fa:`plus-circle` and specify **DB**. This category will be applied to the application's MySQL database tier.

   .. figure:: images/1.png

#. Click **Save**.

Creating a Security Policy
..........................

*Nutanix Flow includes a policy-driven security framework that uses a workload-centric approach instead of a network-centric approach. Therefore, it can scrutinize traffic to and from VMs no matter how their network configurations change and where they reside in the data center. The workload-centric, network-agnostic approach also enables the virtualization team to implement these security policies without having to rely on network security teams.*

*Security policies are applied to categories and not to the VMs themselves. Therefore, it does not matter how many VMs are started up in a given category. Traffic associated with the VMs in a category is secured without administrative intervention, at any scale.*

*...*

#. In **Prism Central**, select :fa:`bars` **> Policies > Security Policies**.

#. Click **Create Security Policy > Secure Applications (App Policy) > Create**.

#. Fill out the following fields:

   - **Name** - POC-App
   - **Purpose** - Restrict unnecessary access to POC-App
   - **Secure this app** - AppType: POCApp
   - Do **NOT** select **Filter the app type by category**
   - (Optional, if **Syslog** configured for cluster) Enable **Policy Hit Logs**

   .. figure:: images/2.png

#. Click **Next**.

#. If prompted, click **OK, Got it!** on the tutorial diagram of the **Create App Security Policy** wizard.

   *By default, the policy builder will let you control what goes in and comes out of an application based on its AppType category, but we want to get more granular than that, to ensure only certain traffic is allowed based on the individual tiers - letting us allow client traffic to our web tier, but not allow any direct client traffic to the database.*

#. Click **Set rules on App Tiers, instead**.

   .. figure:: images/3.png

#. Click **+ Add Tier**.

#. Select **AppTier:Web** from the drop down.

#. Repeat to add **AppTier:DB**.

   .. figure:: images/4.png

   *Next you will define the Inbound rules, which control which sources you will allow to communicate with your application. You can allow all inbound traffic, or define whitelisted sources. By default, the security policy is set to deny all incoming traffic.*

   *In this scenario we want to allow inbound TCP traffic to the web tier on TCP port 80 from all clients.*

#. Under **Inbound**, click **+ Add Source**.

#. Fill out the following fields to allow all inbound IP addresses:

   - **Add source by:** - Select **Subnet/IP**
   - Specify **0.0.0.0/0**

   *Sources can also be specified by Categories, allowing for greater flexibility as this data can follow a VM regardless of changes to its network location. As an example, you could add a category for Administrator desktops that would also allow connections to the web and database via SSH (TCP Port 22).*

#. To create an inbound rule, select the **+** icon that appears to the left of **AppTier:Web**.

   .. figure:: images/5.png

#. Fill out the following fields:

   - **Protocol** - TCP
   - **Ports** - 80

   *Multiple protocols and ports can be added to a single rule.*

#. Click **Save**.

   Calm could also require access to the VMs for workflows including scaling out, scaling in, or upgrades. Calm communicates with these VMs via SSH, using TCP port 22.

#. Under **Inbound**, click **+ Add Source**.

#. Fill out the following fields:

   - **Add source by:** - Select **Subnet/IP**
   - Specify *Your Prism Central IP*\ /32

   .. note::

     The **/32** denotes a single IP as opposed to a subnet range.

#. Click **Add**.

   .. figure:: images/6.png

#. Select the **+** icon that appears to the left of **AppTier:Web**, specify **TCP** port **22** and click **Save**.

#. Repeat the previous step for **AppTier:DB** to allow Calm to communicate with the database VM.

   .. figure:: images/7.png

   *By default, the security policy allows the application to send all outbound traffic to any destination. For this example we'll assume the only outbound communication required for your application is to communicate with your DNS server.*

#. Under **Outbound**, select **Whitelist Only** from the drop down menu, and click **+ Add Destination**.

#. Fill out the following fields:

   - **Add Destination by:** - Select **Subnet/IP**
   - Specify *Your Domain Controller IP*\ /32

#. Click **Add**.

#. Select the **+** icon that appears to the right of **AppTier:Web**, specify **UDP** port **53** and click **Save** to allow DNS traffic. Repeat this for **AppTier:DB**.

   .. figure:: images/8.png

   *Each tier of the application communicates with other tiers and the policy must allow this traffic. Some tiers such as web do not require communication within the same tier.*

#. To define intra-app communication, click **Set Rules within App**.

   .. figure:: images/9.png

#. Click **AppTier:Web** and select **No** to prevent communication between VMs in this tier.

   *If this application scaled out to multiple webserver VMs, there wouldn't be a reason for them to communicate with one another, so this reduces attack surface.*

#. While **AppTier:Web** is still selected, click the :fa:`plus-circle` icon to the right of **AppTier:DB** to create a tier-to-tier rule.

   .. figure:: images/10.png

#. Fill out the following fields to allow communication on TCP port 3306 between the web and database tiers:

   - **Protocol** - TCP
   - **Ports** - 3306

   *This is the default port for communication with the MySQL service on the database VM.*

#. Click **Save**.

   .. figure:: images/11.png

#. Click **Next** to review the security policy.

#. Click **Save and Monitor**.

   *By not immediately applying, we put the policy into learning mode, allowing us to identify any other connections that you may want to include in the policy.*

Assigning Category Values
+++++++++++++++++++++++++

*Before we see any traffic within the policy, we will need to apply the previously created categories to the VMs provisioned from the POC-App Blueprint. Alternatively, these could have been assigned as part of the Calm Blueprint.*

#. In **Prism Central**, select :fa:`bars` **> Virtual Infrastructure > VMs**.

#. Using the checkboxes, select the **MYSQL-\*** and **webserver-\*** VMs associated with the application click **Actions > Manage Categories**.

#. Specify **AppType:POCApp** in the search bar and click **Save** icon to bulk assign the category to all VMs.

#. Select ONLY the **webserver-\*** VM, select **Actions > Manage Categories**, specify the **AppTier:Web** category and click **Save**.

#. Repeat the previous step to assign **AppTier:DB** to your **MYSQL-\*** VM.

Monitoring and Applying a Security Policy
+++++++++++++++++++++++++++++++++++++++++

Before applying the Flow policy, you will ensure the POC-App application is working as expected.

Testing the Application
.......................

#. From **Prism Central > Virtual Infrastructure > VMs**, note the IP addresses of your **MYSQL-\*** and **webserver-\*** VMs.

#. Open a browser and access \http://*WebServer-VM-IP*/.

#. Verify that the application loads and you can browse the list of stores and products.

   .. figure:: images/12.png

#. Launch the console for your **WinServer** VM.

   *We should expect that the VM should be able to access both the database and webserver VMs on any port, currently. Also, recall that we previously assigned an Environment category to this VM.*

#. Within your **WinServer** VM, open **Command Prompt** and run ``ping -t MYSQL-VM-IP`` to verify connectivity between the client and database. Leave the ping running.

#. Open a second **Command Prompt** and run ``ping -t node-VM-IP`` to verify connectivity between the client and web server. Leave the ping running.

   .. figure:: images/13.png

Using Flow Visualization
........................

#. Return to **Prism Central** and select :fa:`bars` **> Virtual Infrastructure > Policies > Security Policies > POC-App**.

#. Verify that your **WinServer** VM appears as an inbound source.

   *The source and line appear in yellow to indicate that traffic has been detected from your client VM.*

   .. figure:: images/14.png

   Are there any other detected outbound traffic flows? Hover over these connections and determine what ports are in use.

#. Click **Update** to edit the policy.

#. Click **Next** and wait for the detected traffic flows to populate.

#. Mouse over the **WinServer** source that connects to **AppTier:Web** and click **Accept > OK** to add the rule to your policy. You can also click on the flow line to see what traffic has been discovered.

   *Using this approach it's easy to model what connections an application may depend on, and include them in your microsegmentation policy.*

   .. figure:: images/15.png

#. Click **Next > Save and Monitor** to update the policy.

Applying Flow Policies
......................

In order to enforce the policy you have defined, the policy must be applied.

#. Select **POC-App** and click **Actions > Apply**.

   .. figure:: images/16.png

#. Type **APPLY** in the confirmation dialogue and click **OK** to begin blocking traffic.

#. Return to the **WinServer** VM console.

   *As expected, we now see the ping to the database VM blocked, whereas the ping to the Web tier continues as we added the rule for this specific VM. If we try pinging the Web tier from another source, this should be blocked as well.*

#. Verify that the Windows Client VM can still access the POC-App using the web browser. Add a new store or product to demonstrate the web tier is still able to communicate with the MySQL database.

#. (Optional) In **Calm > Applications > POC-App-1 > Services**, select **WebServer** and click **Open Terminal** to access an HTML5 SSH session to the WebServer VM. Attempt to SSH to your database VM IP, the request should time out. If you return to the **Security Policy** you should see this traffic discovered within the app, and blocked.

   .. figure:: images/17.png

*As demonstrated, Flow makes it easy to model policies using visualization, and streamlines bulk application through the use of categories.*
