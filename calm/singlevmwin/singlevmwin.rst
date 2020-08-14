.. _singlevmwin:

-------------------
Calm IaaS (Windows)
-------------------

In this module you'll create a Single VM Blueprint in Calm, using Windows Server 2016, then deploy and manage the application. Additionally you'll publish the Blueprint to your Marketplace, so assigned users can deploy and manage their own instances on a self-service basis.

**Pre-requisites:** Completion of :ref:`calmenable`

**Expected Module Duration:** 60 minutes

**Covered Test IDs:** N/A

*Infrastructure-as-a-Service (IaaS) is defined as the ability to quickly provide compute resources, on-demand through a self service portal. While many customers utilize Nutanix Calm to orchestrate complex, multi-tiered applications, a significant portion of customers also utilize Calm to provide basic IaaS for their end users.*

Creating a Single VM Blueprint
++++++++++++++++++++++++++++++

.. note::

   You can optionally complete the following workflow logged in as a **Developer** role account to feature the different roles supported by Calm.

*A Blueprint is the framework for every application or piece of infrastructure that you model by using Nutanix Calm.  While complex, multi-tiered applications utilize the "Multi VM/Pod Blueprint", the streamlined interface of the "Single VM Blueprint" is conducive for IaaS use cases. You can model each type of infrastructure your company utilizes (for instance Windows, CentOS, and/or Ubuntu) in a Single VM Blueprint, and end users can repeatedly launch the Blueprint to create infrastructure on demand. The resulting infrastructure (which is still referred to as an "application"), can then be managed throughout its entire lifecycle within Calm, including managing Nutanix Guest Tools (NGT), modifying resources, snapshotting, and cloning.*

#. In **Prism Central**, select :fa:`bars` **> Services > Calm**.

#. Select |Blueprints| **Blueprints** in the left-hand toolbar to view and manage Calm Blueprints.

#. Click **+ Create Blueprint > Single VM Blueprint**.

#. Fill out the following fields:

   - **Name** - WinServer-IaaS
   - **Description** - (Optional)
   - **Project** - POC-Project

   .. figure:: images/0.png

#. Click **VM Details**.

#. Note the following fields on the **VM Details** page:

   - **Name** - The internal-to-Calm name of the VM.  This can be left as the default.
   - **Cloud** - The cloud on which the VM will be deployed. This should be left as **Nutanix**.
   - **Operating System** - Windows

   .. figure:: images/1.png

#. Click **VM Configuration**.

#. Next you'll configure key settings for the VM:

   - **General Configuration**

      - **VM Name** - WinServer-@@{calm_time}@@

      *This is the name of the VM as it will appear to the hypervisor.*

      - **vCPUs** - 4
      - Mark the **vCPUs** field as **Runtime** by clicking the "running man"

      *Enabling this allows the settings to be modified at the time of deployment.*

      - **Cores per vCPU** - 1
      - **Memory (GiB)** -  6
      - Mark **Memory (GiB)** as **Runtime**

      .. figure:: images/2.png

   - **Guest Customization** - *Guest customization allows for the modification of certain settings at boot.  Linux OSes use "Cloud Init", while Windows OSes use "Sysprep".*

      - Select **Guest Customization**
      - **Type** - Sysprep
      - **Install Type** - Prepared
      - Do NOT select **Join a Domain**
      - **Script:**

      .. literalinclude:: sysprep.xml
         :language: xml

      .. figure:: images/3.png

   *Take note of the "@@{vm_password}@@" text.  In Calm the "@@{" and "}@@" characters represent a macro.  At runtime, Calm will automatically "patch" or substitute in the proper value(s) when it encounters a macro.  A macro could represent a system defined value, a VM property, or (as it does in this case) a runtime variable.  Later in this lab we'll create a runtime variable with the name "vm_password".*

   - **Disks** - *A disk is the storage of the VM or infrastructure that we're deploying.  It could be based on a pre-existing image (as it will in our case), or it could be based on a blank disk to enable the VM to consume additional storage.  For instance, a Microsoft SQL server may need its base OS disk, a separate SQL Server binary disk, separate database data file disks, separate TempDB disks, and a separate logging disk.  In our case we're going to have a single disk, based on a pre-existing image.*

      - **Type** - DISK
      - **Bus Type** - SCSI
      - **Operation** - Clone from Image Service
      - **Image** - Windows2016.qcow2
      - **Bootable** - Enabled (A minimum of 1 disk must always be bootable)

      .. figure:: images/4.png

   - **Boot Configuration** - Legacy BIOS

   - **vGPUs** - N/A

   - **Categories** - Mark **Runtime** to allow users to configure categories at deployment time that could be used to apply a variety of policies (such as your Production VM Protection Policy tied to **Environment:Production**, try it!).

   .. figure:: images/5.png

   - **NICs** -

      - Click :fa:`plus-circle` to add a NIC
      - **NIC 1** - Primary
      - **Private IP** - Dynamic

   - **Serial Ports** - Leave the default of **None**.

   .. figure:: images/6.png

#. Click **Save**.

   It is expected to have a single error about an incorrect macro due to our Guest Customization containing **vm_password**.  If you have additional errors, resolve them before continuing to the next section.

   .. figure:: images/7.png

Defining Variables
++++++++++++++++++

Variables allow extensibility of Blueprints, meaning a single Blueprint can be used for multiple purposes and environments depending on the configuration of its variables.  Variables can either be static values saved as part of the Blueprint or they can be specified at **Runtime** (when the Blueprint is launched), as they will in this case.

In a Single VM Blueprint, variables can be accessed by clicking the **App variables** button near the top.  By default, variables are stored as a **String**, however additional **Data Types** (Integer, Multi-line String, Date, Time, and Date Time) are all possible.  Any of these data types can be optionally set as **Secret**, which will mask its value and is ideal for variables such as passwords.  There are also more advanced **Input Types** (versus the default **Simple**), however these are outside the scope of this lab.

Variables can be used in scripts executed against objects using the **@@{variable_name}@@** construct (called a macro). Calm will expand and replace the variable with the appropriate value before sending to the VM.

#. Click the **App variables** button along the top pane to bring up the variables menu.

   .. figure:: images/8.png

#. Click **+ Add Variable** and fill out the following fields:

   - Click the **Running Man** icon to mark this variable as **Runtime**
   - **Name** - vm_password

   *This name must exactly match (including case) the value within our macro from our Guest Customization script, otherwise we'll continue to get an error when we save.*

   - **Data Type** - String

   *Calm supports additional built-in data types including integers, date, time, date/time, and multi-line strings.*

   - **Value** - Leave blank, as we want the end users to specify their own VM password

   - Select the **Secret** checkbox, as we do not want this password to be visible.
   - Click **Show Additional Options**
   - **Label** - Leave blank
   - **Description** - Create a password for the user "Administrator".
   - Select **Mark this variable mandatory**

   *This will ensure that the end user enters a password, which is required since we did not provide default value.*

   .. note::

      Validating variable inputs with regular expressions `is awesome <https://xkcd.com/208/>`_.

   .. figure:: images/9.png


#. Click **Done**.

#. Click **Save**.

   It is expected to receive a **Warning** stating that the value of our secret variable is empty.  This is needed as there is not way to determine the value of a secret once you save the Blueprint, so this warning alerts a user in the event they accidentally left it blank.  Warnings do not prevent users from launching or publishing the Blueprint.  If you receive any other warning, or a red error, please resolve the issue before continuing on.

   .. figure:: images/10.png

Launching the Blueprint
+++++++++++++++++++++++

Now that our Blueprint is complete, take note of the options to the right of the **Save** button:

- **Publish** - This allows us to request to publish the Blueprint into the Marketplace.  Blueprints have a 1:1 mapping to a Project, meaning only other users who are members of our own Project will have the ability to launch this Blueprint.  Publishing Blueprints to the Marketplace allows an administrator to assign any number of Projects to the Marketplace Blueprint, which enables self service for any number of end users desired.

- **Download** - This option downloads the Blueprint in a JSON format, which can be checked into source control, or uploaded into another Calm instance.

- **Launch** - This launches our Blueprint and deploys our application and/or infrastructure.

#. Click **Launch**, and enter the following:

    - **Name of the Application** - WS-IaaS-1
    - **vm_password** - Nutanix/4u

    .. note::

      The unattend.xml used for Guest Customization uses the built-in Calm macro **@@{calm_application_name}@@** as the hostname for the VM being created. If your **Name of Application** exceeds 15 characters, or contains characters other than letters, numbers, and hyphens, your Sysprep will fail.

      If you wanted to further harden this process, you could create a custom runtime variable for VM hostname, and use a regular expression (e.g. ^(?![0-9]{1,15}$)[a-zA-Z0-9-]{1,15}$) to validate the specified value is a valid Windows hostname.

   .. figure:: images/11.png

#. Click **Create** to begin deployment.

Managing your Application
+++++++++++++++++++++++++

#. Wait several minutes for your application to change from a **Provisioning** state to a **Running** state.  If it instead changes to an **Error** state, navigate to the **Audit** tab, and expand the **Create** action to start troubleshooting your issue.

#. Once your application is in a **Running** state, navigate around the five tabs in the UI:

   .. figure:: images/12.png

   - The **Overview** tab gives you information about any variables specified, the cost incurred (if Showback has been enabled in Calm Settings), an application summary, and a VM summary.
   - The **Manage** tab allows you to run actions against the application / infrastructure.  This includes basic lifecycle (start, restart, stop, delete), NGT management (install, manage, uninstall), and App Update, which allows for editing of basic VM resources.
   - The **Metrics** tab gives in depth information about CPU, Memory, Storage, and Network utilization.
   - The **Recovery Points** tab lists the history of VM Snapshots, and allows the user to restore the VM to any of these points.
   - The **Audit** tab shows every action run against the application, the time and user that ran a given action, and in depth information on the results of that action, including script output.

#. Next, view the common VM tasks available in the upper-right corner of the UI:

   .. figure:: images/13.png

   - The **Clone** button allows a user to duplicate the existing application into a new app that is manageable separately from the current application.  For a brand new application, this is equivalent to launching the Blueprint again.  However, a user may have spent significant time customizing the existing application to suit their specific needs, and would like these changes to be present on the new app.
   - The **Snapshot** button creates a new recovery point of the VM, which allows a user to restore the VM.
   - The **Launch Console** button opens a console window to the VM.
   - The **Update** button allows for the end user to modify basic VM settings (this is equivalent to the **Manage > App Update** action).
   - The **Delete** button deletes the underlying VM and the Calm Application (this is equivalent to the **Manage > App Delete** action).

   Now that we're familiar with the application page layout, let's modify our application by adding additional memory, but let's do it in a way that we can recover from in case something goes wrong.

#. Click the **Snapshot** button in the upper right, and fill out the following:

   - **Snapshot Name** - before-update-@@{calm_time}@@
   - **Snapshot Type** - Crash consistent

#. Click **Save**.

#. You're re-directed to the **Audit** tab.  Expand the **Snapshot Create** action to view the tasks of the snapshot.  Once complete, navigate to the **Recovery Points** tab, a validate that our new snapshot is listed.

   .. figure:: images/14.png

#. Click the **Launch Console** button in the upper right, and log in to your VM.

   - **Username** - Administrator
   - **Password** - Nutanix/4u (or whatever you defined as your **Runtime** password)

#. To view the current memory on Windows, open a **Command Prompt**, and run ``systeminfo | findstr Memory``.  Take note of the current memory allocated to your VM.

   .. figure:: images/15.png

#. Return to **WS-IaaS-1** in Calm, and click the **Update > Update VM Configuration**.

   .. figure:: images/16.png

#. Increase the **Memory (GiB)** field by 2 GiB and click **Update**.

   .. figure:: images/17.png

#. Validate that the memory has been increased by 2 GiB, and click **Confirm**.

   .. figure:: images/18.png

#. In the **Audit** tab of Calm, wait for the **App Update** action to complete.

#. Return to the **VM Console**, run ``systeminfo | findstr Memory``, and note that it has increased by 2 GiB.

   .. figure:: images/19.png

   .. note::

      If anything went wrong with the VM Update, navigate to the **Recovery Points** tab, click **Restore** on the **before-update** snapshot we took earlier, and click **Confirm** on the pop-up.

   *Common day 2 operations, like snapshotting, restoring, cloning, and updating the infrastructure can all be done by end users directly within Calm.*

Adding Blueprints to the Marketplace
++++++++++++++++++++++++++++++++++++

*At this point, users assigned to roles with permissions to deploy/edit/manage Blueprints and Applications within the POC-Project would be able to by logging in to Prism Central using their own credentials. In some cases, however, you may want to make a commonly used Blueprint available to users assigned across a number of Projects - such as the case with this IaaS Windows Server Blueprint.*

Publishing the Blueprint
........................

#. Select |Blueprints| **Blueprints** in the left hand toolbar to view and manage Calm Blueprints.

#. Click your **WinServer-IaaS** Blueprint.

#. Click the **Publish** button, and enter the following:

   - **Publish as a** - New Marketplace Blueprint
   - **Name** - Windows Server 2016
   - **Publish with secrets** - Disabled
   - **Initial Version** - 1.0.0
   - **Description** - (Optional)

   .. figure:: images/20.png

   *Blueprint versioning allows you to publish out multiple versions of a given app simultaneously, to support users who may have different application requirements, while maintaining a standard workflow.*

#. Click **Submit for Approval**.

   .. note::

     Publish with Secrets: By default, the secret values from the Blueprint are not preserved while publishing. As a result, during the launch of the marketplace item, the secret values will either be patched from the environment or the user will have to fill them in.

     Set this flag if you do not want this behaviour and you would rather the secret values are preserved as is. *Credential passwords/keys and secret variables are considered secret values. While publishing with secrets, these values will be encrypted.*

Approving Blueprints
....................

.. note::

   If you built and submitted your Blueprint using a **Developer** account in Prism Central, you'll need to log back in as an Administrator to approve the Blueprint.

*Built into Calm is an approval workflow, allowing Developers to submit Blueprints for Marketplace approval, allowing administrators or QA to evaluate the Blueprint before publishing.*

#. Select |marketplacemgr| **Marketplace Manager** in the left-hand toolbar to view and manage Marketplace Blueprints.

#. You will see the list of all Marketplace Blueprints, and their versions, listed. Select **Approval Pending** at the top of the page.

#. Click your **Windows Server 2016** Blueprint.

#. Review the available actions:

   - **Approve** - Approves the Blueprint for publication to the Marketplace.
   - **Reject** - Prevents  Blueprint from being launched or published in the Marketplace. The Blueprint will need to be submitted again after being rejected before it can be published.
   - **Delete** - Deletes the Blueprint submission to the Marketplace.
   - **Launch** - Launches the Blueprint as an application, similar to launching from the Blueprint Editor. This can be used to test the Blueprint deployment prior to publishing.

#. Review the available selections:

   - **Category** - Allows you to update the Category for the new Marletplace Blueprint.
   - **Projects Shared With** - Allows you to make the Marketplace Blueprint only available to a certain project.

#. Click **Approve**.

   .. figure:: images/21.png

#. Select **Marketplace Blueprints** at the top of the page, and enter your *Windows* in the search bar. Click the **Blueprint Name**.

   .. figure:: images/22.png

#. Under **Projects Shared With**, select your **POC-Project** and **default** projects and click **Apply**.

   *This is how Administrators can control which projects will have access to Marketplace Blueprints.*

#. Click **Publish** to complete making the Blueprint available through the Marketplace.

   .. figure:: images/22b.png

Configuring Project Environment
...............................

*To launch a Blueprint directly from the Marketplace, we need to define a set of defaults to ensure our Project has all of the requisite environment details to satisfy the Blueprint. These are configured on a per Cloud type basis, allowing a single Blueprint to potentially be deployed to multiple types of Clouds.*

#. Select |projects| **Projects** from the left-hand toolbar.

#. Select your **POC-Project**.

#. Select the **Environment** tab.

#. Under **Credential**, click :fa:`plus-circle` and enter the following:

   - **Credential Name** - Administrator
   - **Username** - Administrator
   - **Secret** - Password
   - **Password** - Nutanix/4u
   - Click the **Running Man** icon above Password box to mark this variable as **Runtime**.

   .. figure:: images/23.png

#. Under **VM Configuration > Nutanix** expand **Windows**, and enter the following:

   - **VM Name** - vm-@@{calm_array_index}@@-@@{calm_time}@@ (Default)
   - **vCPUs** - 4
   - **Cores per vCPU** - 1
   - **Memory** - 6GiB
   - **Image** - Windows2016.qcow2
   - **NICs** - Click :fa:`plus-circle`, then selecting **Primary** in the dropdown
   - **Private IP** - Dynamic
   - Select **Check log-in upon create**
   - **Credential** - Administrator (Defined Above)

   .. figure:: images/24.png

#. Click **Save**.

Launching your Blueprint from the Marketplace
+++++++++++++++++++++++++++++++++++++++++++++

#. Log in to Prism Central as one of your **Consumer** user accounts.

#. Select :fa:`bars` **> Services > Calm**, and click |marketplace| **Marketplace** in the left-hand toolbar.

#. Select your **Windows Server 2016** Blueprint and click **Launch**.

   .. figure:: images/25.png

#. Select the **POC-Project** from the drop-down menu and click **Launch**.

#. Provide a **Name** and **vm_password** and click **Create**.

   .. note::

      Name of the application should be 15 characters or less and only contain letters, numbers, and hyphens, as this is used in the unattend.xml as the ComputerName for the deployed VM.

#. Monitor the application deployment. Explore the **Calm** UI as a Consumer user and *note you do not have the ability to create/upload/edit new Blueprints. You do have the ability to launch Blueprints associated with the POC-Project. If you wanted to eliminate this ability in a production environment, you could create separate Projects for Blueprint development and testing, and only allow Operators and Consumers access to projects with published Blueprints.*

.. |marketplacemgr| image:: images/marketplacemanager.png
.. |marketplace| image:: images/marketplace.png
.. |Blueprints| image:: images/blueprints.png
.. |applications| image:: images/alueprints.png
.. |projects| image:: images/projects.png
