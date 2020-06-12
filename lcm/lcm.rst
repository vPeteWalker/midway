.. _lcm:

--------------------------
Lifecycle Manager (LCM)
-----------------------

What is LCM?
++++++++++++

- LCM  aims to enable administrators to take inventory of the firmware and software in their Nutanix environment and perform updates in a simple, cluster-aware, fashion.
- LCM is an industry-unique solution - providing the same 1-click operational upgrade simplicity for 7 (and growing) different hardware vendor appliances running Nutanix software.
- LCM just offers a management layer that will allow users to deploy firmware upgrades simply without having to worry about upgrade paths or conflicts between versions. Customers should not require a manual to upgrade their environment - LCM aims to solve this at scale.
- Once a customer performs an Inventory of their cluster, all the firmware is downloaded and staged. From there, users can deploy the updates with as much or as little granularity as they desire. You can either select individual entities for upgrade or do the entire cluster at once (subject to what the actual component being upgraded allows).
- A Dark Site LCM version is available

Recommended Upgrade Order
+++++++++++++++++++++++++

- Upgrade and Run Latest NCC Checks. Fix any issues prior to conducting upgrades.
- Upgrade AOS to the latest available version in your selected release train (STS or LTS).  Some AOS/LCM interface code is improved in later AOS versions.
- Upgrade Foundation to the latest “1-click” available version. Latest Phoenix is bundled with newer CVM Foundation versions.
- Run “Perform Inventory” in LCM and upgrade LCM to the latest version; then re-run “Perform Inventory”. Applicable if “Auto LCM Framework Update” is not selected.
- Check minimum Hypervisor versions required for firmware updates before updating firmware.

   - Prism Central (PC): Upgrade and run NCC on Prism Central.
   - PC: Upgrade Prism Central.
   - PC: Run NCC.
   - Prism Element clusters (PE): Upgrade and run NCC.
   - PE: Upgrade Foundation.
   - PE: Run and upgrade Life Cycle Manager (LCM):
      - Perform an LCM inventory (also updates LCM framework). Do not upgrade any other software component except LCM in this step.
   - PE: Upgrade AOS.
   - PE: Run and upgrade Life Cycle Manager (LCM):
      - Perform an LCM inventory (also updates LCM framework).
      - Upgrade SATA DOM firmware (for hardware using SATA DOMs) as recommended by LCM.
      - Upgrade all other firmware as recommended by LCM (BIOS / BMC / other).
   - PE: Upgrade AHV for AHV clusters.
   - PE: Upgrade cluster hypervisor hosts other than AHV.
   - PE: Run NCC.

      .. figure:: images/1.png
      :align: left
      :scale: 40%
         Nutanix Upgrade Order 1 of 2


      .. figure:: images/2.png
      :align: right
      :scale: 40%
         Nutanix Upgrade Order 2 of 2
