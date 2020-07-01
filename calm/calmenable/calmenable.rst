.. _calmenable:

------------------
Starting with Calm
------------------

In this module you will enable the Calm service within Prism Central and create a Project that can be used for additional Calm POC modules.

**Expected Module Duration:** 10 minutes

**Covered Test IDs:** N/A

Enabling Calm
+++++++++++++

*Calm provides powerful automation for application deployment and management operations, defining multi-tiered apps into Blueprints capable of being provisioned to a variety of different environments, or clouds, including AHV, ESXi, AWS, Azure, and GCP. Similar to other Nutanix services, Calm is tightly integrated into Prism Central *

**FEEDBACK** - Could probably use more exposition here.

#. Select :fa:`bars` **> Services > Calm**.

#. Click **Enable App Orchestration (Calm)**.

   .. figure:: images/0.png

#. Click **Enable App Management**.

   .. figure:: images/1.png

   *By default, Calm will be deployed with a number of built-in Blueprints that you can use to deploy common apps, including application stacks like LAMP, CI/CD tools like Jenkins, configuration management tools like Chef and Puppet, open source databases, and monitoring and logging solutions.*

#. Monitor the **Enable App Management** operation in **Tasks**. This should take ~3 minutes to complete.

   .. figure:: images/2.png

   *As mentioned in the UI, enabling Calm will result in an additional 4GB of RAM being assigned to the Prism Central VM, which is what is occurring in the VM update operation alongside enabling Calm. This is completely non-disruptive, and in just a couple minutes we'll have added Calm functionality without setting up new VMs, installing software, or accessing additional consoles.*

#. When the task completes, return to :fa:`bars` **> Services > Calm**.

Creating A Project
++++++++++++++++++

*In order for non-infrastructure administrators to access Calm, allowing them to create or manage applications, users or groups must first be assigned to a Project, which acts as a logical container to define user roles, infrastructure resources, and resource quotas. Projects define a set users with a common set of requirements or a common structure and function, such as a team of engineers collaborating on an application.*

*For the POC, we'll create a new POC project for any blueprints we build and deploy.*

#. In **Calm**, select **Projects** from the left-hand menu and click **+ Create Project**.

   .. figure:: images/3.png

   .. note::

     Mousing over an icon will display its title.

#. Fill out the following fields:

   .. note::

      Adding the User/Group mappings before adding the Infrastructure can cause adding the Infrastructure to fail. To avoid this, add the Infrastructure before the User/Group mappings.

   - **Project Name** - POC-Project

   - Under **Infrastructure**, select **Select Provider > Nutanix**

   - Click **Select Clusters & Subnets**

   - Select your cluster

   - Under **Subnets**, select **Primary** and click **Confirm**

   - Mark **Primary** as the default network by clicking the :fa:`star`

   - Under **Users, Groups, and Roles**, select **+ User**

      - **Name** - SSP Developers
      - **Role** - Developers
      - **Action** - Save

   - Select **+ User**

      - **Name** - SSP Consumers
      - **Role** - Consumers
      - **Action** - Save

   - Under **Quotas**, specify

      - **vCPUs** - 100
      - **Storage** - <Leave Blank>
      - **Memory** - 200

   .. figure:: images/4.png

#. Click **Save & Configure Environment**.

   ..note::

      This will redirect you to the Envrionments page, but there is nothing needed to configure here immediately. Environment definitions are required when launching Blueprints directly from the Marketplace.

   *That's it, now we're ready to start building our own Blueprints or importing and deploying existing applications.*

Enabling Showback
+++++++++++++++++

*Calm can also provide cost computation showback for AHV and VMware environments, enabling this feature is just another One Click operation and will help provide you with analyzing your IT spend and predicting future costs.*

#. In **Calm**, select **Settings** from the left-hand menu and toggle **Enable Showback**.

   .. figure:: images/5.png

#. If desired, update the vCPU, memory, and storage resource usage costs and click **Enable Showback**. This task should complete in < 1 minute.

   .. figure:: images/6.png

   *Typically you would want these values to include... ?*
