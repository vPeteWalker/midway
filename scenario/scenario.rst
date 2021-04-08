.. _scenario:

-------------------------
POC Validation & Scenario
-------------------------

This POC Guide can be used as a tool for your manager, Enablement, or Portfolio Specialists to validate that you are capable of delivering a Basic POC in one or more product areas.

.. note::

   You may have already been validated for several products based on delivery of real world POCs. This guide is only meant to supplement the validation process.

The Process:

- You or your manager decide which products you should target for validation
- Your manager schedules a time for your mock customer meeting. Ideally these meetings should include multiple individuals capable of providing quality feedback.
- You reserve a HPOC cluster to complete the desired POC exercises. See :ref:`staging` for instruction on automatically staging the cluster *if you already have experience with basic AOS/AHV POCs.*
- When presenting, use the provided scenario (or substitute your own). As you go through this POC (Proof of Concept) Workshop you will use the Customer Scenario to provide some depth, context, and realism to the exercises that you do.

Your Goal:

- Foremost, demonstrate that you have completed the exercise by showing your HPOC environment in a post-exercise state (VMs configured, Policies created, etc.).
- Take into account each stakeholder's perspective, their pain points, and their goals and requirements.
- Communicate the specific Nutanix Product/Feature in your POC sections to each of those stakeholders and explain how Nutanix can solve their business challenges, can help them achieve their goals, and how Nutanix differentiates itself from the competition.
- Think of how you might do this with an actual meeting with a prospect/customer at the end of a POC.  How would you show them success for the POC and how it met their requirements and reduced or eliminated their pain?
- This should not be a PowerPoint presentation, but a demonstration through verbal communication and a walk through of what was accomplished in the UI.

Customer Scenario
+++++++++++++++++

The Customer is a retailer with multiple locations across the country.  They have several datacenters and also several remote branch offices that provide localized IT services. They have engaged with Nutanix because they are looking to consolidate a few of their data centers and are interested in the Nutanix HCI platform and software services that Nutanix offers.

Their VP of Infrastructure has communicated to their staff that as part of this consolidation they also wants to reduce the complexity of their current solution. There are too many silos, dependencies, and complicated engineered solutions which slow the business down. They want to be able to be flexible and nimble as they introduce new products to the market to beat their competition and gain an edge.  Security is also a huge concern for their team as they try to keep up with the pace of the business.  Several companies in their industry have been brought to their knees with Ransomware and other targeted attacks.  Because of this they want to increase their capabilities to recover from failures and need to ensure that there are no single points of failure and wants to be able to recover from individual failures to also entire datacenter site-wide failures.

The Enterprise Architect wants to ultimately have a solution that is flexible to changing needs from the business and gives them similar capabilities to a cloud provider, without the high cost and lock in to a specific vendor and their technology.  They're also deeply invested into VMware's software for virtualization, automation, network security, and monitoring. They've often mentioned the pain involved whenever they wish to move forward with a new feature from one of their VMware products on how long it takes them to upgrade all the other software components that feature relies on.  They mentioned that their storage is a mixture of NetApp, Dell/EMC, and Pure Storage.  They also leverage the NetApp as a Filer for file share data but their storage admin continually gripes about the lack of insight that they have into their NetApp Filer. The EA is frustrated with them since getting new storage from them can take weeks for new projects.

The Storage Admin is very concerned about performance, especially for their business critical database workloads, and is skeptical that a new solution can outperform their current storage arrays.  They're also very confident in their current storage arrays availability and reliability to date.  Though they have mentioned that they are very cautious and rarely updates the SANs or the FC Fabric due to previous experiences which ended up in outages and several day maintenance windows. They have said that typically it takes them about 1 week to deploy storage for a new project, assuming that there's available capacity, otherwise it takes 2-3 months to deploy a new storage array.
