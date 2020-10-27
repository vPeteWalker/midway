.. _staging:

-------------------------------
Automated Initial Configuration
-------------------------------

*The estimated time to complete staging a cluster is ~60 minutes.*

This exercise will show you how to stage your Hosted POC cluster so you can skip the **Initial Configuration** exercises and proceed to any of the additional SE POC Guide sections.

Requirements
++++++++++++

While the SE POC Guide can be used in conjunction with any Nutanix cluster and environment, the automated staging is only supported on HPOC clusters. To proceed you will need a HPOC reservation.

Refer to your automation@nutanix.com **Reservation Confirmation** e-mail, similar to the example below, and note the **CVM IPs** and **Password**.

.. figure:: images/1.png

Running the Staging Scripts
+++++++++++++++++++++++++++

#. Use Terminal or PuTTY to SSH into a Controller VM (CVM), using the **Password** provided in your **Reservation Confirmation** e-mail.

   .. code-block:: bash

    ssh nutanix@<CVM-IP>

#. Now we need to call the staging script. Run the following command to download and call the **bootstrap.sh** file.

   .. code-block:: bash

    curl --remote-name --location https://raw.githubusercontent.com/nutanixworkshops/stageworkshop/master/bootstrap.sh && sh ${_##*/}

#. Next you will be prompted to enter the Clusters **Admin User**, **Admin Password**, and **Admin Email**. The **Admin User** and **Admin Password** map to the **Prism UI Credentials** in the email you received, and the **Admin Email** is your email address.

   .. code-block::

      Note: Hit [Return] to use the default answer inside brackets.

      Optional: What is this cluster's admin username? [admin] admin

      Note: Password will not be displayed.
      REQUIRED: What is this PHX-POC055 cluster's admin password?
      CONFIRM:               PHX-POC055 cluster's admin password?

      Note: @nutanix.com will be added if domain omitted.
      REQUIRED: Email address for cluster admin?  <INSERTYOUREMAILHERE>@nutanix.com

#. This is the same staging script shared by many Nutanix Bootcamp offerings. When prompted, enter the number that corresponds to the **SE POC Guide (AHV)** staging.

   .. note::

    These options change frequently, ensure you have made the proper selection before executing the script.

   .. figure:: images/2.png

#. Enter ``y`` and press **Return** to begin staging.

#. To monitor the progress of the staging on **Prism Element**, tail the *xyz_bootcamp.log* file.

   .. code-block:: bash

    tail -f *bootcamp.log

#. You will see it update and install several things:

- sshpass & jq
- AutoAD
- Role Mapping
- Configure VM Networks & Storage Container
- Download and Install Prism Central (this takes roughly 17 minutes)
- Register Prism Element to Prism Central

#. After PC has been deployed you can monitor its status. SSH to the Prism Central VM (10.XX.YY.39) so you can tail the *xyz_bootcamp.log* file there and follow along.

   .. code-block:: bash

    ssh nutanix@<PC IP>

#. The password with be nutanix/4u since this is a default install of Prism Central.

#. Now tail the *xyz_bootcamp.log* file on the Prism Central VM.

   .. code-block:: bash

    tail -f *bootcamp.log

#. Once Images have completed uploading in Prism Central, your environment is ready to be used for POC Guide exercises.

See :ref:`scenario` for more details on presenting to your manager for Basic POC validation.
