# Build Guide: Setting up Test Virtual Machines

This guide describes the step-by-step process of setting up two virtual machines (Linux Ubuntu Server and Windows 11) using a hypervisor. It is designed so that a beginner can repeat the procedure independently.

## Prerequisites
1. Download and install a hypervisor (VirtualBox or VMware Workstation Pro).
2. Download official ISO images for Ubuntu Server and Windows 11.
3. Check the host machine storage and RAM to ensure sufficient free resources.

## Step 1: Creating the Linux (Ubuntu Server) Virtual Machine
1. Open the hypervisor application and select the option to create a new virtual machine.
2. Enter a descriptive name (for example, ubuntu-test-vm) and select the downloaded Ubuntu Server ISO file.
3. Allocate hardware resources: set RAM to 4 GB (4096 MB) to prevent host memory overcommit, and set the virtual hard disk size to 30 GB.
4. Configure the network adapter to use **NAT** mode instead of bridged mode to bypass local academic network MAC address filtering.
5. Finish the creation wizard, power on the virtual machine, and complete the standard Ubuntu installation steps.

## Step 2: Creating the Windows 11 Virtual Machine
1. In the hypervisor, create another new virtual machine for the Windows guest.
2. Select the Windows 11 ISO installation image.
3. Allocate resources: set RAM to 4 GB and virtual hard disk size to 40 GB to accommodate system files and updates.
4. Set the network adapter to **NAT** mode, matching the configuration used for the Linux guest.
5. Finish the wizard, start the virtual machine, and complete the Windows installation.

## Step 3: Network and Verification
1. Power on both virtual machines at the same time.
2. Open the terminal on the Linux guest and check the network status.
3. Test communication between the guests using the ping command. If Windows blocks ICMP requests, adjust the Windows Defender firewall settings on the Windows guest to allow echo requests.
4. Take a clean-state snapshot of each virtual machine in the hypervisor to preserve the working baseline.