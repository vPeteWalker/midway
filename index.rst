.. title:: Nutanix POC Guide

.. toctree::
   :maxdepth: 2
   :caption: Planning & Deployment
   :name: _info
   :hidden:

   prereqs/prereqs
   hpoc/hpoc
   foundation/foundation

.. toctree::
   :maxdepth: 2
   :caption: Initial Configuration
   :name: _info
   :hidden:

   clusterconfig/clusterconfig
   pcconfig/pcconfig
   vmmanage/vmmanage

.. toctree::
   :maxdepth: 2
   :caption: AOS/PC
   :name: _info
   :hidden:

   node_addremove/node_addremove
   aos_upgrade/aos_upgrade
   lcm/lcm
   pcapimanage/pcapimanage
   climanage/climanage

.. toctree::
   :maxdepth: 2
   :caption: Flow
   :name: _flow
   :hidden:

   flow/flowbasic/flowbasic
   flow/flowappsec/flowappsec
   calm/deploygraylog/deploygraylog

.. toctree::
   :maxdepth: 2
   :caption: Calm
   :name: _calm
   :hidden:

   calm/calmenable/calmenable
   calm/singlevmwin/singlevmwin
   calm/deploypocapp/deploypocapp

.. toctree::
   :maxdepth: 2
   :caption: Files
   :name: _files1
   :hidden:

   files/files1
   files/files2
   files/files3

.. toctree::
   :maxdepth: 2
   :caption: X-Ray
   :name: _xray
   :hidden:

   xray/xray

.. toctree::
   :maxdepth: 2
   :caption: Failure
   :name: _failure
   :hidden:

   failure/failure

==========================
NUTANIX SE POC GUIDE (AHV)
==========================

What Is This?
+++++++++++++

The purpose of this guide is to provide detailed guidance to Nutanix or partner SEs on the delivery of a Proof of Concept either on-premises or using a Hosted POC cluster. It includes planning considerations, talking points, and step-by-step instructions for cluster deployment, configuration, and operation. **It is NOT the only way to deliver a Nutanix POC**. It simply serves as guidance for you to learn what good looks like, and provides a foundation upon which to build and modify in your own POCs.

This guide can also be used in conjunction with the standard `Nutanix POC Test Plan Tracker <https://docs.google.com/spreadsheets/d/15r8Q1kCIJY4ErwL1CaHHwv4Q7gmCbLOz5IaR51t9se0/edit#gid=0>`_ spreadsheet.

Every successful POC has a well-defined test plan. While this guide will want you through common uses and scenarios for Nutanix products, many POCs will also include workload or application specific requirements. When working with the customer to add these additional items to your test plan, keep in mind that: **Test items should identify success criteria (faster, simpler, provides new and required functionality, etc.) which are ideally measurable (less time to complete, less steps to configure), but may also be verification that a feature functions as expected (true/false).**

When Should I Do A POC?
+++++++++++++++++++++++

**FEEDBACK** - could use some bullets here. Also want to steer folks to Bootcamps/Test Drive for the appropriate use cases. Repeat guidance about

How Do I Use This Guide?
++++++++++++++++++++++++

No two POCs are the same, and as such, much of this guide is meant to be used modularly:

- To begin, review the **Planning & Deployment** section prior to your POC.
- The **Initial Configuration** section covers exercises that are common to all AHV POCs, and should be completed in order.
- Following that, additional module can be completed in almost any order. If applicable, specific pre-requisites will be noted at the beginning of each module.
- Each module provides an approximate **Duration** to complete. Take these into account when planning time to spend in front of the customer working through test cases.

How Do I Get Support?
+++++++++++++++++++++

**FEEDBACK** - Any process recommendations here?

If you're experiencing product issues during a POC, you can engage Nutanix Support directly by creating a case at https://portal.nutanix.com or calling 1-855-NUTANIX, Option 3. The full list of international support phone numbers can be found `here <https://www.nutanix.com/support-services/product-support/support-phone-numbers>`_.

For non-blocking issues, you can also try to engage Support in #sre in Slack.

Feedback
++++++++

We already have multiple additions planned for this guide, including more advanced Calm exercises, Era, X-Play, and Leap. If you have additional feedback, questions, or ideas, please send them to te-amer@nutanix.com.
