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

DevOps

   - Single global namespace for multi-geography collaboration for teams spread around the world
   - S3 support
   - Time-to-first-byte of 10ms or less

Long Term Data Retention

   - WORM compliance
   - Object versioning
   - Lifecycle policies

Backup Target

   - Support for HYCU, Commvault, Veritas NetBackup, Rubrik
   - Ability to support multiple backup clients simultaneously
   - Ability to handle really small and really large backup files simultaneously with a key-value store based metadata structure and multi-part upload capabilities

Prerequisites and Cluster Preparation Guidance
++++++++++++++++++++++++++++++++++++++++++++++

Prerequisites
.............

- Prism Central and AOS version 5.11

- Prism Central has at least one AHV cluster registered to it.

- Data Services IP and Cluster Virtual IP are set on the AHV cluster.

- Prism Element and Prism Central are configured with DNS and NTP servers. (PC and PE Must have the same DNS servers configured)

- At least one network created in AHV with the following properties:
   - A VLAN with at least 18 available addresses (subnet mask of at least /27)
   - IP Address Management enabled (IPAM)
   - An IP pool of at least 12 IPs defined within this network
   - 2 static IP addresses outside of this pool (within the same network) to allocate to internal services that require static addresses
   - 4 or more static IP addresses outside of this pool (within this same network, or a separate network) to allocate as Client Access IPs

Deploy Objects
++++++++++++++
