.. _failure:

--------------------------------
Starting with simulating failure
--------------------------------

In this module you will be provided with multiple methods to test the redundancy and resiliency of the Nutanix platform.

**Pre-requisites:** Completion of :ref:`vmmanage`

**(Optional) Pre-requisite:** N/A

**Expected Module Duration:** 15 minutes - 11+ hours (varies depends on type and number of tests)

**Covered Test IDs:** `Core-015, Core-016, Core-017, Core-018 <https://confluence.eng.nutanix.com:8443/display/SEW/Official+Nutanix+POC+Guide+-+INTERNAL>`_

**FEEDBACK** :ref:`feedback`

Before you begin
++++++++++++++++

It is recommended you complete the :ref:`clusterconfig` section before proceeding.

Use your best judgment to decide when or even *if* to run these tests. For example, if this isn't a competitive situation, these tests may be unnecessary.

One of our key differentiators is resiliency; it's critical to highlight how a platform performs under duress. Hardware *will* fail. In competitive situations, demonstrating how Nutanix responds to these failures is sometimes crucial for someone evaluating multiple platforms to witness for themselves. For others, perhaps a customer reference will suffice. You be the judge based on your prospect's evaluation criteria.

Please be aware that any information such as server names, IP addresses, and similar information contained within any screen shots are strictly for demonstration purposes. Do not use these values when proceeding with any of the steps contained within this workshop.

This workshop was created with the following versions of Nutanix products. There may be differences, in both written steps and screen shots, between what is shown throughout this workshop, and what you experience if you are using later versions of the individual software packages listed below.

   - AOS             - 5.17.0.4
   - PC              - 5.17.0.3

Finally, while you are welcome to vary your inputs compared to the instructions listed below, please be aware that by diverting from these instructions, you may negatively impact your ability to successfully complete this workshop.
