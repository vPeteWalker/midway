.. _node:

Node Failure
++++++++++++

In this section, we will be simulating a node failure by leveraging the IPMI (commonly referred to out-of-band or "lights out" management) to power off the node unexpectedly, and observe the behavior of the cluster. In a 2+ node configuration, Nutanix can tolarate the unavailability of a single node - whether due to a failure, or scheduled maintenance.

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Create two VMs, one on the host that is running on the host you are shutting down, one on a host that will remain untouched. Begin a continuous ping between these VMs prior to issuing the shutdown command via IPMI, and observe that pings are lost once the host is powered off, and the VM resumes operation after the HA event. Creating VMs is oulined in :ref:`vmmanage`

   - RECOMMENDED: Use X-ray to run OLTP or VDI workload. Setup of X-Ray is oulined in :ref:`xray`. Instructions for running the OLTP Simulator can be found in :ref:`xray3`.

   - HIGHLY RECOMMENDED: Use X-Ray to run Extended Node Failure test (estimated time of completion: varies, up to 11 hours). Utilizing this method will generate data on the cluster, is easily repeatable, supports multiple platforms, creates visual data, and effectively does everything else in the exercise automatically. If you choose this method, you may skip the exercise below.

.. note::

   If you choose to run X-Ray on the same cluster you are performing failures on, you must avoid simulating the node failure on the same host that is running X-Ray.

#. Login to the IPMI interface of the selected node to participate in the simulated node failure test.

#. Click the **Power Down** button.

   .. figure:: images/11.png

#. Observe that the test VM(s) on the selected host are now powered off, and a High Availability (HA) event has occurred. The cluster will automatically attempt to restart the VM(s) on the remaining hosts.

#. Log into Prism.

#. From the dropdown, select **Hardware**. Click on the selected host. Observe that the host is offline.

#. From the dropdown, select **VM**. Monitor the VM(s) that were previously running on the test host, now will boot up on the remaining hosts. This process should take approximately 3-5 minutes between the time you power off the host, and the VM(s) being up and running once again.

#. Return to the IPMI interface for the selected node, and click **Power On**. This will take approximately 10-15 minutes to boot up, and be available within the cluster.

#. Perform a short demo, ensure all VMs are now back up and running, and for a few VMs or services, show the customer that all is up and running without problems. Additionally, demonstrate the result of either the **BASIC** or **RECOMMENDED** scenarios.
