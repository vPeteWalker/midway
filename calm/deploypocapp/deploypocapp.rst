.. _deploypocapp:

------------------
Deploying POC App
------------------

In this module you will upload and deploy a multi-VM application from an existing Blueprint. It isn't necessarily intended to be focused on proving out Calm capabilities, but rather providing a real application, quickly, that can be used for the :ref:`flowappsec` module. To begin exploring Calm and creating your first Blueprint, see :ref:`singlevmwin`.

**Pre-requisites:** Completion of :ref:`calmenable`

**Expected Module Duration:** 20 minutes

**Covered Test IDs:** N/A

Staging A Blueprint
+++++++++++++++++++

*A Blueprint is the framework for every application that you model by using Nutanix Calm. Blueprints are templates that describe all the steps that are required to provision, configure, and execute tasks on the services and applications that are created. A Blueprint also defines the lifecycle of an application and its underlying infrastructure, starting from the creation of the application to the actions that are carried out on a application (updating software, scaling out, etc.) until the termination of the application.*

*You can use Blueprints to model applications of various complexities; from simply provisioning a single virtual machine to provisioning and managing a multi-node, multi-tier application.*

#. `Download the POC-App Blueprint by right-clicking here <https://raw.githubusercontent.com/nutanixworkshops/midway/master/calm/deploypocapp/POC-App.json>`_.

#. From **Prism Central > Calm**, select **Blueprints** from the left-hand menu and click **Upload Blueprint**.

   .. figure:: images/0.png

#. Select **POC-App.json**.

#. (Optional) Update your **Blueprint Name**.

   .. note::

      Even across different projects, Calm Blueprint names must be unique.

#. Select your Calm project and click **Upload**.

   *Before the Blueprint can be launched, we'll need to configure a few parameters specific to this environment. As every environment is different, Blueprints will not retain local network or disk image mappings, and by default will not export credentials, for security purposes. So we'll assign these now.*

#. Select the **WebServer** Service, and in the **VM** Configuration menu on the right, select **Primary** as the **NIC 1** network.

   .. figure:: images/1.png

#. By default, this Blueprint will download a CentOS image to deploy the VMs, requiring an internet connection. *If the cluster does not have Internet access (or it's slow)*, select the **CentOS.qcow2** Image previously uploaded under **Disk (1)**.

   .. figure:: images/2.png

#. Repeat the **NIC 1** (and if required, the **Disk (1) Image**) assignment(s) for the **MySQL** Service.

   *You'll also notice that Category values can be applied at the time of deployment with Calm, ensuring policies for things like Flow and data protection are applied throughout the lifecycle of your application.*

#. Click **Credentials** to define a private key used to authenticate to the CentOS VM that will be provisioned by the Blueprint.

   .. figure:: images/27b.png

#. Expand the **CENTOS** credential and use your preferred SSH key, or paste in the following value as the **SSH Private Key**:

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

   .. note::

      See `here <https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/create-with-openssh/>`_ for instructions on creating SSH keys using macOS or Linux, or `here <https://www.digitalocean.com/docs/droplets/how-to/add-ssh-keys/create-with-putty/>`_ for Windows.

#. Click **Save** and click **Back** once the Blueprint has completed saving.

Launching A Blueprint
+++++++++++++++++++++

This can be done directly logged in using your Prism Administrator account by skipping to **Step 3**, or alternatively you can demonstrate the role based access control available through Calm.

#. Log out of Prism Central and log in using an account mapped to the **Consumer** role.

   .. figure:: images/4.png

#. From **Prism Central > Calm**, select **Blueprints**.

   *As you can see, from the Consumer role we have significantly reduced permissions, with no ability to create new Blueprints or modify existing Blueprints. If we had multiple Projects created, I would only see Blueprints within the Projects to which I've been assigned.*

#. Open the **POC-App** Blueprint and click **Launch**.

   .. figure:: images/5.png

#. Fill out the following fields and click **Create**:

   - **Name of the Application** - POC-App-1
   - **db_password** - nutanix/4u

   *Lastly we'll fill in any runtime variables, in this case what we want to configure as the MySQL password, and a name for our deployed application. You can also see the estimated monthly cost for the VM based on the rates we configured when enabling Calm.*

#. Click **Create**.

#. Select the **Audit** tab to monitor the deployment of the Fiesta development environment. Complete provisioning of the app should take ~10 minutes (depending on whether or not you use the downloadable CentOS image and available bandwidth).

   .. figure:: images/6.png

#. While the application is provisioning, open :fa:`bars` **> Administration > Projects** and select your project.

#. Review the **Summary**, **Usage**, **VMs**, and **Users** tabs to see what type of data is made available to users. These breakouts make it easy to understand on a per project, vm, or user level, what resources are being consumed.

   .. figure:: images/7.png

   .. note::

      If desired, you can proceed with the :ref:`flowappsec` module while the application is still provisioning.

#. Return to **Calm > Applications > POC-App-1** and wait for the application to move from **Provisioning** to **Running**. Select the **Services** tab and select the **WebServer** Service to obtain the IP of the web tier.

   .. figure:: images/8.png

#. Open \http://<*WebServer-VM-IP*> in a new browser tab and validate the app is running.

   .. figure:: images/41.png

#. Log out of Prism Central and log back in as a Prism Administrator user.
