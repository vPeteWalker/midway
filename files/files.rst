.. _files:

-----
Files
-----

Prerequisites
+++++++++++++

.. note::

   Your system must meet precheck requirements when creating a file server. If your system does not meet the requirements then the file server displays a pre check window in the Prism web console. When creating a file server, you must meet the following requirements. The precheck window displays the met and remaining requirements. Complete the Precheck information which includes the following:

- Latest Files Software

- Available Data Service IP Address

- Available Storage and Client Networks

Requirements
++++++++++++

- Configured and defined internal (storage) networks

- Configured and defined external (client side) networks

- Distributed File System (DFS) enabled (on by default for Windows clients)

- Network Time Protocol Server access

- Minimum of one (1) network (two (2) networks recommended)

- Minimum of 4 vCPUs (per host)

- Minimum of 12 GiB of memory (per host)

- Minimum domain administrator credentials access or user with delegated permissions

Network Requirements (Managed and Unmanaged)
++++++++++++++++++++++++++++++++++++++++++++

.. note::

   Files requires a set number of IP addresses for network configuration. To ensure you have the correct number of IP addresses, meet the following requirements for each network.

   For storage networks, be sure you have one more IP address than the number of FSVM nodes. For external networks, be sure you have the same number of IP addresses as the number of FS VM nodes. Consider the following examples, where N is the number of FS VM nodes.

- Storage Network: N+1
- Client Network: N

.. note::

   IP addresses do not need to be sequential.

Ensure that the external and storage networks use a tagged VLAN. This will block client access from the storage network. The external and storage networks must have separate subnets if the networks are not the same. If the same network is used for both clients and storage, then IP addresses must be unique.

Prerequisite Information
++++++++++++++++++++++++

.. list-table:: Credentials
  :widths: 25 75
  :header-rows: 0

  * - CIFS Server Name
    - The host name FQDN or the Files server DNS IP address
  * - Share Name
    - Name of the share the user wants to access

.. list-table:: AD and DNS
  :widths: 25 75
  :header-rows: 0

  * - Active Directory
    - Windows AD domain name
  * - AD Admin Account
    - Name of the share the user wants to access
  * - NTP Server
    - NTP Server name for the time synchronization between the file server and AD
  * - DNS Server Names
    - DNS servers names are used by Files to store entries to translate Files cluster or FSVM names to corresponding IP addresses
