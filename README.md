# GPU Partitioning with Hyper-V

## Overview
This guide provides instructions to create a Hyper-V VM for gaming using GPU Paravirtualization to "partition" a GPU. The configuration allows the VM to utilize approximately 50% of the GPU's performance, but this percentage can be adjusted based on your needs. The instructions are tailored for systems with an Nvidia GPU, though similar steps may apply for AMD GPUs.

## Prerequisites
- **OS**: Windows 10+ Pro edition
- **Hardware**:
  - Minimum 16GB RAM (32GB+ recommended)
  - Minimum 6-core CPU (8+ cores recommended)
  - GPU capable of handling desired games below 50% load
  - Sufficient disk space (~256GB for large games like Vermintide 2)
- **Additional**:
  - Windows ISO matching the host OS version

## Steps

### 1. Enable Virtualization
1. Enable virtualization in UEFI.
2. Search "Features" in Windows and open "Turn Windows features on or off."
3. Enable Hyper-V and reboot.

### 2. Configure Hyper-V
1. Open **Hyper-V Manager**.
2. Right-click the host name and open **Hyper-V Settings**.
3. Disable "Enhanced Session Mode Policy" and "Enhanced Session Mode."
4. Open **Virtual Network Manager**:
   - Create a new external switch, assign it to the current host network adapter, and name it (e.g., "External").

### 3. Create the Virtual Machine
1. Use the **New** button in Hyper-V Manager.
2. Configure the VM:
   - **Name**: Assign a name.
   - **Location**: Choose where to store VM configuration files.
   - **Generation**: Select Generation 2.
   - **Memory**: Allocate RAM and disable Dynamic Memory.
   - **Network**: Select the external network created earlier.
   - **Disk**: Create a virtual disk (VHD) and designate its size and location.
   - **OS**: Use an ISO file to install Windows.
3. Finish and **do not start the VM**.

### 4. Adjust VM Settings
1. Open **Settings** for the VM:
   - **Firmware**: Ensure the ISO file is booted before the HDD.
   - **Security**: Enable Secure Boot and TPM.
   - **Processor**: Assign CPU threads (e.g., 4 threads for a 6-core, 12-thread CPU).
   - **Integration Services**: Disable Backup and Guest Services.
   - **Checkpoints**: Disable checkpoints.
   - **Startup and Shutdown**: Set to "Do nothing" and "Shutdown."
2. Apply changes.

### 5. Install Windows on the VM
1. Start the VM and press a key to boot from the ISO.
2. Install Windows, skipping unnecessary steps like the Microsoft account requirement.
3. Update the system and configure the PC.

### 6. Install Required Software
1. Install the following on the VM:
   - **TightVNC**: `winget install GlavSoft.TightVNC`
   - **VB Cable**: [Download here](https://vb-audio.com/Cable/)
   - **Virtual-Display-Driver**: [Download from GitHub](https://github.com/itsmikethetech/Virtual-Display-Driver)
2. Configure TightVNC and reboot the VM.

### 7. Prepare GPU Drivers
1. Connect to the VM using TightVNC.
2. Disable the Hyper-V display adapter in Device Manager.
3. Shut down the VM.
4. Mount the VM VHD to the host.
5. Copy GPU driver files:
   - From host: `C:\Windows\System32\DriverStore\FileRepository\nv_dispi.inf_amd64...`
   - To VM: `C:\Windows\System32\HostDriverStore\FileRepository\`
   - Copy all `NV*` files from `C:\Windows\System32\` to the VM's same directory.
6. Unmount the VHD.

### 8. Configure GPU Partitioning
1. Open PowerShell ISE as administrator.
2. Set the execution policy:
   ```powershell
   Set-ExecutionPolicy unrestricted
   ```
3. Paste and adjust the following script, replacing `Your VM Name` with the VM's name:
   ```powershell
   $vm = "Your VM Name"
   Remove-VMGpuPartitionAdapter -VMName $vm
   Add-VMGpuPartitionAdapter -VMName $vm
   Set-VMGpuPartitionAdapter -VMName $vm -MinPartitionVRAM 1
   Set-VMGpuPartitionAdapter -VMName $vm -MaxPartitionVRAM 500000000
   Set-VMGpuPartitionAdapter -VMName $vm -OptimalPartitionVRAM 499999999
   Set-VMGpuPartitionAdapter -VMName $vm -MinPartitionEncode 1
   Set-VMGpuPartitionAdapter -VMName $vm -MaxPartitionEncode 500000000
   Set-VMGpuPartitionAdapter -VMName $vm -OptimalPartitionEncode 499999999
   Set-VMGpuPartitionAdapter -VMName $vm -MinPartitionDecode 1
   Set-VMGpuPartitionAdapter -VMName $vm -MaxPartitionDecode 500000000
   Set-VMGpuPartitionAdapter -VMName $vm -OptimalPartitionDecode 499999999
   Set-VMGpuPartitionAdapter -VMName $vm -MinPartitionCompute 1
   Set-VMGpuPartitionAdapter -VMName $vm -MaxPartitionCompute 500000000
   Set-VMGpuPartitionAdapter -VMName $vm -OptimalPartitionCompute 499999999
   Set-VM -GuestControlledCacheTypes $true -VMName $vm
   Set-VM -LowMemoryMappedIoSpace 1Gb -VMName $vm
   Set-VM -HighMemoryMappedIoSpace 32GB -VMName $vm
   ```
4. Run the script.
5. Start the VM and connect using TightVNC.

### 9. Finalize Configuration
1. Install the Virtual Display Driver.
2. Configure display settings in Windows.
3. Install Sunshine and configure audio using VB-Cable.
4. Pair Moonlight with Sunshine for streaming.

## Notes
1. Repeat GPU driver file copying after each host driver update.
2. Automate VM setup with scripts for convenience.
3. Prioritize host GPU usage for optimal performance.

---

This concludes the guide for GPU partitioning with Hyper-V. Enjoy gaming on your VM!
