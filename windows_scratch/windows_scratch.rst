.. _windows_scratch:

------------------------------
Deploying Windows From Scratch
------------------------------

This guide assumes there are business or technical reasons that prevent the use of a pre-configured Windows disk image for your POC. This method, while more time consuming, starts with a base of the evaluation Windows ISO image from Microsoft. Depending on your situation, the ISO or disk image may be something the customer requires they provide, due to internal security policies, or personal preference. Otherwise, using the Nutanix or customer provided pre-installed Windows image may be preferable for your needs.

Before you get started
++++++++++++++++++++++

There are a few pieces of software that are required to be able to accomplish this section:

   - Windows image (2016 or 2019)
   - VirtIO ISO
   - (Optional) Chrome or Firefox

VirtIO
......

Nutanix Virt IO is a collection of drivers for paravirtual devices that enhance the stability and performance of virtual machines on a paravirtualized hypervisor (Balloon, Ethernet, and SCSI pass through drivers). This is required to install Microsoft Windows on Nutanix AHV.

#. Within the `AHV section of the Downloads page on the Nutanix Portal <https://portal.nutanix.com/page/downloads?product=ahv>`_, choose **VirtIO** from the dropdown, and then either click the **Download** button associated with the latest version of *Nutanix VirtIO for Windows (iso)*, or the :fa:`ellipsis-v` next to it, and choose **Copy Download Link**.

   .. figure:: images/4.png

#. Log on to Prism Element, and choose **Settings** from the dropdown.

#. Click on **Image Configuration**.

#. Click on :fa:`plus` **Upload Image**.

#. Enter the following with the listed fields:

   - **Name** - VirtIO

   - **Image Type** - ISO



   **Choose one of the following**:

   **EITHER**

      - Within *Image Source*, click **Upload a file > Choose File**. Browse to the ISO file for VirtIO, select it, and click **Open**.

         .. figure:: images/6.png

   **OR**

      - Within *Image Source*, click :fa:`dot-circle` **From URL**, and paste the download link you previously copied from the Nutanix Portal.

         .. figure:: images/5.png

         .. note::

            Verify that the *Image Type* is **ISO** before clicking **Save**. After pasting the download link, as it may change to **Disk**.

#. Click **Save**.

Installing Windows
..................

You may use a customer-provided image, or obtain the Windows Server 2016 and Windows Server 2019 ISO images directly from the `Microsoft Evaluation Center <https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server>`_.

.. note::

   These evaluation versions have a 180-day expiration period.

Installing Windows 2016 from ISO
++++++++++++++++++++++++++++++++

#. Log on to Prism Element, and choose **Settings** from the dropdown.

#. Click on **Image Configuration** from the left-hand side.

#. Click on :fa:`plus` **Upload Image**.

#. Enter the following with the listed fields:

   - **Name** - Windows2016ISO

   - **Image Type** - ISO

#. Within *Image Source*, click **Upload a file > Choose File**. Browse to the ISO file for Windows 2016, select it, and click **Open**.

#. Click **Save**.

   .. figure:: images/1.png

#. Choose **VM** from the dropdown menu.

#. Click on :fa:`plus` **Create VM**.

#. Enter the following with the listed fields:

   - **Name** - Windows2016

   - **vCPU(s)** - 2

   - **Memory** - 4

#. Within the *Disks* section, click on the pencil next to the *CD-ROM* entry. Within the *Operation* dropdown, choose **Clone from Image Service**. Within the *Image* dropdown, choose **Windows2016ISO**. Click **Update**.

#. Within the *Add Disk* dialog box, enter **100** for *Size (GiB)*, and click **Add**.

   .. figure:: images/2.png

Click on :fa:`plus` **Add New Disk**. Change the *Type* to **CD-ROM**. Change the *Operation* to **Clone from Image Service**. Change *Image* to **VirtIO**. Click **Add**.

#. Click :fa:`plus` **Add New NIC**. Ensure *Primary* is selected, and click **Add**. and click **Add**.

#. Click **Save**.

#. Within Prism Element, right click on your *Windows2016* VM, and choose **Power on**.

#. Wait a few moments, then right click on your *Windows2016* VM once more, and choose **Launch Console**.

#. Click **Next** on the initial *Windows Setup* screen, then click **Install Now**.

#. Choose **Windows Server 2016 Datacenter Evaluation (Desktop Experience)**, and click **Next**.

#. Click the **I accept the license terms** box, and then click **Next**.

#. Choose **Custom**.

#. Click **Load Driver > Browse**.

#. Choose the CD-ROM drive that has the VirtIO ISO loaded. Navigate to the **Windows Server 2016**, and then to the **amd64** folder. Click **OK**.

   .. figure:: images/7.png

#. Highlight all entries, and click **Next**.

#. The installation disk should now be displayed. Click **Next** to begin the Windows installation process. This should take approximately 5 minutes, after which the VM will reboot automatically.

#. At the *Customize Settings* screen, set the Administrator password as **nutanix/4u** for ease of use, and then log in as Administrator.

#. Recommend installing all Windows updates. This can be very time consuming, so you may wish to move onto other tasks/demos while you monitor this from time to time and restart when prompted.

#. Windows Updates may require multiple restarts to apply all updates. Every time you are prompted to restart, do so, and check for Windows Updates once again. Proceed only when there are no other updates to apply.

#. Ensure the date/time are correct. If not, right click on the time in the lower right-hand corner, and choose **Adjust Date/Time**. Make the required changes, and close the window once complete.

#. Execute **C:\\Windows\\System32\\Sysprep\\Sysprep.exe**. Check the box for **Generalize**, and from the *Shutdown Options* dropdown, choose **Shutdown**.

   .. figure:: images/8.png
      :align: center

   .. note::

      Per Microsoft: "When a system is generalized, specific configuration data for a given installation of Windows is removed. For example, during the generalize configuration pass, the unique security ID (SID) and other hardware-specific settings are removed from the image."

#. Within Prism Element, right click on your *Windows2016* VM, and choose **Update**.

#. Remove one CD-ROM drive by clicking the :fa:`times` icon.

#. Click **Save**.

This image can now serve as the Windows Server 2016 base image during the POC process.

Installing Windows 2019 from ISO
++++++++++++++++++++++++++++++++

#. Log on to Prism Element, and choose **Settings** from the dropdown.

#. Click on **Image Configuration** from the left-hand side.

#. Click on :fa:`plus` **Upload Image**.

#. Enter the following with the listed fields:

   - **Name** - Windows2019ISO

   - **Image Type** - ISO

#. Within *Image Source*, click :fa:`dot-circle` **Upload a file > Choose File**. Browse to the ISO file for Windows 2019, select it, and click **Open**.

#. Click **Save**.

   .. figure:: images/1a.png

#. Choose **VM** from the dropdown menu.

#. Click on :fa:`plus` **Create VM**.

#. Enter the following with the listed fields:

   - **Name** - Windows2019

   - **vCPU(s)** - 2

   - **Memory** - 4

#. Within the *Disks* section, click on the pencil next to the *CD-ROM* entry. Within the *Operation* dropdown, choose **Clone from Image Service**. Within the *Image* dropdown, choose **Windows2019ISO**. Click **Update**.

#. Click on :fa:`plus` **Add New Disk**. Within the *Add Disk* dialog box, enter **100** for *Size (GiB)*, and click **Add**.

   .. figure:: images/2.png

#. Click on :fa:`plus` **Add New Disk**. Change the *Type* to **CD-ROM**. Change the *Operation* to **Clone from Image Service**. Change *Image* to **VirtIO**. Click **Add**.

#. Click :fa:`plus` **Add New NIC**. Ensure *Primary* is selected, and click **Add**.

#. Click **Save**.

#. Right click on your *Windows2019* VM, and choose **Power on**.

#. Wait a few moments, then right click on your *Windows2019* VM, and choose **Launch Console**.

#. Click **Next** on the initial *Windows Setup* screen, then click **Install Now**.

#. Choose **Windows 2019 Datacenter Evaluation (Desktop Experience)**, and click **Next**.

#. Click the **I accept the license terms** box, and then click **Next**.

#. Choose **Custom**.

#. Click **Load Driver > Browse**.

#. Choose the CD-ROM drive that has the VirtIO ISO loaded. Navigate to the **Windows Server 2019**, and then to the **amd64** folder. Click **OK**.

   .. figure:: images/7a.png

#. Highlight all entries, and click **Next**.

#. The installation disk should now be displayed. Click **Next** to begin the Windows installation process. This should take approximately 5 minutes, after which the VM will reboot automatically.

#. At the *Customize Settings* screen, set the Administrator password as **nutanix/4u** for ease of use, and then log in as Administrator.

#. Recommend installing all Windows updates. This can be very time consuming, so you may wish to move onto other tasks/demos while you monitor this from time to time and restart when prompted.

#. Windows Updates may require multiple restarts to apply all updates. Every time you are prompted to restart, do so, and check for Windows Updates once again. Proceed only when there are no other updates to apply.

#. Execute **C:\\Windows\\System32\\Sysprep\\Sysprep.exe**. Check the box for **Generalize**, and from the *Shutdown Options* dropdown, choose **Shutdown**.

   .. figure:: images/8.png
      :align: center

   .. note::

      Per Microsoft: "When a system is generalized, specific configuration data for a given installation of Windows is removed. For example, during the generalize configuration pass, the unique security ID (SID) and other hardware-specific settings are removed from the image."

#. Within Prism Element, right click on your *Windows2019* VM, and choose **Update**.

#. Remove one CD-ROM drive by clicking the :fa:`times` icon.

#. Click **Save**.

This image can now serve as the Windows Server 2019 base image during the POC process.
