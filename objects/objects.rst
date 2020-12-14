.. _objects:

-------
Objects
-------

**Prerequisites:** N/A

**Expected Module Duration:** Varies depending on number of updates performed

**Covered Test IDs:** N/A

What is Object Storage?
+++++++++++++++++++++++

Data is growing faster than ever before, and much of the new data generated every second is unstructured. Video, backups, images, and e-mail archives are all examples of unstructured data that can cause issues at scale using traditional file and block storage solutions.

Unlike file or block storage, object storage is a data storage architecture designed for unstructured data at the petabyte scale. Object storage manages data as objects, where each object contains the data itself, a variable amount of metadata, and a globally unique identifier. There is no filesystem overhead as there is in block and file storage, so it can be easily scaled out at a global level.

What is Nutanix Objects?
++++++++++++++++++++++++

Nutanix Objects is an S3-compatible object storage solution that leverages the underlying Nutanix storage fabric which allows it to benefit from features such as encryption, compression, and erasure coding (EC-X).

Objects allows users to store petabytes of unstructured data on the Nutanix platform, with support for features such as WORM (write once, read many) and object versioning that are required for regulatory compliance, and easy integration with 3rd party backup software and S3-compatible applications.

Use Cases for Nutanix Objects
+++++++++++++++++++++++++++++

Backup – You can integrate Objects with the backup applications such as Commvault, HYCU, Veeam, and Veritas. You can create backups to protect your data with a simple, scalable, and cost-effective active archive solution. You can start with small storage and scale to petabytes of storage to deliver great performance. Objects supports the multipart upload API with which you can reduce slow upload times by breaking data into chunks and upload documents, images, and videos to the global namespace.

Long-term Retention – You can use Objects for long-term data retention. Built-in object versioning provides storage protection and searches your data without the problem of tape systems. Versioning maintains previous copies of the object to avoid data loss from overwrites or deletes. Write-Once-Read-Many (WORM) buckets help you meet regulatory compliance requirements for healthcare, financial services, and government sectors by protecting your data from being overwritten or deleted.

DevOps – Easy application access to your data in a global namespace using simple PUT and GET commands makes Objects the perfect fit for your dev-ops data. Easily integrate REST API calls within your programs or scripts without tracking complex directory structures. DevOps and IT ops can use the S3-compatible interface for cross-geo, cross-team collaboration, and agile development.

Prerequisites
+++++++++++++

Below is a truncated list of prerequisites and requirements applicable to the POC use case. Please refer to the `Deployment Prerequisites - AHV and ESXi section of the Objects User Guide <https://portal.nutanix.com/page/documents/details?targetId=Objects-v3_0:v30-deployment-guidelines-r.html>`_ for full details.

General Requirements
....................

- Prism Element version 5.11.2 or later and Prism Central version 5.17.1 or later running in your environment.

- Recommended browser: Google Chrome

- One or two node deployments are for test or POC use only.

- Ensure that no upgrade is in progress while deploying Objects.

Network Requirements
....................

- Configure Domain Name Servers (DNS) on both Prism Element and Prism Central.

- Configure Network Time Protocol (NTP) servers on both Prism Element and Prism Central.

- Configure the virtual IP address and the data services IP address.

Deploying Objects
+++++++++++++++++

Enable Objects
..............

#. Log on to Prism Central, and click the :fa:`bars` **> Services > Objects**.

#. To enable the *Object Store Services*, click **Enable > Enable**.

After you enable Objects, ensure that you perform LCM inventory and upgrade the *MSP* (v2.0+) and *Objects Manager* (v3.0+) to the latest versions before you start with deployment. This will upgrade Objects to 3.0.

You can now use the Objects services.

Create Object Store
...................

#. Log on to Prism Central, and click the :fa:`bars` **> Services > Objects**.

#. Click **Create Object Store**.

#. In the Prerequisites window, click **Continue** if you fulfill the prerequisites.

#. On the *Name* page, fill out the following fields, and click **Next**:

   - **Object Store Name** store01
   - **Domain** ntnxlab.local

   .. figure:: images/1.png

#. On the *Configure* page, fill out the following fields, and click **Next**:

   - **Capacity** 1 TiB

   .. figure:: images/2.png

#. On the *Cluster Details* page, fill out the following fields, and click **Create**:

   - Select your cluster (ex. `PHX-POC069`) on which you want to deploy the object store.

   - **Internal Access Network** Primary

   - **Internal Access IPs** Choose two unallocated IP address (ex. .41, .42)

   - **Client Access Network** Primary

   - **Client Access IPs** Choose two unallocated IP address (ex. .43, .44, .45, .46)

   .. figure:: images/3.png

.. note::

   Deployment will take approximately 40 minutes, and will vary based on WAN speed. The deployment will sit at 5% for a long time while the software is downloaded from S3.

Create Bucket
.............

#. Log on to Prism Central, and click the :fa:`bars` **> Services > Objects**.

#. Click the name of object store in which you want to create a bucket (ex. `store01`). The object store opens in a new window.

#. Click **Create Bucket**.

#.
