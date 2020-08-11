.. _power:

Complete Power Failure
======================

There are two example scenarios you can run to demonstrate the cluster resiliency during this event:

   - BASIC: Follow the instructions below.

   - RECOMMENDED: Use X-Ray to run Total Power Loss test (estimated time of completion: 2 hours). Setup of X-Ray is oulined in :ref:`xray`

.. note::

   You must run X-Ray on a different cluster than the cluster you are performing the power failure on.

In this section, we will be simulating a cluster failure by leveraging the IPMI (commonly referred to out-of-band or "lights out" management) to power off all nodes simultaneously, and observe the behavior of the cluster once the simulated power is restored.

#. Open a separate browser tab for each, and within each tab, login to the IPMI interface of each node. This will allow you to quickly and easily power off all nodes.

#. Click the **Power Down** button for each node.

   .. figure:: images/11.png

#. Demonstrate that all test VM(s) on the selected host are now powered off, as all hosts themselves are powered off.

#. After a few minutes, click the **Power On** button within the IPMI console for each node. Wait approximately 15-20 minutes.

#. SSH into any CVM, and run the ``cluster status`` command.

   .. figure:: images/12.png

      Sample output of the ``cluster status`` command

#. Wait for all services on all nodes in the cluster to be up before you attempt to log in to Prism.

#. Perform a short demo, ensure all VMs are now back up and running, and for a few VMs or services, show the customer that all is up and running without problems.
