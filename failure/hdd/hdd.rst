.. _hdd:

HDD Failure
+++++++++++

.. note::

   This section describes a method to simulate a HDD failure that, while it works correctly using our testing, is not recommended by engineering. There will be a future version to simulate this using another method. It is recommended that you perform the steps within this section only if the POC absolutely requires this. These instructions will guide you with how to properly identify a disk with certainty, to simulate its failure, and add it back to the cluster once complete. However, be aware that you are using commands that if not entered correctly, could negatively impact your POC, and require involving support to rectify the issue.

**CREATE A VIDEO STEPPING THROUGH THIS BECAUSE THERE COULD BE DRAGONS HERE?**

In this section, we will be simulating a Hard Disk Drive (HDD) failure by executing a command that will simulate a degredation event for a hard disk that would normally happens over time under real world conditions. This is preferable to performing a "drive pull" test, as that is a very unlikely scenario. What is more likely, is a HDD to develop bad sectors or similar issues gradually over time, and as protecting customer data is vital, any infrastructure must handle this gracefully, and without interruption or loss.

This test demonstrates the ability of Nutanix's AOS to immediately begin rebuilding additional copies of data on the surviving drives, as a high priority event, utilizing all nodes in the cluster to aid in that rebuild. This results in a restoration to full redundancy in a very short amount of time. Internal tests show that using several generation old equipment, Nutanix can rebuild 5TB worth of data within 45 minutes. This differs between other platforms in two major ways. One, we don't have a default wait period before rebuilding data, and two, Nutanix doesn't operate in a paired fashion, so we can take advantage of the distributed nature and combined performance of an entire Nutanix cluster to rebuild missing data quickly. In a nutshell, the rebuild is both very fast and the workload per node is minimized to avoid bottlenecks and to reduce the impact to running workload. Data rebuild time decreases with each additional node added to the cluster, as a result of our MapReduce Framework which leverages the full power of the cluster to perform these types of activities concurrently.

   - BASIC: Create two VMs, one on the host that contains the HDD that the simulated failure will be performed on, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the HDD failure commands, and observe that there are no lost pings. Creating VMs is oulined in :ref:`vmmanage`

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload. Setup of X-Ray is oulined in :ref:`xray`. Instructions for running the OLTP Simulator can be found in :ref:`xray3`.

.. note::

   If you choose to run X-Ray on the same cluster you are performing failures on, it is recommended to avoid simulating the HDD failure on the same host that is running X-Ray.

#. Identify the host you are planning to simulate the HDD drive failure on.

#. Within Prism, choose **Hardware** from the dropdown. Click on **Diagram**, and then click on the selected host containing the HDD you'll be using for testing.

#. Click through all the disks associated with the host, and observe the *Disk Details* window in the lower left. Identify a drive that lists **HDD** as *Storage Tier*, optionally one with the lowest amount of disk usage, to reduce the time required to remove the drive. Lastly, make note of the drive's serial number. This will aid in identifying the drive from the CLI.

   .. figure:: images/hdd0.png

#. Click on the host, then scroll down. On the left-hand side, you will see a summary screen for the host itself, similar to the below. Use this to identify how many and what type (SSD or HDD) of drives the host contains.

   .. figure:: images/hdd1.png

#. Click on **Table** and note the IP address for the CVM that corresponds to the host you are testing on.

   .. figure:: images/hdd2.png

#. SSH to the CVM.

#. Run the command ``disk_operator list_usable_disks``. This will output similar to the below:

   .. figure:: images/hdd3.png

#. Run the command ``sudo parted /dev/sdX p`` replacing X with the letter that corresponds to the disks contained within your host. This command will only display information about the drive, and no changes are made at this stage. We are using this command to confirm which drives are SSDs and which are HDDs. In our example, we ran ``sudo parted /dev/sda p`` which identified an SSD - the only one in this host, since this is a single SSD configuration. Recommend you repeat this command, substituting the letter corresponding to each drive in your host.

   .. figure:: images/hdd4.png
      :align: left

      This is an example of an SSD

   .. figure:: images/hdd5.png
      :align: right

      This is an example of a HDD

#. Now that we have identified our HDD that we wish to use in our failure test, we can run ``disk_operator mark_disks_unusable /dev/sdX`` where X corresponds to the ID of the identified disk. In our example, we ran ``disk_operator mark_disks_unusable /dev/sdb``, and the output is below. **You will repeat this command until you observe a failure within Prism**. This is required to trigger Curator to mark the drive as failed.

   .. figure:: images/hdd6.png

#. Within Prism, observe that the disk is in the process of being removed, and shows a failure state. Make note of the disk serial number at this time.

   .. figure:: images/hdd7.png

#. Within the SSH session, run the command ``links http://0:2010``. You will be presented with the *Rebuild Estimator* interface similar to the below.

   .. figure:: images/hdd7a.png

#. Hit **Enter** on the *Curator Master* IP address.

#. Scroll down, and highlight **Rebuild Info**. Hit **Enter**. You will be presented with a screen similar to the below, which displays the estimated time remaining to rebuild the data from the removed disk.

   .. figure:: images/hdd7b.png

      To refresh the screen hit CTRL+R

#. Once the disk has been successfully removed, hit **CTRL+C** to exit the *Rebuild Estimator*.

#. Enable hidden commands in ncli by running ``ncli -h=true``.

#. Run the command ``disk list-tombstone-entries`` to show the tombstone list.

#. Observe that the disk you marked unusable is now in the tombstone list.

   .. figure:: images/hdd8.png

      Sample output of all commands

#. Run the command ``edit-hades``. This will open the text editor, enabling you to remove the necessary entries to bring the disk back online. It is recommended to take a screen shot to document the existing settings before making changes.

#. Hit **Insert** to begin editing. Remove anything with the main heading **is_bad** or **disk_diagnostics**, including anything within those sections, as shown below. Once complete, hit **ESC** to stop editing, followed by **:wq** and **Enter** to exit the file editor.

   .. figure:: images/hdd9.png
      :align: left

      Before

   .. figure:: images/hdd10.png
      :align: right

      After

#. You may now exit your SSH session.

#. Run the command ``genesis restart``. This will refresh Prism, and you will now see that the disk is available to add and repartition.

#. Return to Prism, select the disk, and choose **+ Repartition and Add > Yes**.

   .. figure:: images/hdd11.png

#. The previously removed disk will now be reincorporated into the cluster, and perform as normal. Additionally, demonstrate the result of either the **BASIC** or **RECOMMENDED** scenarios.
