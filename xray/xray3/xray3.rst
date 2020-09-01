.. _xray3:

Executing the OLTP Simulator
++++++++++++++++++++++++++++

.. note::

   The OLTP simulator is the ideal method if you are running X-Ray tests with the intention of demonstrating how Nutanix (and multiple platforms, in a competitive situation) react to a :ref:`failure` scenario while a simulated a real-world workload is in progress, as it mirrors what many customers have in their environment (vs. VDI, which isn't typically encountered as often). That said, it is always recommended to understand the client's environment prior to choosing a test, as you'll want to run an X-Ray test that most closely matches their workloads (i.e. make the test more "real" to them). Before you proceed, you should consider how long a particular simulated failure operation should take, and adjust the runtime of the selected X-Ray test accordingly. It is recommend to overestimate the test duration, as you can always stop the X-Ray test at any point once your failure testing has concluded. For example, you can safely select *Custom* and change the runtime to 7200 seconds (2 hours) for the *OLTP Simulator* test, and have confidence that 2 hours should suffice for any one particular failure scenario outlined in this workshop. Another option may be to choose to run it for 43,200 seconds (12 hours), as perhaps you wish to run through all of the simulated failure scenarios, and then stop the OLTP Simulator test once your simulated failure testing has concluded.

The X-Ray test scenarios offer predefined test cases that consist of multiple events and predefined parameters. X-Ray executes scenarios against test targets to produce results for analysis. X-Ray scenarios simulate real-world workloads on test targets. Effective virtualized data center solutions delegate resources so that workloads do not monopolize resources from other workloads. Running different workloads in this manner helps evaluate how multiple workloads interact with one another.

X-Ray uses the open-source Flexible I/O (FIO) benchmark tool to generate an I/O workload. FIO files define the characteristics of the FIO workload. Each FIO file contains defined parameters and job descriptions involved in the file.

The test scenarios simulate Online Transaction Processing (OLTP), Virtual Desktop Infrastructure (VDI), and Decision Support System (DSS) workloads. For this exercise, we will be running the OLTP Simulator.

To view detailed information about each test scenario, click **View & Run Test** within the *Tests* dashboard to display the details of the selected test.

#. Within the *Application Performance* section, click **View & Run Test** under *OLTP Simulator*.

#. In the *Choose test target* dropdown, choose your cluster.

   .. figure:: images/16.png

#. Review the test requirements in the left pane before proceeding. Modify the entries within *Choose the test variant*. Once finished, click **Run Test**.

#. You will be presented with the following message. Click **View** within it, if you wish to view the test in progress.

   .. figure:: images/17.png

#. Otherwise, click **Results** and then click anywhere within the test entry itself to open the *Results* page for your test.

   .. figure:: images/19.png
      :align: left

   .. figure:: images/18.png
      :align: right

#. For other options, select the check box next to the test and click one of the option buttons.

   - For the raw data, click **Export Raw Results**.

   - To have X-Ray return a report with a description, summary tests results, and high level information about each target in the test, click **Generate Report**.

   .. figure:: images/20.png
