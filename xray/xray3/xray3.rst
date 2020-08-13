.. _xray3:

Executing the OLTP Simulator
++++++++++++++++++++++++++++

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
