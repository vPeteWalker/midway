.. _prereqs:

--------------------------------
Pre-Requisites
---------------------------

Choosing Between HPOC and Physical
+++++++++++++++++++++++++++++++++++

When should you do hosted versus on-site? (Timing, customer data, integration with on-site workloads/products, etc.)
Lead time considerations (block availability in territory versus shipped from HQ versus finding HPOC availability) - one isn't always faster than the other
POC length - max 45 days on-site, max 14 days HPOC

HPOC Pre-requisites
+++++++++++++++++++++++++++++++++++

HPOC Reservation
POC Guide Disk Images
  - AutoAD
  - CentOS
  - Windows Server 2016

No other special network considerations (HPOC has 2x /25 VLANs which can be configured for IPAM)


On-premises Pre-requisites
+++++++++++++++++++++++++++++++++++

Hardware
4+ Nutanix nodes - Reference Confluence page for SFDC POC block instructions
Reference Physical Foundation page for additional power and network cabling considerations

Network
1 VLAN (also used for CVM/AHV network) - Ideally /24 or larger, no DHCP
Routable to internet

Active Directory
Recommended: AutoAD Disk Image for self-contained AD for POC

If using customer's existing AD, you will require:
 - Minimum AD Forest Level of 2008 R2 (need to confirm)
 - Admin credentials for PE/PC integration (need to define least priv access)
 - 4 Security Groups with 1+ user account each to map to Calm groups (Admin, Developer, Operator, Consumer)

POC Guide Disk Images
  - AutoAD
  - CentOS
  - Windows Server 2016
